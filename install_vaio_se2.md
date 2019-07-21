![](logo_med.png)

# Archlinux Vaio

Documentation personnelle sur l'installation de Archlinux sur mon laptop Vaio, basée sur le tuto de Frederic Bezies, que je remercie au passage

---

## Etape 1 - CD/Clé d'install

Cette étape s'effectue depuis le shell de la clé USB d'installation

### Clavier

Configurer le clavier en Azerty BE

```shell
loadkeys be-latin1
```

### Partitions / systèmes de fichier

Partionner le disque, avec un /boot séparé.
A adapter selon vos besoins ;)

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

### Configuration de base de Pacman

On configure le gestionnaire de paquet pacman pour utiliser un mirroir belge pour les dépots

```shell
nano /etc/pacman.d/mirrorlist
```

Et on garde

```shell
Server = http://archlinux.cu.be/$repo/os/$arch
```

### Installation des paquets de base

```shell
pacstrap /mnt base base-devel pacman-contrib zip unzip p7zip vim mc alsa-utils syslog-ng mtools dosfstools lsb-release ntfs-3g exfat-utils
```

### Création du fichier /etc/fstab

```shell
genfstab -U -p /mnt >> /mnt/etc/fstab
```

### Installer Grub + OS prober

```shell
pacstrap /mnt grub os-prober
```

### chroot dans son nouvel OS

```shell
arch-chroot /mnt
```

### Config clavier et langues

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

### Config date, heure, fuseau horaire

On configure le fuseau horaire pour Bruxelles

```shell
ln -sf /usr/share/zoneinfo/Europe/Brussels /etc/localtime
```

### Configurer grub

On génère la config de grub et on l'install

