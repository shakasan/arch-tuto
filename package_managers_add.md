# Package Managers additionnels

## Flatpak

```shell
trizen -S flatpak
```

## Snap

```shell
trizen -S snapd
```

> **Remarque** : Snap fonctionne avec un Store centralisé et géré par Canonical. D'un point de vue d'un libriste, je vous conseil fortement d'éviter autant que possible et privilégier AUR ou Flatpak.

## NodeJS et NPM

On install NodeJS + le package manager NodeJS

```shell
trizen -S nodejs npm
```

On définit où seront installé les packages installé avec l'option -g de `npm`

```shell
npm config set prefix /home/<YOUR_USER_NAME>/.local
```

Dans `~/.bashrc`, on ajoute :

```shell
PATH=~/.local/bin/:$PATH
```

## PIP

On install le package manager de python

```shell
trizen -S python-pip
```
