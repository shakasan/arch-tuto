# Apps

L'installation de base étant terminée, passons à l'ajout des applications.

Pour les package managers pip3, npm, gem, veuillez vous référer à la partie sur les outils de développement pour leur éventuelle installation.

> **Astuce** : si vous voulez des infos sur un paquet en particulier, en résumé `trizen -Ss <nom_du_paquet>` et en détaillé `trizen -Si <nom_du_paquet>`.

## Utilitaires

Depuis les dépôts

```shell
trizen -S detox network-manager-applet qt5ct qt5-styleplugins clamav clamtk grub-customizer deja-dup acetoneiso2 alltray wget curl aria2 htop bmon mc screen bleachbit copyq dialog dosbox fcrackzip fdupes figlet gawk gnome-disk-utility gnome-font-viewer gparted gsmartcontrol gtk-engine-murrine guake idle3-tools less lsb-release mupdf mupdf-tools numlockx pdfcrack pdfgrep procps-ng psensor pv rarcrack screenfetch synapse terminator tilda unrar zip pandoc stress mesa-demos odt2txt strace hwinfo evince
```

Depuis AUR

```shell
trizen -S tmsu xcalib qtqr woeusb libgksu gksu multibootusb-git spectre-meltdown-checker inxi cryptkeeper s-tui linpack nano-syntax-highlighting-git kdocker etcher-git
```

Depuis NPM

```shell
npm i -g coinmon
```

Depuis PIP

```shell
sudo -H pip3 install tldr cheat md2pdf
```

## Multimédia

Depuis des dépôts

```shell
trizen -S quodlibet vlc mpv xsane shotwell simplescreenrecorder lib32-simplescreenrecorder asunder audacious audacious-plugins audacity avidemux-cli avidemux-qt blender cheese cuetools darktable peek shotcut flacon gpick gpicview-gtk3 guvcview handbrake handbrake-cli hugin inkscape kodi krita krita-plugin-gmic milkytracker mkvtoolnix-cli mkvtoolnix-gui moc openshot picard pitivi rawtherapee shntool smplayer smplayer-{skins,themes} soundconverter id3v2 youtube-dl mac libdvdcss
```

Depuis AUR

```shell
trizen -S font-manager gmusicbrowser radiotray-ng vidcutter entangle indicator-sound-switcher python2-pydbus-git gyazo green-recorder xnviewmp
```

Depuis Flatpak

```shell
sudo flatpak install flathub com.spotify.Client
```

## Rhythmbox + plugins

Depuis les dépôts

```shell
trizen -S rhythmbox
```

Depuis AUR

```shell
trizen -S rhythmbox-{llyrics,plugin-alternative-toolbar-git,plugin-hide-git,plugin-radio-browser-git,plugin-tray-icon-git}
```

## Gimp + plugins

Depuis les dépôts

```shell
trizen -S gimp gimp-{help-fr,refocus,nufraw,plugin-fblur,plugin-gmic,plugin-lqr,plugin-wavelet-denoise}
```

Depuis AUR

```shell
trizen -S gimp-{plugin-resynthesizer,plugin-refocusit,plugin-pandora,plugin-layers-to-divs,plugin-export-layers}
```

## eBook

Depuis les dépôts

```shell
trizen -S calibre sigil fbreader
```

## Graveur CD/DVD/BR

Depuis les dépôts

```shell
trizen -S brasero k3b xfburn
```

## Bureautique

Depuis les dépôts

```shell
trizen -S libreoffice-still libreoffice-still-fr dia scribus
```

## Internet, Web

Depuis les dépôts

```shell
trizen -S firefox firefox-i18n-fr chromium franz-bin slack-desktop thunderbird thunderbird-i18n-fr nextcloud-client digikam discord-canary filezilla geary google-chrome hexchat icedtea-web links mumble teamspeak3 mypaint opera opera-ffmpeg-codecs pidgin pidgin-{libnotify,otr} purple-{facebook,plugin-pack,skypeweb} quiterss syncthing syncthing-gtk telegram-{desktop,qt,purple} transmission-gtk vivaldi vivaldi-ffmpeg-codecs skypeforlinux-stable
```

Depuis AUR

```shell
trizen -S whalebird-bin tootle birdtray megasync insync
```

## Réseau

Depuis les dépôts

```shell
trizen -S aircrack-ng ngrep networkmanager-openvpn dsniff wireshark-qt wireshark-cli iftop iptraf-ng nethogs whois keychain x11-ssh-askpass nmap bind-tools bluez create_ap
```

Depuis NPM

```shell
npm i -g whatismyip
```

Depuis PIP

```shell
sudo -H pip3 install speedtest-cli
```

## Plugins Caja

Depuis les dépôts

```shell
trizen -S caja-{open-terminal,image-converter,sendto,share,wallpaper,xattr-tags,extensions-common}
```

## Nautilus + plugins

Depuis les dépôts

```shell
trizen -S nautilus nautilus-{image-converter,sendto,share}
```

## Thunar + plugins

Depuis les dépôts

```shell
trizen -S thunar thunar-{archive-plugin,media-tags-plugin,volman}
```

## Wine

Depuis les dépôts

```shell
trizen -S wine winetricks playonlinux
```

## CAD

Depuis les dépôts

```shell
trizen -S kicad kicad-library kicad-library-3d librecad
```

## jeux

Depuis les dépôts

```shell
trizen -S joyutils lutris
```

> **Remarque** : voir la partie 2 de l'installation de base pour les pilotes graphiques
> **Remarque 2** : voir la partie Matériel pour l'installation de Vulkan

## Gadgets

Depuis AUR

```shell
trizen -S no-more-secrets-git
```

## Perso

Depuis AUR

```shell
trizen -S wpfind
```
