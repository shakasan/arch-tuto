# Installation de base

Cette étape s'effectue depuis le shell de la clé USB d'installation

## Clavier

Configurer le clavier en Azerty BE

```shell
loadkeys be-latin1
```

## Partitions / systèmes de fichier

Partionner le disque, avec un /boot séparé (à adapter selon vos besoins ^^)

```shell
cfdisk /dev/sda
```

Schéma de partition

part | size | mountpoint | filesys | boot flag
:-:|:-:|:-:|:-:|:-:
/dev/sda1 | 521M | /boot | ext4 | oui
/dev/sda2 | 1G | | swap | non
/dev/sda3 | reste | / | ext4 | non

Créer les systèmes de fichier

```shell
mkfs.ext4 /dev/sda1
mkfs.ext4 /dev/sda3
```

Et le swap

```shell
mkswap /dev/sda2
swapon /dev/sda2
```

On monte le tout

```shell
mount /dev/sda3 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

## Configuration de base de Pacman

On configure le gestionnaire de paquet pacman pour utiliser un mirroir belge pour les dépots

```shell
nano /etc/pacman.d/mirrorlist
```

Et on garde

```shell
Server = http://archlinux.cu.be/$repo/os/$arch
```

## Installation des paquets de base

```shell
pacstrap /mnt base base-devel pacman-contrib zip unzip p7zip vim mc alsa-utils syslog-ng mtools dosfstools lsb-release ntfs-3g exfat-utils
```

## Création du fichier /etc/fstab

On génère un fichier `fstab` dans `/etc/fstab` afin que nos partitions soient montées au boot correctement

```shell
genfstab -U -p /mnt >> /mnt/etc/fstab
```

## Installer Grub + OS prober

On install le bootloader GRUB ainsi qu'OS Prober pour ajouter automatiquement les OS présents sur d'autres disques au menu de démarrage

```shell
pacstrap /mnt grub os-prober
```

## chroot dans son nouvel OS

On "entre" dans son nouvel OS via la commande `chroot`

```shell
arch-chroot /mnt
```

## Config clavier et langues

Dans `/etc/vconsole.conf`, on ajoute :

```shell
KEYMAP=be-latin1
FONT=lat9w-16
```

Dans `/etc/locale.conf`, on ajoute :

```shell
LANG=fr_BE.UTF-8
```

Dans `/etc/locale.gen`, on décommente

```shell
en_US.UTF-8 UTF-8
fr_BE.UTF-8 UTF-8
```

On génère le tout

```shell
locale-gen
```

Et on exporte le tout pour la session courante

```shell
export LANG=fr_BE.UTF-8
```

## Config date, heure, fuseau horaire

On configure le fuseau horaire pour Bruxelles

```shell
ln -sf /usr/share/zoneinfo/Europe/Brussels /etc/localtime
```

## Configurer GRUB

On génère la config de `GRUB` et on l'install

```shell
mkinitcpio -p linux
grub-install --no-floppy --recheck /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

## Password root

On modifie le mot de passe `root`

```shell
passwd
```

## Réseau

On install networkmanager

```shell
pacman -Syy networkmanager gnome-keyring
systemctl enable NetworkManager
```

## Ajout de dépôts additionnels dans Pacman

**Remarque** : c'est le même principe que pour n'importe quel dépot de n'importe quelle distro, il faut **impérativement** n'ajouter que des dépots dans lesquels on a **confiance**. Et c'est idem ++ pour les paquets AUR.

Dans `/etc/pacman.conf`

On ajoute le support du 32bits en décommentant :

```shell
[multilib]
Include = /etc/pacman.d/mirrorlist
```

Et on ajoute les dépôts suivants :

```shell
[arcanisrepo]
Server = https://repo.arcanis.me/repo/$arch

[archstrike]
Server = https://mirror.archstrike.org/$arch/$repo

[herecura]
Server = https://repo.herecura.be/$repo/$arch

[sergej-repo]
Server = http://repo.p5n.pp.ru/$repo/os/$arch

[archlinuxfr]
SigLevel = Never
Server = https://repo.archlinux.fr/$arch
```

On met les DBs des paquets à jour

```shell
pacman -Syy
```

On importe la clé de Archstrike

```shell
pacman-key --init
dirmngr < /dev/null
wget https://archstrike.org/keyfile.asc
pacman-key --add keyfile.asc
pacman-key --lsign-key 9D5F1C051D146843CDA4858BDE64825E7CBC0D51
```

On install les paquets suivants (procédure spcifique à Archstrike)

```shell
pacman -S archstrike-keyring
pacman -S archstrike-mirrorlist
```

Et toujours dans `/etc/pacman.conf`, on remplace :

```shell
[archstrike]
Server = https://mirror.archstrike.org/$arch/$repo
```

Par :

```shell
[archstrike]
Include = /etc/pacman.d/archstrike-mirrorlist
```

Et on met les DBs des paquets à jours à nouveau

```shell
pacman -Syy
```

## Redémarrer

La première étape est terminée, on quite le chroot, on démonte les partitions et on reboot.

```shell
exit
umount -R /mnt
reboot
```
