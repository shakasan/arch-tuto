# Package Managers additionnels

## Flatpak

```shell
trizen -S flatpak
```

## Snap

```shell
trizen -S snapd
```

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
