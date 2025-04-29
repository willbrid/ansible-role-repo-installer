Ansible-role-repo-installer
=========

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/willbrid/ansible-role-repo-installer/blob/main/LICENSE) [![CI](https://github.com/willbrid/ansible-role-repo-installer/actions/workflows/ci.yml/badge.svg)](https://github.com/willbrid/ansible-role-repo-installer/actions/workflows/ci.yml)

**ansible-role-repo-installer** est un rôle Ansible permettant d'ajouter facilement des dépôts logiciels sur les systèmes basés sur Debian (via APT) et RedHat (via YUM ou DNF). <br>
Il prend en charge les dépôts officiels ou externes, la gestion des clés GPG, et offre une configuration souple grâce à des variables dédiées pour chaque famille de distribution.

Exigences
------------

Aucune.

Description des Variables
--------------

|Nom|Type|Description|Obligatoire|Valeur par défaut|
|---|----|-----------|-----------|-----------------|
`ri_debian_dependencies_packages`|list|liste des dépendences nécessaires avant l'installation des dépôts des distributions Debian|non|`[]`
`ri_debian_distro_repos`|list|liste dont chaque élément est une caractéristique d'un dépôts d'une distribution Debian. (Voir les caractéristiques de ses éléments ci-dessous)|oui (pour les distributions Debian)|`[]`
`ri_debian_distro_repos[].name`|str|nom identifiant d'un dépôt Debian|oui|``
`ri_debian_distro_repos[].baseurl`|str|url d'un dépôt Debian|oui|``
`ri_debian_distro_repos[].distribution`|str|distribution d'un dépôt Debian (focal, bullseye, noble,...)|oui|``
`ri_debian_distro_repos[].components`|list|composants d'un dépôt Debian (main, stable,...)|oui|``
`ri_debian_distro_repos[].type`|str|le type d'un dépôt Debian(deb ou deb-src)|non|`"deb"`
`ri_debian_distro_repos[].is_external_repo`|bool|indication si c'est un dépôt Debian officiel (false) ou externe (true)|non|`true`
`ri_debian_distro_repos[].arch`|str|indication sur l'architecture supportée par un dépôt Debian (amd64, arm64,...)|non|`""`
`ri_debian_distro_repos[].gpgkeys`|list|les clés d'un dépôt Debian (Voir les caractéristiques de ses éléments ci-dessous)|non|`"[]"`
`ri_debian_distro_repos[].gpgkeys[].id`|str|identifiant unique d'une clé d'un dépôt Debian|oui|`""`
`ri_debian_distro_repos[].gpgkeys[].url`|str|url d'une clé d'un dépôt Debian (prend en compte les protocoles : http, https, ftp, file)|oui|`""`
`ri_debian_distro_repos[].gpgkeys[].type`|str|type d'une clé d'un dépôt Debian (soit asc ou gpg)|non|`"asc"`
`ri_redhat_distro_repos`|list|liste dont chaque élément est une caractéristique d'un dépôt d'une distribution RedHat. (Voir les caractéristiques de ses éléments ci-dessous)|oui (pour les distributions RedHat)|`[]`
`ri_redhat_distro_repos[].name`|str|nom identifiant d'un dépôt RedHat|oui|``
`ri_redhat_distro_repos[].description`|str|description d'un dépôt RedHat|non|`ri_redhat_distro_repos[].name`
`ri_redhat_distro_repos[].baseurl`|str|url d'un dépôt RedHat|oui|``
`ri_redhat_distro_repos[].is_external_repo`|bool|indication si c'est un dépôt RedHat est officiel (false) ou externe (true)|non|`true`
`ri_redhat_distro_repos[].enabled`|bool|indication si c'est un dépôt RedHat qui doit être activé (true) ou non (false)|non|`true`
`ri_redhat_distro_repos[].extra_options`|dict|les autres options de configuration d'un dépôt RedHat|non|`{}`

Dépendances
------------

Aucune.

Exemple Playbook
----------------

En cours...

Licence
-------

MIT

Informations sur l'auteur
------------------

William Bridge NGASSAM
