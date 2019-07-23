# Outils de Dev

Pour cette partie, je passe certaines explications étant donné le publique cible ^^

## Outis de base

```shell
trizen -S gdb ghex jq meld
```

## Langages

### Javascript

#### NodeJS et NPM

```shell
trizen -S nodejs npm
```

#### NVM

```shell
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

#### Yarn

```shell
sudo npm i -g yarn
```

#### Gulp

```shell
trizen -S gulp
```

#### Angular CLI

```shell
sudo npm i -g @angular/cli
```

Et utiliser `yarn` à la place de `npm` (AngularCLI >= 6)

```shell
ng config -g cli.packageManager yarn
```

#### Ionic & Cordova

```shell
sudo npm i -g ionic cordova
```

### GO

```shell
trizen -S go
```

### Lua

```shell
trizen -S lua luajit
```

### PHP

```shell
trizen -S composer php
```

### Python

```shell
trizen -S python-{pip,pyqt5,pipenv} tk
```

```shell
sudo -H pip3 install pylint autopep8
```

### QT5

```shell
trizen -S qtcreator qt5-tools
```

### Ruby

```shell
trizen -S ruby
```

Dans `~/.bashrc`, ajouter

```shell
PATH=$PATH:~/.gem/ruby/2.5.0/bin
```

## Base de données

### MongoDB + Compass

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

### MySQL

```shell
trizen -S mysql-workbench
```

### SQLite + SQLiteBrower

```shell
trizen -S sqlite sqlitebrowser
```

## IDE

### Android Studio

```shell
trizen -S android-tools android-studio
```

### Anjuta

```shell
trizen -S anjuta anjuta-extras
```

### Arduino IDE

```shell
trizen -S arduino arduino-docs
sudo usermod -a -G uucp shakasan
sudo usermod -a -G lock shakasan
```

### Atom

```shell
trizen -S atom apm
```

### Brackets

```shell
trizen -S brackets-bin
```

### Bluefish

```shell
trizen -S bluefish
```

### Codeblocks

```shell
trizen -S codeblocks
```

### Eclipse (JAVA)

```shell
trizen -S eclipse-java
```

### Eric

```shell
trizen -S eric
```

### Geany

```shell
trizen -S geany geany-plugins
```

### Glade

```shell
trizen -S glade
```

### Intellij-idea (CE)

```shell
trizen -S intellij-idea-community-edition
```

### Lazarus

```shell
trizen -S lazarus-qt5 gdb
```

### Notepadqq

```shell
trizen -S notepadqq
```

### PyCharm (CE)

```shell
trizen -S pycharm-community-edition
```

### Visual Studio Code

```shell
trizen -S visual-studio-code-bin
```

### BlueJ

```shell
trizen -S bluej
```

## Outils

### Ansible

```shell
trizen -S ansible ansible-lint
```

Si vous avez une erreur concernat un module python déjà présent, désinstallez-le via pip3

Dans mon cas, j'ai eu du faire

```shell
sudo -H pip3 uninstall cffi pycparser
```

### Boostnote

```shell
trizen -S boostnote
```

### Docker

```shell
trizen -S docker docker-compose
```

Si vous avez une erreur concernat un module python déjà présent, désinstallez-le via pip3

Dans mon cas, j'ai eu du faire

```shell
sudo -H pip3 uninstall docopt
```

### git

```shell
trizen -S git
```

### packagecloud cli tool

```shell
gem install package_cloud
```

### Postman

```shell
trizen -S postman-bin
```

### Umbrello

```shell
trizen -S umbrello
```

### Vagrant

```shell
trizen -S vagrant
```

### VirtualBox

```shell
trizen -S virtualbox
```

### Wordpress

```shell
trizen -S wp-cli wpscan
```

### Zeal

```shell
trizen -S zeal
```
