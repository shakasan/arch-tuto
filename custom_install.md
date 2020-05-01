# Configuration

## SSD

On va activer le TRIM pour les disques SSD

```shell
sudo systemctl enable fstrim.timer
```

## Swappiness

Si l'on dispose de suffisament de RAM, on peut limiter l'usage du swap et donc ménager un peu le SSD

Dans `/etc/sysctl.d/99-sysctl.conf`, on ajoute :

```shell
vm.swappiness=10
```

## Mise à jours du Microcode du CPU

> Remarque : Cette étape est optionnelle, mais fortement recommandée (failles spectre, meltdown, ...)

On install le nécessaire (CPU Intel)

```shell
trizen -S iucode-tool intel-ucode
```

Et on met à jour la configuration de `GRUB` pour qu'il en tienne compte

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Firewall

On install les outis graphique/cli

```shell
trizen -S ufw gufw
```

Si vous utilisez `syncthing`, on ouvre les ports pour celui-ci

```shell
sudo ufw allow syncthing
```

De même si vous faites l'install depuis une connexion SSH, on ouvre les ports SSH également. Et c'est important de le faire **avant** d'activer le firewall sous peine de se retrouver à la porte ;)

```shell
sudo ufw allow ssh
```

Et on active le firewall au boot.

```shell
sudo ufw enable
```

## Unbound (cache DNS)

Pour accélérer la résolution DNS, on install un cache DNS local

```shell
trizen -S unbound
```

Et on active le service au boot

```shell
sudo systemctl start unbound.service
sudo systemctl enable unbound.service
```

## .bashrc

Dans `~/.bashrc`, on ajoute :

Un theme plus sympa pour `mocp`

```shell
alias mocp='mocp -T "darkdot_theme"'
```

Des couleurs et un affichage plus complet de la commande `ls`

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

## Terminal par défaut

> **Remarque** : Nécessaire pour certaines applications comme `rclonebrowser` par exemple

Dans `/etc/environment`, on ajoute

```shell
TERMINAL=/usr/bin/mate-terminal
```

## Numlock ON avec lightdm au boot

Rien de plus agaçant que de ne pas avoir la touche numlock activée lorsqu'on arrive sur l'écran de connexion.

Pour `lightdm`, dans `/etc/lightdm/lightdm.conf`, on ajoute la ligne suivante

```shell
[Seat:*]
greeter-setup-script=/usr/bin/numlockx on
```

## Partages SMB/CIFS dans /etc/fstab

Exemple de configuration dans `/etc/fstab` pour monter automatiquement au boot un dossier partagé sur son NAS avec smb/cifs

```shell
//<VOTRE_ADRESSE_IP>/<NOM_DU_DOSSIER_PARTAGE>     /mnt/<NOM_DU_POINT_DE_MONTAGE_LOCAL>          cifs   vers=3.0,x-systemd.automount,x-systemd.idle-timeout=1min,_netdev,credentials=/etc/smbcreds,rw,iocharset=utf8,uid=1000,gid=1000 0 0
```

> **Remarque** : le `uid` et le `gid` sont a adapter si besoin à ceux de votre utilisateur

Et on regénère les règles `systemd` pour le nouveau point de montage smb/cifs

```shell
sudo systemctl daemon-reload
sudo systemctl restart remote-fs.target
sudo systemctl restart local-fs.target
```

## Environnement QT5

Pour donner la même apparences aux applications écrites avec `QT5` et `GTK`, on va utiliser `qt5ct` comme intermédiaire. Celui-ci s'ajoute dans le Panneau de configuration.

Dans `/etc/environment`, on ajoute

```shell
QT_QPA_PLATFORMTHEME=qt5ct
```