```shell
mkinitcpio -p linux
grub-install --no-floppy --recheck /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### Password root

On modifie le mot de passe root

```shell
passwd
```

### Réseau

On install networkmanager

```shell
pacman -Syy networkmanager gnome-keyring
systemctl enable NetworkManager
```

### Ajout de dépôts additionnels dans Pacman

**Remarque** : c'est le même principe que pour n'importe quel dépot de n'importe quelle distro, il faut **impérativement** n'ajouter que des dépots dans lesquels on a **confiance**. Et c'est idem pour les paquets AUR.

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

[sublime-text]
Server = https://download.sublimetext.com/arch/stable/x86_64

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

### Redémarrer

La première étape est terminée, on quite le chroot, on démonte les partitions et on reboot

```shell
exit
umount -R /mnt
reboot
```

---

## Etape 2 - Suite installation de base

Après redémarrage, nous voilà sur notre 1ère ébauche d'OS fraichement installé

### Réseau - suite

Si le réseau n'est pas up (ethernet ici dans mon cas)

```shell
systemctl start NetworkManager.service
```

Et si tout est ok

```shell
systemctl enable NetworkManager.service
```


### Ajout d'un utilisateur

On ajoute un compte utilisateur + mot de passe

```shell
useradd -m -g wheel -c 'Francois B (Makoto)' -s /bin/bash shakasan
passwd shakasan
```

### Configurer sudo

Pour pouvoir exécuter des commandes avec les droits admin depuis son user fraichement créé, on doit configurer sudo pour qu'il accepte les membres du groupe `wheel`.

Dans `/etc/sudoers`, on décommente cette ligne

```shell
%wheel ALL=(ALL) ALL
```

### On se logue avec le nouvel utilisateur

On quite le user actuelle

```shell
exit
```

Et on se logue avec le nouveau.

A partir de maintenant, on utilisera sudo à la place du compte root (prenons les bonnes habitudes)

### Yaourt ou Trizen

On install yaourt ou trizen pour épauler pacman avec les 'paquets' AUR.

Ce sont en fait des wrapper pour pacman qui ajoutent des fonctions en plus, comme la possibilité d'installer des 'paquets' depuis AUR.

**Remarque 1** : utilisez de préférence trizen qui est plus sécurisé
**Remarque 2** : et n'oubliez pas de lire les scripts d'installation à chaque fois, même lors de mises à jours !

Pour yaourt

```shell
sudo pacman -S yaourt
```

Pour trizen

```shell
sudo pacman -S git
git clone https://aur.archlinux.org/trizen.git
cd trizen
makepkg -si
```

A partir de maintenant, je vais utiliser trizen pour tout (wrapper pacman)

### Sources du Kernel

On install le package contenant les sources du kernel indispensable pour pouvoir compiler des modules addtionnels avec DKMS (Virtualbox, ....)

```shell
trizen -S linux-headers
```

### NTP, cron, syslog

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

### Son avec ALSA

Configurer les entrées/sorties, volumes, ...

```shell
alsamixer
```

On quite quand c'est ok, et on sauvegarde la config

```shell
sudo alsactl store
```

### Gstreamer

On install gstreamer + plugins

```shell
trizen -S gst-plugins-{base,good,bad,ugly} gst-libav
```

### Xorg

On install le serveur graphique

```shell
trizen -S xorg-{server,xinit,apps} xf86-input-{mouse,keyboard,libinput} xdg-user-dirs
```

Et les pilotes graphiques (intel dans mon cas)

```shell
trizen -S xf86-video-intel
```

### Polices de caractères

```shell
trizen -S ttf-{bitstream-vera,liberation,dejavu} freetype2 ttf-ms-fonts gnu-free-fonts
```

Et pour le japonais ;)

```shell
trizen -S noto-fonts-cjk noto-fonts-emoji noto-fonts
```

### Impression

```shell
trizen -S cups hplip python-pyqt5 foomatic-{db,db-ppds,db-gutenprint-ppds,db-nonfree,db-nonfree-ppds} gutenprint
```

### Configurer le clavier

On configure le clavier pour Xorg

```shell
sudo localectl set-x11-keymap be
```

### Bluetooth

On ajoute le support du bluetooth

```shell
trizen -S bluez bluez-tools bluez-plugins bluez-utils blueman
```

### SSD

On va activer le TRIM pour les disques SSD

```shell
sudo systemctl enable fstrim.timer
```

### Swappiness

Si l'on dispose de suffisament de RAM, on peut limiter l'usage du swap et donc ménager un peu le SSD

Dans `/etc/sysctl.d/99-sysctl.conf`, on ajoute :

```shell
vm.swappiness=10
```

### Services à démarrer au boot

On active dans systemd les services suivants pour qu'ils démarrent automatiquement au boot

```shell
sudo systemctl enable syslog-ng@default
sudo systemctl enable cronie
sudo systemctl enable avahi-daemon
sudo systemctl enable avahi-dnsconfd
sudo systemctl enable org.cups.cupsd
sudo systemctl enable bluetooth
sudo systemctl enable ntpd
```

### Gestionnaire de connexion lightdm

On install le gestionnaire de connexion

```shell
trizen -S lightdm lightdm-{gtk-greeter,gtk-greeter-settings} accountsservice
```

### MATE

On install l'envirronement de bureau MATE

```shell
trizen -S gvfs-{afc,goa,google,gphoto2,mtp,nfs,smb} mtpfs
```

Puis

```shell
trizen -S mate mate-{extra,applet-dock,applet-streamer,applets,backgrounds,calc,common,control-center,desktop,icon-theme,icon-theme-faenza,media,menu,menus,netbook,notification-daemon,panel,polkit,power-manager,screensaver,sensors-applet,session-manager,settings-daemon,system-monitor,terminal,themes,user-guide,user-share,utils} mozo pluma engrampa atril gnome-icon-theme python2-pyinotify ffmpegthumbnailer pulseaudio pulseaudio-{alsa,bluetooth} libcanberra-{pulse,gstreamer} system-config-printer
```

MATE Tweak, pour avoir accès à certains réglages depuis le Panneau de configuration

```shell
trizen -S mate-tweak
```

Et un petit hack pour avoir accès aux screensavers de gnome sous MATE (glmatrix par exemple)

```shell
trizen -S mate-screensaver-hacks
```

### Gestionnaire de connexion

On lance le gestionnaire de connexion lightdm

```shell
sudo systemctl start accounts-daemon
sudo systemctl start lightdm
```

Et si tout va bien, une fois dans l'environnement de bureau MATE, on l'active via le Terminal MATE

```shell
sudo systemctl enable accounts-daemon
sudo systemctl enable lightdm
```

---

## Etape 3 - Répertoire home chiffré

Cette étape est optionnelle mais fortement recommandée, surtout sur un laptop.

Cela permet de chiffrer votre répertoire `home` et donc de rendre vos données inaccessibles en cas de vol ou de perte.

Ce répertoire contient vos données, mais également des mots de passes, des clés ssh, gpg, ... etc etc

Bref, fortement conseillé ^^

**Remarque** : ne **PAS** re-démarrer avant la fin de la procédure !!

**Astuce** : vous pouvez alterner entre des consoles différentes via `CTRL+ALT+(F2-F6)` et revenir à la partie graphique via `CTRL+ALT+F7`

### La procédure

On va redémarrer l'ordi et ne surtout **PAS** se logguer avec le `user` dont on va chiffrer le `home`.

Donc à l'écran de connexion, faite un `CTRL+ALT+F2` et logguez-vous en `root`.

On install le paquet nécessaire pour migrer le répertoire `home`

```shell
trizen -S ecryptfs-utils
```

On charge le module noyau nécessaire

```shell
modprobe ecryptfs
```

On lance le script de migration (ça peut prendre beaucoup de temps en fonction du contenu présent dans `home`)

```shell
ecryptfs-migrate-home -u shakasan
```

Si tout s'est bien déroulé, on monte puis démonte son home chiffrée pour vérifier que tout est ok.

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

---

## Etape 4 - Installation des Apps

L'installation de base étant terminée, passons à l'ajout des applications.

Pour les package managers pip3, npm, gem, veuillez vous référer à la partie sur les outils de développement pour leur éventuelle installation.

**Astuce** : si vous voulez des infos sur un paquet en particulier, en résumé `trizen -Ss <nom_du_paquet>` et en détaillé `trizen -Si <nom_du_paquet>`.

### Utilitaires

```shell
trizen -S detox network-manager-applet qt5ct qt5-styleplugins clamav clamtk grub-customizer tmsu deja-dup acetoneiso2 alltray wget curl aria2 htop bmon mc screen bleachbit bash-completion xcalib qtqr copyq woeusb etcher libgksu gksu multibootusb-git spectre-meltdown-checker dialog dosbox fcrackzip fdupes figlet flatpak gawk gksu gnome-disk-utility gnome-font-viewer gparted gsmartcontrol gtk-engine-murrine guake idle3-tools inxi less lsb-release mupdf mupdf-tools numlockx pdfcrack pdfgrep procps-ng psensor pv rarcrack screenfetch snapd synapse terminator tilda unrar zip princexml cryptkeeper pandoc s-tui stress linpack nano-syntax-highlighting-git kdocker mesa-demos odt2txt
```

```shell
sudo npm i -g coinmon
```

```shell
sudo -H pip3 install tldr cheat md2pdf
```

### Multimédia

```shell
trizen -S quodlibet vlc mpv xsane shotwell simplescreenrecorder asunder audacious audacious-plugins audacity avidemux-cli avidemux-qt blender cheese cuetools darktable banshee-git gmusicbrowser radiotray-ng sayonara-player-git vidcutter entangle peek indicator-sound-switcher gyazo shotcut flacon gpick gpicview-gtk3 python2-pydbus-git green-recorder guvcview handbrake handbrake-cli hugin inkscape kodi krita krita-plugin-gmic milkytracker mkvtoolnix-cli mkvtoolnix-gui moc openshot picard pitivi rawtherapee sayonara-player-git screenkey shntool smplayer smplayer-{skins,themes} soundconverter spotify xnviewmp
```

### Rhythmbox + plugins

```shell
trizen -S rhythmbox rhythmbox-{llyrics,plugin-alternative-toolbar-git,plugin-hide-git,plugin-radio-browser-git,plugin-tray-icon-git}
```

### Gimp + plugins

```shell
trizen -S gimp gimp-{help-fr,refocus,nufraw,plugin-fblur,plugin-gmic,plugin-lqr,plugin-wavelet-denoise,plugin-resynthesizer,plugin-refocusit,plugin-pandora,plugin-layers-to-divs,plugin-export-layers,lensfun,brushbox}
```

### eBook

```shell
trizen -S calibre sigil fbreader
```

### Graveur CD/DVD/BR

```shell
trizen -S brasero k3b xfburn
```

### Breautique

```shell
trizen -S libreoffice-still libreoffice-still-fr dia scribus marp
```

### Web, internet

```shell
trizen -S firefox firefox-i18n-fr chromium franz-bin slack-desktop thunderbird thunderbird-i18n-fr nextcloud-client insync corebird delux digikam discord-canary filezilla geary google-chrome 
hexchat icedtea-web links mumble teamspeak3 mypaint opera opera-ffmpeg-codecs pidgin pidgin-{libnotify,otr} purple-{facebook,plugin-pack,skypeweb} quiterss skypeforlinux-bin syncthing 
syncthing-gtk telegram-{desktop,qt,purple} transmission-gtk vivaldi vivaldi-ffmpeg-codecs whalebird-bin tootle birdtray megasync
```

### Réseau

```shell
trizen -S aircrack-ng ngrep networkmanager-openvpn dsniff wireshark-gtk wireshark-cli iftop iptraf-ng nethogs whois keychain x11-ssh-askpass nmap bind-tools bluez create_ap
```

#TODO : whatismyip ?

```shell
sudo -H pip3 install speedtest-cli
```

### Plugins Caja

```shell
trizen -S caja-{open-terminal,image-converter,sendto,share,wallpaper,xattr-tags,extensions-common}
```

### Nautilus + plugins

```shell
trizen -S nautilus nautilus-{image-converter,sendto,share}
```

### Thunar + plugins

```shell
trizen -S thunar thunar-{archive-plugin,media-tags-plugin,volman}
```

#TODO: fix

### Wine

```shell
trizen -S wine winetricks playonlinux
```

### CAD

```shell
trizen -S kicad kicad-library kicad-library-3d librecad
```

### jeux

```shell
trizen -S joyutils
```

### Unbound (cache DNS)

On install

```shell
trizen -S unbound
```

Et on active le service au boot

```shell
sudo systemctl start unbound.service
sudo systemctl enable unbound.service
```

### Gadgets

```shell
trizen -S no-more-secrets-git
```

### Perso

```shell
trizen -S wpfind
```

### Apps lié au matos

#### Logitech Unifying

Solaar permet de configurer les périphériques Logitech compatible Unifying.

```shell
trizen -S solaar
```

#### Lecteur de carte

Le nécessaire pour utiliser les cartes à puces (carde d'identité, ...)

```shell
trizen -S pcsc-tools
```

#### Ledger Nano S

On install la nouvelle application de gestion/config du wallet hardware Ledger Nano S.

```shell
trizen -S ledger-live-bin
```

## Etape 5 - Configuration

### Mise à jours du Microcode du CPU

Cette étape est optionnelle, mais fortement recommandée (failles spectre, meltdown, ...)

On install le nécessaire (CPU Intel)

```shell
trizen -S iucode-tool intel-ucode
```

Et on met à jour la configuration de Grub pour qu'il en tienne compte

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

### Firewall

On install les outis graphique/cli

```shell
trizen -S ufw gufw
```

Et on active le firewall au boot

```shell
sudo ufw enable
```

Et si vous utilisez syncthing, on ouvre les ports pour celui-ci

```shell
sudo ufw allow syncthing
```

### .bashrc

Dans `~/.bashrc`, on ajoute :

Un theme plus sympa pour mocp

```shell
alias mocp='mocp -T "darkdot_theme"'
```

Des couleurs et un affichage plus complet de la commande `ll`

```shell
alias ll='ls -lah --color'
```

On affiche `screenfetch` à l'ouverture d'un terminal

```shell
screenfetch -t
```

On configure `nano` comme éditeur cli par défaut

```shell
export EDITOR=nano
```

### Terminal par défaut

Nécessaire pour certaines applications comme rclonebrowser par exemple

Dans `/etc/environment`, on ajoute

```shell
TERMINAL=/usr/bin/mate-terminal
```

### Numlock ON avec lightdm au boot

Rien de plus agaçant que de ne pas avoir la touche numlock activée lorsqu'on arrive sur l'écran de connexion.

Dans `/etc/lightdm/lightdm.conf`, on ajoute la ligne suivante

```shell
[Seat:*]
greeter-setup-script=/usr/bin/numlockx on
```

### Partages SMB/CIFS dans /etc/fstab

Exemple de configuration dans fstab pour monter automatique au boot un dossier partagé sur son NAS avec smb/cifs

```shell
//<VOTRE_ADRESSE_IP>/<NOM_DU_DOSSIER_PARTAGE>     /mnt/<NOM_DU_POINT_DE_MONTAGE_LOCAL>          cifs   vers=3.0,x-systemd.automount,x-systemd.idle-timeout=1min,_netdev,credentials=/etc/smbcreds,rw,iocharset=utf8,uid=1000,gid=1000 0 0
```

**Remarque** : le uid et le gid sont a adapter si besoin à ceux de votre utilisateur

Et on regénère les règles systemd pour le nouveau point de montage smb/cifs

```shell
sudo systemctl daemon-reload
sudo systemctl restart remote-fs.target
sudo systemctl restart local-fs.target
```

### Environnement QT5

Pour donner la même apparences aux applications écrites avec QT5 et GTK, on va utiliser qt5ct comme intermédiaire. Celui-ci s'ajoute dans le Panneau de configuration

Dans `/etc/environment`, on ajoute

```shell
QT_QPA_PLATFORMTHEME=qt5ct
```

### Udev

#### Périphs Android

Afin de pouvoir utiliser les outils du SDK Android, comme `adb` ou `fastboot`, on doit donner un accès au smartphone via USB.

Manuellement, on créer le fichier `/etc/udev/rules.d/99-android.rules` et on ajoute

```shell
SUBSYSTEM=="usb", ATTR{idVendor}=="0502", MODE="0666", OWNER="shakasan" # Acer
SUBSYSTEM=="usb", ATTR{idVendor}=="0b05", MODE="0666", OWNER="shakasan" # Asus
SUBSYSTEM=="usb", ATTR{idVendor}=="413c", MODE="0666", OWNER="shakasan" # Dell
SUBSYSTEM=="usb", ATTR{idVendor}=="0489", MODE="0666", OWNER="shakasan" # Foxconn
SUBSYSTEM=="usb", ATTR{idVendor}=="04c5", MODE="0666", OWNER="shakasan" # Fujitsu
SUBSYSTEM=="usb", ATTR{idVendor}=="04c5", MODE="0666", OWNER="shakasan" # Fujitsu-Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="091e", MODE="0666", OWNER="shakasan" # Garmin-Asus
SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", OWNER="shakasan" # Google-Nexus
SUBSYSTEM=="usb", ATTR{idVendor}=="201E", MODE="0666", OWNER="shakasan" # Haier
SUBSYSTEM=="usb", ATTR{idVendor}=="109b", MODE="0666", OWNER="shakasan" # Hisense
SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", MODE="0666", OWNER="shakasan" # HTC
SUBSYSTEM=="usb", ATTR{idVendor}=="12d1", MODE="0666", OWNER="shakasan" # Huawei
SUBSYSTEM=="usb", ATTR{idVendor}=="8087", MODE="0666", OWNER="shakasan" # Intel
SUBSYSTEM=="usb", ATTR{idVendor}=="24e3", MODE="0666", OWNER="shakasan" # K-Touch
SUBSYSTEM=="usb", ATTR{idVendor}=="2116", MODE="0666", OWNER="shakasan" # KT Tech
SUBSYSTEM=="usb", ATTR{idVendor}=="0482", MODE="0666", OWNER="shakasan" # Kyocera
SUBSYSTEM=="usb", ATTR{idVendor}=="17ef", MODE="0666", OWNER="shakasan" # Lenovo
SUBSYSTEM=="usb", ATTR{idVendor}=="1004", MODE="0666", OWNER="shakasan" # LG
SUBSYSTEM=="usb", ATTR{idVendor}=="22b8", MODE="0666", OWNER="shakasan" # Motorola
SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", MODE="0666", OWNER="shakasan" # MTK
SUBSYSTEM=="usb", ATTR{idVendor}=="0409", MODE="0666", OWNER="shakasan" # NEC
SUBSYSTEM=="usb", ATTR{idVendor}=="2080", MODE="0666", OWNER="shakasan" # Nook
SUBSYSTEM=="usb", ATTR{idVendor}=="0955", MODE="0666", OWNER="shakasan" # Nvidia
SUBSYSTEM=="usb", ATTR{idVendor}=="2257", MODE="0666", OWNER="shakasan" # OTGV
SUBSYSTEM=="usb", ATTR{idVendor}=="10a9", MODE="0666", OWNER="shakasan" # Pantech
SUBSYSTEM=="usb", ATTR{idVendor}=="1d4d", MODE="0666", OWNER="shakasan" # Pegatron
SUBSYSTEM=="usb", ATTR{idVendor}=="0471", MODE="0666", OWNER="shakasan" # Philips
SUBSYSTEM=="usb", ATTR{idVendor}=="04da", MODE="0666", OWNER="shakasan" # PMC-Sierra
SUBSYSTEM=="usb", ATTR{idVendor}=="05c6", MODE="0666", OWNER="shakasan" # Qualcomm
SUBSYSTEM=="usb", ATTR{idVendor}=="1f53", MODE="0666", OWNER="shakasan" # SK Telesys
SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0666", OWNER="shakasan" # Samsung
SUBSYSTEM=="usb", ATTR{idVendor}=="04dd", MODE="0666", OWNER="shakasan" # Sharp
SUBSYSTEM=="usb", ATTR{idVendor}=="054c", MODE="0666", OWNER="shakasan" # Sony
SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0666", OWNER="shakasan" # Sony Ericsson
SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0666", OWNER="shakasan" # Sony Mobile Communications
SUBSYSTEM=="usb", ATTR{idVendor}=="2340", MODE="0666", OWNER="shakasan" # Teleepoch
SUBSYSTEM=="usb", ATTR{idVendor}=="0930", MODE="0666", OWNER="shakasan" # Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="19d2", MODE="0666", OWNER="shakasan" # ZTE
```

Ou de manière automatique, via un paquet sur AUR :

```shell
trizen -S android-udev
```

#### Clé FIDO-U2F Key-ID

Idem, pour avec accès à la clé FIDO U2F via USB.

On créer le fichier `/etc/udev/rules.d/70-u2f.rules` et on ajoute

```shell
# this udev file should be used with udev 188 and newer\nACTION!="add|change", GOTO="u2f_end"

