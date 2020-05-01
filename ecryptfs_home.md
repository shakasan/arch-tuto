# Répertoire home chiffré

> **!! Cette section nécessite des ajustements !!**

Cette étape est optionnelle mais fortement recommandée, surtout sur un laptop.

Cela permet de chiffrer votre répertoire `home` et donc de rendre vos données inaccessibles en cas de vol ou de perte.

Ce répertoire contient vos données, mais également des mots de passes, des clés ssh, gpg, ... etc etc

Bref, fortement conseillé ^^

> **Remarque** : ne **PAS** re-démarrer avant la fin de la procédure !!

> **Astuce** : vous pouvez alterner entre des consoles différentes via `CTRL+ALT+(F2-F6)` et revenir à la partie graphique via `CTRL+ALT+F7`



## La procédure

On va redémarrer l'ordi et ne surtout **PAS** se logguer avec le `user` dont on va chiffrer le `home`.

Donc à l'écran de connexion, faite un `CTRL+ALT+F2` et logguez-vous en `root`.

On install le paquet nécessaire pour migrer le répertoire `home`

```shell
trizen -S ecryptfs-utils rsync lsof
```

On charge le module noyau nécessaire

```shell
modprobe ecryptfs
```

On lance le script de migration (ça peut prendre beaucoup de temps en fonction du contenu présent dans `home`)

```shell
ecryptfs-migrate-home -u shakasan
```

Si tout s'est bien déroulé, on switch de console, on se logue avec son user et on monte puis démonte son home chiffrée pour vérifier que tout est ok.

```shell
ecryptfs-mount-private
ecryptfs-umount-private
```

On va maintenant demander à PAM de monter le répertoire `home` chiffré lors du login

Pour cela, on va éditer le `fichier /etc/pam.d/system-auth`

Et rajouter ces 3 lignes (attention, l'ordre à de l'importance):

- `auth required pam_ecryptfs.so unwrap`
- `password optional pam_ecryptfs.so`
- `session optional pam_ecryptfs.so unwrap`

Ce qui devrais donner quelquechose comme ceci :

```shell
#%PAM-1.0

auth      required  pam_unix.so     try_first_pass nullok
auth      required  pam_ecryptfs.so     unwrap
auth      optional  pam_permit.so
auth      required  pam_env.so

account   required  pam_unix.so
account   optional  pam_permit.so
account   required  pam_time.so

password  optional  pam_ecryptfs.so
password  required  pam_unix.so     try_first_pass nullok sha512 shadow
password  optional  pam_permit.so

session   required  pam_limits.so
session   required  pam_unix.so
session   optional  pam_ecryptfs.so     unwrap
session   optional  pam_permit.so
```

Vous vous loguez avec le `user` au `home` fraichement chiffré avec un `CTRL+ALT+F7` puis via l'écran de connexion.

Une fois loggué et sur le bureau, ouvrez un terminal et paramétrez la `passphrase`

```shell
ecryptfs-unwrap-passphrase
ecryptfs-add-passphrase
ecryptfs-rewrap-passphrase /home/shakasan/.ecryptfs/wrapped-passphrase
```

Tout devrait fonctionner maintenant, vous pouvez re-démarrer ^^

**Mais en cas de problème** : votre ancien `home` est toujours présent.

Comme ceci :

```shell
# nouveau home chiffré
/home/shakasan
# ancien home
/home/shakasan.e8Sc2LAe
```

Si vous voulez rétablir la situation intiale sans chiffrage, vous devez simplement vous logguez en `root` via `CTRL+ALT+F2`, puis remplacer les répertoires :

```shell
cd /home
rm -rf shakasan
mv shakasan.e8Sc2LAe shakasan
reboot
```