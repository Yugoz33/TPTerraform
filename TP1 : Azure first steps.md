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
ssh -i C:\Users\antoi\.ssh\cloud_tp1 azureuser@4.178.136.116
The authenticity of host '4.178.136.116 (4.178.136.116)' can't be established.
ED25519 key fingerprint is SHA256:eNa4cU5aWvRZjxtykb7CI6OusUDQ6B3JAvZElOT1Eb8.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '4.178.136.116' (ED25519) to the list of known hosts.
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.15.0-1089-azure x86_64)

...

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@cloudTP1:~$
```


2. az : a programmatic approach

🌞 Créez une VM depuis le Azure CLI

🌞 Assurez-vous que vous pouvez vous connecter à la VM en SSH sur son IP publique

🌞 Une fois connecté, prouvez la présence...

3. Terraforming planets infrastructures

🌞 Utilisez Terraform pour créer une VM dans Azure

🌞 Prouvez avec une connexion SSH sur l'IP publique que la VM est up

4. Exemple d'utilisation Azure + Terraform