# Key-ID FIDO U2F
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="096e", ATTRS{idProduct}=="0850|0880", TAG+="uaccess"

LABEL="u2f_end"
```

#### Ledger Nano S

Et toujours idem... ^^

On créer le fichier `/etc/udev/rules.d/20-hw1.rules` et on ajoute

```shell
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="2b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="3b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="4b7c", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1807", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2581", ATTRS{idProduct}=="1808", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2c97", ATTRS{idProduct}=="0000", MODE="0660", TAG+="uaccess", TAG+="udev-acl"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="2c97", ATTRS{idProduct}=="0001", MODE="0660", TAG+="uaccess", TAG+="udev-acl”
#KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0660", GROUP="plugdev", ATTRS{idVendor}=="2c97"
#KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0660", GROUP="plugdev", ATTRS{idVendor}=="2581"
```

#### Recharger les règles udev

Afin que ces règles udev soit prises en compte directement :

```shell
sudo udevadm control --reload
```

---

## Etape 6 - Customization

### Grub (thèmes)

Pour donner un loog un peu plus sympa à grub, on install le thème

```shell
trizen -S grub-themes-vimix
```

Et on le configure via l'application `grub-customizer`

### Thèmes et icones

On ajoute quelques thèmes GTK et icones

```shell
trizen -S numix-gtk-theme arc-{gtk-theme,icon-theme,solid-gtk-theme}
```

### Widgets

Pour ceux qui aiment les beaux widgets sur leur bureau

```shell
trizen -S conky-manager conky
```

### Wallpapers

#### Nitrogen

Ce utilitaire permet de configurer des wallpapers différents en configuration multi-écran OU de configurer un wallpaper couvrant l'ensemble de ceux-ci.

Malheureusement, le prix à payer est la désactivation des icones sur le bureau.

On install nitrogen

```shell
trizen -S nitrogen
```

On l'ajoute dans les applications à démarrer lors de la session (Panneau de configuration), avec cette commande :

```shell
sh -c "sleep 5; nitrogen --restore"
```

Et on désactive la gestion des icones du bureau par Caja (MATE)

```shell
gsettings set org.mate.background draw-background false
gsettings set org.mate.background show-desktop-icons false
```

### Docks

#### Plank

Dock léger et bien fini en provenance de ElementaryOS.

```shell
trizen -S plank
```

#### Docky

Autre Dock léger et bien fini.

```shell
trizen -S docky
```

---

## Etape 7 - Outils de Dev

Pour cette partie, je passe certaines explications étant donné le publique cible ^^

### Outis de base

```shell
trizen -S gdb ghex jq meld
```

### Langages

#### Javascript

##### NodeJS et NPM

```shell
trizen -S nodejs npm
```

##### NVM

```shell
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

