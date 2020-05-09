# Installation de base (suite)

Après redémarrage, nous voilà sur notre 1ère ébauche d'OS fraichement installé

## Réseau - suite

Si le réseau n'est pas up (ethernet ici dans mon cas)

```shell
systemctl start NetworkManager.service
```

Et si tout est ok, on l'active automatiquement au boot

```shell
systemctl enable NetworkManager.service
```

## Ajout d'un utilisateur

On ajoute un compte utilisateur + mot de passe

```shell
useradd -m -g wheel -c 'Francois B (Makoto)' -s /bin/bash shakasan
passwd shakasan
```

## Configurer sudo

Pour pouvoir exécuter des commandes avec les droits admin depuis son utilisateur fraichement créé, on doit configurer `sudo` pour qu'il accepte les membres du groupe `wheel`.

Dans `/etc/sudoers`, on décommente cette ligne

```shell
%wheel ALL=(ALL) ALL
```

## On se logue avec le nouvel utilisateur

On quite l'utilisateur actuel

```shell
exit
```

Et on se logue avec le nouveau.

A partir de maintenant, on utilisera `sudo` à la place du compte `root` (prenons les bonnes habitudes)

## Yaourt ou Trizen

On install `yaourt` ou `trizen` pour épauler `pacman` avec les 'paquets' AUR.

Ce sont en fait des wrapper pour `pacman` qui ajoutent des fonctions en plus, comme la possibilité d'installer des 'paquets' depuis AUR.

> **Remarque 1** : utilisez de préférence `trizen` qui est plus sécurisé

> **Remarque 2** : et n'oubliez pas de lire les scripts d'installation à chaque fois, même lors de mises à jours !

Pour `yaourt`

```shell
sudo pacman -S yaourt
```

Pour `trizen`

```shell
sudo pacman -S git
cd /tmp
git clone https://aur.archlinux.org/trizen.git
cd trizen
makepkg -si
```

A partir de maintenant, je vais utiliser `trizen` pour tout (wrapper `pacman`)

## Sources du Kernel

On install le package contenant les sources du kernel indispensable pour pouvoir compiler des modules addtionnels avec DKMS (Virtualbox, ....)

```shell
trizen -S linux-headers
```

## NTP, cron, syslog

On install

```shell
trizen -S ntp cronie
```

Et dans `/etc/systemd/journald.conf`, on remplace :

```shell
#ForwardToSyslog=no
```

Par :

```shell
ForwardToSyslog=yes
```

## Son avec ALSA

Configurer les entrées/sorties, volumes, ...

```shell
alsamixer
```

On quite quand c'est ok, et on sauvegarde la config

```shell
sudo alsactl store
```

## Gstreamer

On install `gstreamer` + plugins

```shell
trizen -S gst-plugins-{base,good,bad,ugly} gst-libav
```

## Xorg

On install le serveur graphique

```shell
trizen -S xorg-{server,xinit,apps} xf86-input-libinput xdg-user-dirs
```

> **Remarque** : xf86-input-{mouse,keyboard} have been removed

### Les pilotes graphiques

#### Intel

```shell
trizen -S xf86-video-intel
```

#### Radeon

```shell
trizen -S xf86-video-ati
```

> **Remarque** : selon GPU AMD/ATI, il faut choisir entre xf86-video-ati ou xf86-video-amdgpu

## Polices de caractères

```shell
trizen -S ttf-{bitstream-vera,liberation,dejavu} freetype2 ttf-ms-fonts gnu-free-fonts
```

Et pour le japonais ;)

```shell
trizen -S noto-fonts-cjk noto-fonts-emoji noto-fonts
```

## Impression

```shell
trizen -S cups hplip python-pyqt5 foomatic-{db,db-ppds,db-gutenprint-ppds,db-nonfree,db-nonfree-ppds} gutenprint
```

## Configurer le clavier

On configure le clavier pour `Xorg`

```shell
sudo localectl set-x11-keymap be
```

## Bluetooth

On ajoute le support du bluetooth

```shell
trizen -S bluez bluez-tools bluez-plugins bluez-utils blueman
```

## Services à démarrer au boot

On active dans `systemd` les services suivants pour qu'ils démarrent automatiquement au boot

```shell
sudo systemctl enable syslog-ng@default
sudo systemctl enable cronie
sudo systemctl enable avahi-daemon
sudo systemctl enable avahi-dnsconfd
sudo systemctl enable org.cups.cupsd
sudo systemctl enable bluetooth
sudo systemctl enable ntpd
```

## Gestionnaire de connexion lightdm

On install le gestionnaire de connexion pour pouvoir s'authentifier graphiquement

```shell
trizen -S lightdm lightdm-{gtk-greeter,gtk-greeter-settings} accountsservice
```

## MATE

On install l'envirronement de bureau `MATE`

```shell
trizen -S gvfs-{afc,goa,google,gphoto2,mtp,nfs,smb} mtpfs
```

Puis

```shell
trizen -S mate mate-{extra,applet-dock,applet-streamer,applets,backgrounds,calc,common,control-center,desktop,icon-theme,icon-theme-faenza,media,menu,menus,netbook,notification-daemon,panel,polkit,power-manager,screensaver,sensors-applet,session-manager,settings-daemon,system-monitor,terminal,themes,user-guide,user-share,utils} mozo pluma engrampa atril gnome-icon-theme python2-pyinotify ffmpegthumbnailer pulseaudio pulseaudio-{alsa,bluetooth} libcanberra-{pulse,gstreamer} system-config-printer
```

`MATE Tweak`, pour avoir accès à certains réglages depuis le Panneau de configuration

```shell
trizen -S mate-tweak
```

Et un petit hack pour avoir accès aux screensavers de gnome sous MATE (`glmatrix` par exemple)

```shell
trizen -S mate-screensaver-hacks
```

## Gestionnaire de connexion

On lance le gestionnaire de connexion `lightdm`

```shell
sudo systemctl start accounts-daemon
sudo systemctl start lightdm
```

Et si tout va bien, une fois dans l'environnement de bureau `MATE`, on l'active via le `Terminal MATE`

```shell
sudo systemctl enable accounts-daemon
sudo systemctl enable lightdm
```
