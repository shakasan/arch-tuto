# Customization

## Grub (thèmes)

Pour donner un look un peu plus sympa à `GRUB`, on install le thème depuis AUR

```shell
trizen -S grub2-theme-vimix arch-silence-grub-theme-git
```

Et on le configure via l'application `grub-customizer`

## Thèmes et icones

On ajoute quelques thèmes GTK et icones

Depuis les dépôts

```shell
trizen -S arc-{gtk-theme,icon-theme,solid-gtk-theme}
```

Depuis AUR

```shell
trizen -S numix-{gtk-theme-git,icon-theme-git}
```

## Widgets

Pour ceux qui aiment les beaux widgets sur leur bureau

```shell
trizen -S conky-manager conky
```

## Wallpapers

### Nitrogen

Ce utilitaire permet de configurer des wallpapers différents en configuration multi-écran OU de configurer un wallpaper couvrant l'ensemble de ceux-ci.

Malheureusement, le prix à payer est la désactivation des icones sur le bureau.

On install

```shell
trizen -S nitrogen
```

On l'ajoute dans les applications à démarrer lors de la session (Panneau de configuration), avec cette commande :

```shell
sh -c "sleep 5; nitrogen --restore"
```

Et on désactive la gestion des icones du bureau par `Caja` (`MATE`)

```shell
gsettings set org.mate.background draw-background false
gsettings set org.mate.background show-desktop-icons false
```

## Docks

### Plank

Dock léger et bien fini en provenance de ElementaryOS.

```shell
trizen -S plank
```

### Docky

Autre Dock léger et bien fini, mais malheureusement plus maintenu... Vous pouvez tenter ;)

Depuis AUR

```shell
trizen -S docky
```