##### Yarn

```shell
sudo npm i -g yarn
```

##### Gulp

```shell
trizen -S gulp
```

##### Angular CLI

```shell
sudo npm i -g @angular/cli
```

Et utiliser yarn à la place de npm (AngularCLI >= 6)

```shell
ng config -g cli.packageManager yarn
```

##### GO

```shell
trizen -S go
```

##### Ionic

```shell
sudo npm i -g ionic cordova
```

#### Lua

```shell
trizen -S lua luajit
```

#### PHP

```shell
trizen -S composer php
```

#### Python

```shell
trizen -S python-{pip,pyqt5,pipenv} tk
```

```shell
sudo -H pip3 install pylint autopep8
```

#### QT5

```shell
trizen -S qtcreator qt5-tools
```

#### Ruby

```shell
trizen -S ruby
```

Dans `~/.bashrc`, ajouter

```shell
PATH=$PATH:~/.gem/ruby/2.5.0/bin
```

### Base de données

#### MongoDB + Compass

```shell
trizen -S mongodb mongodb-compass
```

Pour démarrer le serveur

```shell
sudo systemctl start mongodb.service
```

Et si besoin, le démarrer au boot

```shell
sudo systemctl enable mongodb.service
```

#### MySQL

```shell
trizen -S mysql-workbench
```

