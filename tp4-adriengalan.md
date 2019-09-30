ID ETUDIANT : 11364

### TP 4 - Utilisateurs, groupes et permissions

## Exercice 1

**• Commencez par créer deux groupes groupe1 et groupe2**
```sudo addgroup groupe1
addgroup groupe2
```
**• Créez ensuite 4 utilisateurs u1, u2, u3, u4 avec la commande useradd, en demandant la création de
leur dossier personnel et avec bash pour shell**

```
useradd u1 -m --shell /bin/bash
useradd u2 -m --shell /bin/bash
useradd u3 -m --shell /bin/bash
useradd u4 -m --shell /bin/bash
```

**• Placez les utilisateurs dans les groupes :
- u1, u2, u4 dans groupe1
- u2, u3, u4 dans groupe2 **

```
sudo usermod -a -G groupe2 u1
sudo usermod -a -G groupe2 u2
sudo usermod -a -G groupe2 u4
sudo usermod -a -G groupe2 u2
sudo usermod -a -G groupe2 u3
sudo usermod -a -G groupe2 u4
```


**• Donnez deux moyens d’afficher les membres de groupe2**

```
cat /etc/group | grep "groupe2"
```
sudo apt install members
members groupe2
```

**• Faites de groupe1 le groupe propriétaire de /home/u1 et /home/u2 et de groupe2 le groupe propriétaire
de /home/u3 et /home/u4**

```
chown :groupe1 /home/u1
chown :groupe1 /home/u2
chown :groupe2 /home/u3
chown :groupe2 /home/u4
```

**• Remplacez le groupe primaire des utilisateurs :
- groupe1 pour u1 et u2
- groupe2 pour u3 et u4**

```
sudo usermod u1 -g groupe1
sudo usermod u2 -g groupe1
sudo usermod u3 -g groupe2
sudo usermod u4 -g groupe2
```

**• Créez deux répertoires /home/groupe1 et /home/groupe2 pour le contenu commun aux groupes, et
mettez en place les permissions permettant aux membres de chaque groupe d’écrire dans le dossier
associé.**

```
mkdir /home/group1
mkdir /home/group2
chgrp group1 /home/group1
chgrp group2 /home/group2
```

**• Comment faire pour que, dans ces dossiers, seul le propriétaire d’un fichier ait le droit de renommer
ou supprimer ce fichier ?**

```
chmod /home/group1 740
chmod /home/group2 740
```

**• Pouvez-vous vous connecter en tant que u1 ? Pourquoi ?**

Non on ne peut pas, puisqu'il n'y a pas encore de mot de passe et que le compte n'est pas activé.


**• Activez le compte de l’utilisateur u1 et vérifiez que vous pouvez désormais vous connecter avec son
compte.**

```
sudo passwd u1
su u1
```

**• Quels sont l’uid et le gid de u1 ?**

```
id u1
```
on obtient uid = 1001
et gid = 1003

**• Quel utilisateur a pour uid 1003 ?**

```
getent passwd "1003" | cut -d: -f1
```

**• Quel est l’id du groupe groupe1 ?**
```
getent group groupe1 | cut -d: -f3
```

**• Quel groupe a pour guid 1002 ? (Rien n’empêche d’avoir un groupe dont le nom serait 1002...)**

```
getent group "1002" | cut -d: -f1
```

**• Retirez l’utilisateur u3 du groupe groupe2. Que se passe-t-il ? Expliquez.**

```
sudo gpasswd -d u3 groupe2
```
On ne peut pas car il s'agit du groupe primaire de u3.

**• Modifiez le compte de u4 de sorte que :
— il expire au 1er juin 2019
— il faut changer de mot de passe avant 90 jours
— il faut attendre 5 jours pour modifier un mot de passe
— l’utilisateur est averti 14 jours avant l’expiration de son mot de passe
— le compte sera bloqué 30 jours après expiration du mot de passe**

```
sudo chage --expiredate 1559347200 u4
sudo chage -M 90 u4
sudo chage -m 5 u4
sudo chage -W 14 u4
sudo chage -I 30 u4
```

**• Quel est l’interpréteur de commandes (Shell) de l’utilisateur root ?**

```
cat /etc/passwd | grep "root" | cut -d: -f 7
```

**• à quoi correspond l’utilisateur nobody ?**

Nobody (personne en anglais) est le nom conventionnel d'un compte d'utilisateur à qui aucun fichier n'appartient, qui n'est dans aucun groupe qui a 
des privilèges et dont les seules possibilités sont celles que tous les "autres utilisateurs" ont.

**• Par défaut, combien de temps la commande sudo conserve-t-elle votre mot de passe en mémoire ?
Quelle commande permet de forcer sudo à oublier votre mot de passe ?

La commande sudo conserve le mot de passe durant 15 minutes (info attrapée au vol donnée par l'enseignant à un autre élève :D)
Pour forcer sudo à oublier le mot de passe : 
```
sudo -K
```


##Exercice 2. Gestion des permissions

**• Dans votre $HOME, créez un dossier test, et dans ce dossier un fichier fichier contenant quelques
lignes de texte. Quels sont les droits sur test et fichier ?**

```
mkdir test
touch test/fichier
stat -c %a test/fichier
```
On trouve 664 pour les droits.

**• Retirez tous les droits sur ce fichier (même pour vous), puis essayez de le modifier et de l’afficher en
tant que root. Conclusion ?**

```
chmod test/fichier 000
sudo cat test/fichier
```
ça fonctionne, même en enlevant tous les droits, le root peut encore avoir accès au fichier.


**• Redonnez vous les droits en écriture et exécution sur fichier puis exécutez la commande echo "echo
Hello" > fichier. On a vu lors des TP précédents que cette commande remplace le contenu d’un
fichier s’il existe déjà. Que peut-on dire au sujet des droits ?**

```
sudo chmod 600 test/fichier
echo "echo Hello" > fichier
```

Les droits sont revenus, j'imagine.. Non je sais pas.

**• Essayez d’exécuter le fichier. Est-ce que cela fonctionne ? Et avec sudo ? Expliquez.**


**• Placez-vous dans le répertoire test, et retirez-vous le droit en lecture pour ce répertoire. Listez le
contenu du répertoire, puis exécutez ou affichez le contenu du fichier fichier. Qu’en déduisez-vous ?
Rétablissez le droit en lecture sur test**


**• Créez dans test un fichier nouveau ainsi qu’un répertoire sstest. Retirez au fichier nouveau et au
répertoire test le droit en écriture. Tentez de modifier le fichier nouveau. Rétablissez ensuite le droit
en écriture au répertoire test. Tentez de modifier le fichier nouveau, puis de le supprimer. 
Que pouvez vous déduire de toutes ces manipulations ?**


**• Positionnez vous dans votre répertoire personnel, puis retirez le droit en exécution du répertoire test.
Tentez de créer, supprimer, ou modifier un fichier dans le répertoire test, de vous y déplacer, d’en
lister le contenu, etc…Qu’en déduisez vous quant au sens du droit en exécution pour les répertoires ?**


**• Rétablissez le droit en exécution du répertoire test. Positionnez vous dans ce répertoire et retirez lui
à nouveau le droit d’exécution. Essayez de créer, supprimer et modifier un fichier dans le répertoire
test, de vous déplacer dans ssrep, de lister son contenu. Qu’en concluez-vous quant à l’influence des
droits que l’on possède sur le répertoire courant ? Peut-on retourner dans le répertoire parent avec ”cd
..” ? Pouvez-vous donner une explication ?**


**• Rétablissez le droit en exécution du répertoire test. Attribuez au fichier fichier les droits suffisants
pour qu’une autre personne de votre groupe puisse y accéder en lecture, mais pas en écriture.**


**• Définissez un umask très restrictif qui interdit à quiconque à part vous l’accès en lecture ou en écriture,
ainsi que la traversée de vos répertoires. Testez sur un nouveau fichier et un nouveau répertoire.**

**• Définissez un umask très permissif qui autorise tout le monde à lire vos fichiers et traverser vos répertoires, mais n’autorise que vous à écrire. Testez sur un nouveau fichier et un nouveau répertoire.**


**• Définissez un umask équilibré qui vous autorise un accès complet et autorise un accès en lecture aux
membres de votre groupe. Testez sur un nouveau fichier et un nouveau répertoire.**


**• Transcrivez les commandes suivantes de la notation classique à la notation octale ou vice-versa (vous
pourrez vous aider de la commande stat pour valider vos réponses) :
- chmod u=rx,g=wx,o=r fic
- chmod uo+w,g-rx fic en sachant que les droits initiaux de fic sont r--r-x---
- chmod 653 fic en sachant que les droits initiaux de fic sont 711
- chmod u+x,g=w,o-r fic en sachant que les droits initiaux de fic sont r--r-x---**


**• Affichez les droits sur le programme passwd. Que remarquez-vous ? En affichant les droits du fichier
/etc/passwd, pouvez-vous justifier les permissions sur le programme passwd ?**

**• Access Control Lists (ACL) : suivez le tutoriel de cette page : https://doc.ubuntu-fr.org/acl.**
**• Quotas disques : suivez le tutoriel de cette page : https://doc.ubuntu-fr.org/quota.**
