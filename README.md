# Craft Trellis

Heavily sourced from [Roots Trellis](https://roots.io/trellis). If you have any WordPress needs, I can’t recommend the Roots team highly enough.

- Local development environment with Vagrant
- High-performance production servers
- One-command deploys

## What's included

Craft Trellis will configure a server with the following and more:

* Ubuntu 14.04 Trusty LTS
* Nginx (with optional FastCGI micro-caching)
* PHP 7.0
* MariaDB (a drop-in MySQL replacement)
* SSL support (scores an A+ on the [Qualys SSL Labs Test](https://www.ssllabs.com/ssltest/))
* Let's Encrypt integration for free SSL certificates
* HTTP/2 support (requires SSL)
* Composer
* sSMTP (mail delivery)
* MailHog
* Memcached
* Fail2ban
* ferm

## Requirements

Make sure all dependencies have been installed before moving on:

* [Ansible](http://docs.ansible.com/ansible/intro_installation.html#latest-releases-via-pip) 2.0.2
* [Virtualbox](https://www.virtualbox.org/wiki/Downloads) >= 4.3.10
* [Vagrant](https://releases.hashicorp.com/vagrant/1.8.1/) 1.8.1
* [vagrant-bindfs](https://github.com/gael-ian/vagrant-bindfs#installation) >= 0.3.1 (Windows users may skip this)
* [vagrant-hostmanager](https://github.com/smdahlen/vagrant-hostmanager#installation)

## Installation

The recommended directory structure for a Craft Trellis project looks like:

```shell
example.com/            # → Root folder for the project
├── ansible/            # → Playbooks that automate server setup & deploy
├── app/                # → Craft CMS core (don’t touch!)
└── site/               # → A Craft CMS site (feel free to rename on multisite setups)
      ├── config/
      ├── plugins/
      ├── public/       # → Web root for the website
      │     ├── assets/ # → Upload folder for all Craft assets  
      │     └── dist/   # → Production ready CSS, JS, etc.
      ├── src/          # → Contains source files – Sass, uncompiled JS, etc.
      ├── storage/      # → Where Craft stores a bunch of files that get dynamically saved during use.
      └── templates/    # → Template parts
```


1. Create a new project directory: `$ mkdir example.com && cd example.com`
2. Clone Craft Trellis: `$ git clone --depth=1 git@github.com:tyler-morrison/craft-trellis.git ansible && rm -rf ansible/.git`
3. Clone Bedrock: `$ git clone --depth=1 git@github.com:tyler-morrison/craft-bedrock.git site && rm -rf site/.git`
4. Install the Ansible Galaxy roles: `$ cd ansible && ansible-galaxy install -r requirements.yml`

## Local development setup

1. Configure your Craft sites in `group_vars/development/craft_sites.yml` and in `group_vars/development/vault.yml`
2. Run `vagrant up`

## Remote server setup (staging/production)

A base Ubuntu 14.04 server is required for setting up remote servers.

1. Configure your Craft sites in `group_vars/<environment>/craft_sites.yml` and in `group_vars/<environment>/vault.yml`
2. Add your server IP/hostnames to `hosts/<environment>`
3. Specify public SSH keys for `users` in `group_vars/all/users.yml`
4. Run `ansible-playbook server.yml -e env=<environment>` to provision the server

## Deploying to remote servers

1. Add the `repo` (Git URL) of your Bedrock Craft project in the corresponding `group_vars/<environment>/craft_sites.yml` file
2. Set the `branch` you want to deploy
3. Run `./deploy.sh <environment> <site name>`
4. To rollback a deploy, run `ansible-playbook rollback.yml -e "site=<site name> env=<environment>"`
