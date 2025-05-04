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

- Installation du rôle

```bash
mkdir -p $HOME/install-repo-installer/roles
```

```bash
vim $HOME/install-repo-installer/requirements.yml
```

```yaml
- name: ansible-role-repo-installer
  src: git+https://github.com/willbrid/ansible-role-repo-installer.git
  version: v0.0.1
```

```bash
cd $HOME/install-repo-installer && ansible-galaxy install -r requirements.yml --roles-path roles
```

> Note: On suppose qu’un fichier `hosts.ini` (dans le repertoire `$HOME/install-repo-installer`) est défini, contenant l’inventaire des serveurs de groupe `all` utilisant des distributions **Debian** ou **RedHat**.

- Utilisation du rôle dans un playbook

Nous allons, par exemple, ajouter le dépôt des paquets **OpenTofu** pour les distributions **Debian** et **RedHat**.

```bash
vim $HOME/install-repo-installer/playbook.yml
```

```yaml
---
- hosts: all
  gather_facts: true
  vars:
    ri_redhat_dependencies_packages: ["ca-certificates"]
    ri_redhat_distro_repos:
      - name: opentofu
        description: opentofu
        baseurl: https://packages.opentofu.org/opentofu/tofu/rpm_any/rpm_any/\$basearch
        is_external_repo: true
        enabled: true
        extra_options:
          repo_gpgcheck: 0
          gpgcheck: 1
          gpgkey: >
            https://get.opentofu.org/opentofu.gpg
            https://packages.opentofu.org/opentofu/tofu/gpgkey
          sslverify: 1
          sslcacert: /etc/pki/tls/certs/ca-bundle.crt
          metadata_expire: 300
    ri_debian_dependencies_packages: ["apt-transport-https", "ca-certificates", "curl", "gnupg"]
    ri_debian_distro_repos:
      - name: opentofu
        baseurl: https://packages.opentofu.org/opentofu/tofu/any/
        distribution: any
        components: ["main"]
        type: "deb"
        gpgkeys:
          - id: main
            url: https://get.opentofu.org/opentofu.gpg
            type: "gpg"
          - id: repo
            url: file:///tmp/opentofu-repo.gpg
            type: "gpg"
        is_external_repo: true
  
  pre_tasks:
    - name: Install curl and gnupg [Debian]
      ansible.builtin.apt:
        pkg: ['curl', 'gnupg']
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"
    - name: Download opentofu asc repo key and save in /tmp/opentofu-repo.gpg [Debian]
      ansible.builtin.shell: "curl -fsSL https://packages.opentofu.org/opentofu/tofu/gpgkey | gpg --no-tty --batch --dearmor -o /tmp/opentofu-repo.gpg >/dev/null"
      args:
        creates: /tmp/opentofu-repo.gpg
      when: ansible_os_family == "Debian"
  
  roles:
    - ansible-role-repo-installer
```

```bash
cd $HOME/install-repo-installer && ansible-playbook -i hosts.ini playbook.yml
```

Licence
-------

MIT

Informations sur l'auteur
------------------

William Bridge NGASSAM
