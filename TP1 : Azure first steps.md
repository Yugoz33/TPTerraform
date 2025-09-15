I. Prérequis
1. Starting blocks
2. Une paire de clés SSH

🌞 Déterminer quel algorithme de chiffrement utiliser pour vos clés

- vous n'utiliserez PAS RSA
- donner une source fiable qui explique pourquoi on évite RSA désormais (pour les connexions SSH notamment) : https://www.schneier.com/blog/archives/2023/11/new-ssh-vulnerability.html
- donner une source fiable qui recommande un autre algorithme de chiffrement (pour les connexions SSH notamment) : https://www.brandonchecketts.com/archives/ssh-ed25519-key-best-practices-for-2025

🌞 Générer une paire de clés pour ce TP

- la clé privée doit s'appeler cloud_tp1
- elle doit se situer dans le dossier standard pour votre utilisateur
- elle doit utiliser l'algorithme que vous avez choisi à l'étape précédente (donc, pas de RSA)
- elle est protégée par un mot de passe de votre choix

```
$ ssh-keygen -t ed25519 -C "tpleo"
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\hugoc/.ssh/id_ed25519): cloud_tp1
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in cloud_tp1
Your public key has been saved in cloud_tp1.pub
The key fingerprint is:
SHA256:9***********************************9Q8 tpleo
The key's randomart image is:
+--[ED25519 256]--+
|   ************   |
|   ************   |
|   *************  |
|    ************  |
|    ************  |
|     *********    |
|     ********     |
|     *******      |
|     ******       |
+----[SHA256]-----+
```

🌞 Configurer un agent SSH sur votre poste

- détaillez-moi toute la conf ici que vous aurez fait

```  
$ Start-Service ssh-agent
$ ssh-add C:\Users\hugoc\.ssh\cloud_tp1
Enter passphrase for C:\Users\hugoc\.ssh\cloud_tp1:
Identity added: C:\Users\hugoc\.ssh\cloud_tp1 (tpleo)
```
II. Spawn des VMs

1. Depuis la WebUI

🌞 Connectez-vous en SSH à la VM pour preuve


- cette connexion ne doit demander aucun password : votre clé a été ajoutée à votre Agent SSH

```
>>>>>>> cdc11f89c4d87c9198dd89e9c02f84be9ae34657
ssh -i C:\Users\hugoc\.ssh\cloud_tp1 azureuser@4.180.136.110
The authenticity of host '4.180.136.110 (4.180.136.110)' can't be established.
ED25519 key fingerprint is SHA256:eNa4cU5aWvRZjxtykb7CI6OusUDQ6B3JAvZElOT1Eb8.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '4.180.136.110' (ED25519) to the list of known hosts.
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.15.0-1089-azure x86_64)

...

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@cloudTP1:~$
```


2. az : a programmatic approach

🌞 Créez une VM depuis le Azure CLI

```
>>>>>>> cdc11f89c4d87c9198dd89e9c02f84be9ae34657
$ az group create --location francecentral --name tp1leo
{
  "id": "/subscriptions/142f1e87-3a7***********c6610/resourceGroups/tp1leo",
  "location": "francecentral",
  "managedBy": null,
  "name": "tp1leo",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}

$ az vm create -g tp1leo -n cli_1 --image Ubuntu2204 --admin-username antna --ssh-key-values C:\Users\hugoc\.ssh\cloud_tp1.pub --size Standard_B1ls
The default value of '--size' will be changed to 'Standard_D2s_v5' from 'Standard_DS1_v2' in a future release.
Selecting "northeurope" may reduce your costs. The region you've selected may cost more for the same services. You can disable this message in the future with the command "az config set core.display_region_identified=false". Learn more at https://go.microsoft.com/fwlink/?linkid=222571

{
  "fqdns": "",
  "id": "/subscriptions/142f1e87-3a7c-42d***********6610/resourceGroups/tp1leo/providers/Microsoft.Compute/virtualMachines/cli_1",
  "location": "francecentral",
  "macAddress": "60-45-BD-6D-AF-18",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "**.**.**.**",
  "resourceGroup": "tp1leo"
}
```