#### SQLite + SQLiteBrower

```shell
trizen -S sqlite sqlitebrowser
```

### IDE

#### Android Studio

```shell
trizen -S android-tools android-studio
```

#### Anjuta

```shell
trizen -S anjuta anjuta-extras
```

#### Arduino IDE

```shell
trizen -S arduino arduino-docs
sudo usermod -a -G uucp shakasan
sudo usermod -a -G lock shakasan
```

#### Atom

```shell
trizen -S atom apm
```

#### Brackets

```shell
trizen -S brackets-bin
```

#### Bluefish

```shell
trizen -S bluefish
```

#### Codeblocks

```shell
trizen -S codeblocks
```

#### Eclipse (JAVA)

```shell
trizen -S eclipse-java
```

#### Eric

```shell
trizen -S eric
```

#### Geany

```shell
trizen -S geany geany-plugins
```

#### Glade

```shell
trizen -S glade
```

#### Intellij-idea (CE)

```shell
trizen -S intellij-idea-community-edition
```

#### Lazarus

```shell
trizen -S lazarus-qt5 gdb
```

#### Notepadqq

```shell
trizen -S notepadqq
```

#### PyCharm (CE)

```shell
trizen -S pycharm-community-edition
```

#### Sublime-text

```shell
trizen -S subline-text
```

