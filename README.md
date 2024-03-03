# 🔬 Security Lab

This repository aims to simplify the installation process for a security lab tailored for tasks such as reverse engineering, forensics, and malware development.

## 📺 How does it works ?

First, a box of your choose will be downloaded and deployed. Then, provisioning will be performed on the VM, including setting up default configurations (like dark mode and keyboard layouts), updates, and FlareVM configuration.

The tools installed in the VM are sourced from the VM-Packages repository maintained by Mandiant, which is utilized by FlareVM. The configuration is highly customizable, allowing you to add or remove packages according to your requirements.

The tools installed comes from [VM-Packages](https://github.com/mandiant/VM-Packages) repository of Mandiant, which is used by [FlareVM](https://github.com/mandiant/flare-vm). The configuration is highly customizable, allowing you to add or remove packages according to your requirements in `playbook/roles/install_flare/files/*.xml`.

## 🧭 Repository Structure

This repository is structured as follows:

```text
📦SecLab
 ┣ 📜README.md
 ┣ 📜Vagrantfile
 ┣ 📂vagrant
 ┃ ┗ 📜*.ps1
 ┣ 📂playbook
 ┃ ┣ 📂groups_vars
 ┃ ┃ ┗ 📜all
 ┃ ┣ 📂roles
 ┃ ┃ ┗ ...
 ┃ ┣ 📂vars
 ┃ ┃ ┗ 📜*.yml
 ┃ ┣ 📜install.yml
 ┃ ┣ 📜inventory.ini
 ┃ ┗ 📜requirements.yml
 ┣ 📂forensic
 ┣ 📂maldev
 ┗ 📂reverse
```

- `Vagrantfile`: Responsible for deploying all VMs.
- `vagrant`: Contains scripts executed on the VMs by vagrant.
- `playbook`: Holds provisioning data.
  - `groups_vars`: Contains a file called `all` with default variables for all configured hosts.
  - `roles`: Contains different tasks performed on the hosts in each roles.
  - `vars`: Contains files for each VM, specifying configurations.
  - `install.yml`: References the roles to be executed.
  - `inventory.ini`: Lists all hosts in the inventory.
  - `requirements.yml`: Lists the requirements for the Ansible playbook.
- The last three folders contain shared files for each VM.

## 🪛 Installation

### Dependency

To set up your own lab, you'll need the following programs:
- [Vagrant](https://developer.hashicorp.com/vagrant/downloads)
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

Once done, perform `ansible-galaxy install -r playbook/requirements.yml` to install the necessary requirements.
 
> ⚠️ You need to provide your own ZIP file for KAPE, which should be placed in `playbook/roles/add_kape/files/KAPE.zip`.

### Default

Run `vagrant up <box-name>` to download, install, and provision the target. If the box is already present, you can execute `vagrant up <box-name> --provision`.

> If you wish to modify the machine name, you'll need to replace it in both `Vagrantfile` and `playbook/inventory.ini`.

## ⚒️ Troubleshooting during installation

### Windows Defender

If *Windows Defender* detects a threat, disable it manually, then run:

```text
$ ansible-playbook --limit="<box-name>" --inventory-file=playbook/inventory.ini -v playbook/install.yml -t flarevm
```

> If you want to disable it totally, follow the instructions outlined by [commando-vm](https://github.com/mandiant/commando-vm?tab=readme-ov-file#pre-install-procedures).

### Boxstarter

If you encounter an error related to *Boxstarter*, execute the following command in the VM:

```text
PS> Set-ExecutionPolicy Unrestricted -Force
PS> C:\Users\vagrant\Downloads\flare-vm\install.ps1 -customConfig C:\Users\vagrant\Downloads\flare-vm\config.xml -password vagrant -noChecks -noGui -noWait
```

### Other

If any other error interrupts the installer, instead of running again the entire workflow, you can trigger specific parts using tags:

```text
$ ansible-playbook --limit="<box-name>" --inventory-file=playbook/inventory.ini -v playbook/install.yml -t <tags>
```

## ⬇️ Upgrading packages

To upgrade all packages to their latest versions, run:

```text
C:\Users\vagrant> choco upgrade all -y
```

## 💸 Renewing license

As the VMs provided by Vagrant are specifically intended for development purposes, their licenses are valid for only 90 days. However, you can extend this period 90 days more with:

```text
C:\Users\vagrant> slmgr /rearm
```

## 📘 Office

For manual installation of Office, use the following command:

```text
PS> choco install microsoft-office-deployment --params "/64bit /DisableUpdate:TRUE /Exclude:Publisher,PowerPoint,OneDrive,Outlook,OneNote,Lync,Groove,Access" --force
```