# Matos

## Udev

Certains périphériques USB nécessitent des règles UDEV spécifiques pour qu'ils soient accessibles.

### Périphs Android

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

### Clé FIDO-U2F Key-ID

Idem, pour avec accès à la clé FIDO U2F via USB.

On créer le fichier `/etc/udev/rules.d/70-u2f.rules` et on ajoute

```shell
# this udev file should be used with udev 188 and newer\nACTION!="add|change", GOTO="u2f_end"

# Key-ID FIDO U2F
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="096e", ATTRS{idProduct}=="0850|0880", TAG+="uaccess"

LABEL="u2f_end"
```

### Ledger Nano S

Et toujours idem... ^^ pour le Ledger Nano S

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

### Recharger les règles udev

Afin que ces règles `udev` soit prises en compte directement :

```shell
sudo udevadm control --reload
```

## Apps lié au matos

### Logitech Unifying

Solaar permet de configurer les périphériques Logitech compatible Unifying.

```shell
trizen -S solaar
```

### Lecteur de carte

Le nécessaire pour utiliser les cartes à puces (carde d'identité, ...)

```shell
trizen -S pcsc-tools
```

### Ledger Nano S

On install la nouvelle application de gestion/config du wallet hardware Ledger Nano S.

```shell
trizen -S ledger-live-bin
```