#### Visual Studio Code

```shell
trizen -S visual-studio-code-bin
```

#### BlueJ

```shell
trizen -S bluej
```

### Outils

#### Ansible

```shell
trizen -S ansible ansible-lint
```

Si vous avez une erreur concernat un module python déjà présent, désinstallez-le via pip3

Dans mon cas, j'ai eu du faire

```shell
sudo -H pip3 uninstall cffi pycparser
```

#### Boostnote

```shell
trizen -S boostnote
```

#### Docker

```shell
trizen -S docker docker-compose
```

Si vous avez une erreur concernat un module python déjà présent, désinstallez-le via pip3

Dans mon cas, j'ai eu du faire

```shell
sudo -H pip3 uninstall docopt
```

#### git

```shell
trizen -S git
```

#### packagecloud cli tool

```shell
gem install package_cloud
```

#### Postman

```shell
trizen -S postman-bin
```

#### Umbrello

```shell
trizen -S umbrello
```

#### Vagrant

```shell
trizen -S vagrant
```

#### VirtualBox

```shell
trizen -S virtualbox
```

#### Wordpress

```shell
trizen -S wp-cli wpscan
```

#### Zeal

```shell
trizen -S zeal
```

---

## Credits & Licence

Doc réalisée par Francois B (Makoto), basée sur le tuto de Frederic Bezies.

[![cc-by-sa](https://i.creativecommons.org/l/by-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-sa/4.0/)
