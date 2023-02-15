D'abord copié sa clé SSH :

![clé_ssh](https://github.com/CodePit84/Installation-nuage/blob/main/cle_ssh.jpg)

Créer une instance sur nuage (ne pas tenir compte du nom de l'instance dans les screensshot suivants car il y a eu un bug à la création de la 1ère instance)

Nom :

Système : Debian

![création_nuage](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2013-50-07.png)

Attention bien mettre "Associer une IP et ouvrir l'accès SSH" au niveau de l'IP publique !

Et coller votre clé SSH en cliquant sur "Importer une clé" (lui donné un nom ou laissé l'email par defaut)

Et créer l'instance !

Assurer vous que l'instance soit en "vert"

![instance](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2013-57-24.png)

Puis Aller dans "Groupes de sécurité" (en bas) et cliquer sur "Assigner des instances" de "Autoriser tout le trafic depuis Internet"

![instance](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2013-58-07.png)

Sur votre ordinateur, vous devez lié votre clé SSH à votre instance.

Dans votre terminal, au niveau de votre dossier .ssh
Tapez : 
```nano config```

![nano_config](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-15%2010-27-34.png)

Editez le fichier `~/.ssh/config pour lui ajouter ceci :

```bash
# On remplace les xxx par l'adresse ip de votre machine virtuelle
Host xxx.xxx.xxx.xxx
  AddKeysToAgent yes
  # UseKeychain no
  # On remplace maclé par le nom de votre clé privée
  IdentityFile ~/.ssh/maclé

```

![config](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-14%2014-20-04.png)

donc votre ip publique après Host et vous commenté UseKeychain no !

Une fois votre Machine Virtuelle crée, vous pouvez y accéder en faisant la commande `ssh debian@xxx.xxx.xxx.xxx` les xxx étant l'adresse ip publique de votre machine virtuelle.

Donc sur votre terminal, vous tapez :
```ssh debian@xxx.xxx.xxx.xxx```
remplacer les xxx par votre ip publique !

![ssh_debian](https://github.com/CodePit84/Installation-nuage/blob/main/Capture%20d%E2%80%99%C3%A9cran%20du%202023-02-15%2010-35-49.png)
Répondre yes à la question "Are you sure you want to continue connecting (yes/no/[fingerprint])?"

Désormais pour vous connecter utiliser la commande ```ssh debian@xxx.xxx.xxx.xxx``` pour vous connectez au terminal de votre serveur distant.