🌞 Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique

```
$ ssh -i C:\Users\hugoc\.ssh\cloud_tp1 hugoc@**.**.**.**
```
```
ssh -i C:\Users\hugoc\.ssh\cloud_tp1 hugoc@**.**.**.**
>>>>>>> cdc11f89c4d87c9198dd89e9c02f84be9ae34657
The authenticity of host '**.**.**.** (**.**.**.**)' can't be established.
ED25519 key fingerprint is SHA256:bBbibCEUGVfn5UnbN2xjXAJ/REl5OQum2AxXannZCjg.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '**.**.**.**' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1031-azure x86_64)

...

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

hugoc@cli1:~$
```

🌞 Une fois connecté, prouvez la présence...
- ...du service `walinuxagent.service`
```
hugoc@cli1:~$ systemctl status walinuxagent.service
Warning: The unit file, source configuration file or drop-ins of walinuxagent.service changed on disk. Run 'systemctl d>
● walinuxagent.service - Azure Linux Agent
     Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
    Drop-In: /run/systemd/system.control/walinuxagent.service.d
             └─50-CPUAccounting.conf, 50-MemoryAccounting.conf
     Active: active (running) since Fri 2025-09-05 11:56:51 UTC; 6min ago
   Main PID: 727 (python3)
      Tasks: 7 (limit: 405)
     Memory: 42.0M
        CPU: 2.586s
     CGroup: /system.slice/walinuxagent.service
             ├─ 727 /usr/bin/python3 -u /usr/sbin/waagent -daemon
             └─1019 python3 -u bin/WALinuxAgent-2.14.0.1-py3.12.egg -run-exthandlers

Sep 05 11:57:02 cli1 python3[1019]:        0        0 ACCEPT     tcp  --  *      *       0.0.0.0/0            168.63.12>
Sep 05 11:57:02 cli1 python3[1019]:        0        0 ACCEPT     tcp  --  *      *       0.0.0.0/0            168.63.12>
Sep 05 11:57:02 cli1 python3[1019]:        0        0 DROP       tcp  --  *      *       0.0.0.0/0            168.63.12>
Sep 05 11:57:03 cli1 python3[1019]: 2025-09-05T11:57:03.848355Z INFO ExtHandler ExtHandler Looking for existing remote 
```

- ...du service `cloud-init.service`

```
hugoc@cli1:~$ systemctl status cloud-init.service
● cloud-init.service - Cloud-init: Network Stage
     Loaded: loaded (/lib/systemd/system/cloud-init.service; enabled; vendor preset: enabled)
     Active: active (exited) since Fri 2025-09-05 11:56:51 UTC; 9min ago
   Main PID: 482 (code=exited, status=0/SUCCESS)
        CPU: 1.129s

Sep 05 11:56:50 cli1 cloud-init[486]: |   ...   +.=oB=Bo|
Sep 05 11:56:50 cli1 cloud-init[486]: |    . o .EB=*o+O*|
Sep 05 11:56:50 cli1 cloud-init[486]: |     + + oo+o ++o|
Sep 05 11:56:50 cli1 cloud-init[486]: |      + S    .   |
Sep 05 11:56:50 cli1 cloud-init[486]: |     . o         |
Sep 05 11:56:50 cli1 cloud-init[486]: |                 |
Sep 05 11:56:50 cli1 cloud-init[486]: |                 |
Sep 05 11:56:50 cli1 cloud-init[486]: |                 |
Sep 05 11:56:50 cli1 cloud-init[486]: +----[SHA256]-----+
Sep 05 11:56:51 cli1 systemd[1]: Finished Cloud-init: Network Stage.

```


3. Terraforming planets infrastructures

🌞 Utilisez Terraform pour créer une VM dans Azure

🌞 Prouvez avec une connexion SSH sur l'IP publique que la VM est up

4. Exemple d'utilisation Azure + Terraform












