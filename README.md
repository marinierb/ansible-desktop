# Ansible Manjaro Setup

![GitHub last commit](https://img.shields.io/github/last-commit/marinierb/ansible-desktop)

## TO DO
- spotify (aur build)
- For crackling audio
<br>echo options snd-hda-intel power_save=0 power_save_controller=N | sudo tee /etc/modprobe.d/hda.conf

## Custom configure my personal Manjaro desktop

- Can be used to build from scratch or to fix broken stuff.
- Installs and configures all software, peripherals, etc.
- Any change that I make to my system is made through here.
- Includes backups of a few things so that they can be restored via ansible.

## Steps to rebuild my desktop from scratch

### On my desktop

>**In a nutshell:**
><br>My ansible folder is on Dropbox. So, get Manjaro up and running, install and sync Dropbox, install and run ansible.
><br>Cloning this repo is another way to get started, but I need Dropbox for the backups anyway, so...

1. Install the [GNOME edition of Manjaro](https://manjaro.org/download/)

1. Update mirror list

        sudo pacman-mirrors -c Canada United_States

1. Update all packages

        sudo pacman -Syyu

1. Reboot

1. In the package manager GUI

        --> enable AUR support and updates

1. Remove device from Dropbox security settings (to free it up for this install)

1. Install Dropbox

        pamac build dropbox
        
1. Launch Dropbox (there is a Gnome launcher for it) and finish the setup

1. Wait for Dropbox sync to finish

1. Install ansible and requisites

        sudo pamac install ansible python-psutil
        pamac build ansible-aur-git

1. Go to ***Dropbox/ansible-desktop*** directory

        cd Dropbox/ansible/desktop

1. Ensure that the required secrets are set up

        ansible-vault view group_vars/all/vault.yml

1. Review the ***playbook-nuc*** playbook

        cat playbook-desktop.yml

1. Review the backups folder, remove "skip" suffixes as desired (see backups below)

1. Run the ansible helper script!

        ./an-desktop

1. Reboot

#
# ENJOY!

## Steps to run a single role

This will configure a single element such as a software package or a system setting.

1. Look for the appropriate tag in the playbook
1. From the ansible directory, run the ansible helper script with that tag as a parameter

Example:

        ./an-nuc libreoffice

## About backups

I keep most of my stuff in my Dropbox directory, but there are few software configurations that I back up using ansible. At the moment these are:

- A bunch of Gnome settings
- Libreoffice customizations
- VS Code configuration

### To back up everything

Simply run the ansible helper script:

        an-backup

### To back up items

Run the ansible helper script with a tag, e.g.

        an-backup libreoffice

## About restoring from backups

Some roles look for corresponding backups and restore them.
- The backups are stored in the ***ansible-desktop/backups*** directory.
- To avoid restoring backups unless absolutely necessary, a string is appended to the backup files' names to hide them from the restore tasks.
- The string is set in the variable ***skipRestore*** in ***host_vars/nuc.yml***. Anything other than blank will hide the backups from the restore tasks.
- Manually remove this string from the backup's name if a restore is in fact desired.

### To restore a configuration from a backup

- Look for the appropriate tag in the main playbook
- Remove any "skip" string appended to the name of the backup
- Run ***an-nuc*** with that tag

Example:

        mv backups/libreoffice.zip.skiprestore backups/libreoffice.zip
        an-nuc libreoffice

### EOF
