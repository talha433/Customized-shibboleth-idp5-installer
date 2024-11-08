# Shibboleth IdP Version 5 Installer

## Overview
The AAF Shibboleth IdP Installer is designed to automate the install of version 5 for the [Shibboleth IdP](https://shibboleth.atlassian.net/wiki/spaces/IDP5/overview) on a dedicated server with one of the following supported operating systems;
* Rocky Linux 8 or 9
* Stream 8 or Stream 9
* Redhat 8 or 9
* ORACLE Linux 8 or 9
* Ubuntu 20.04 (Focal Fossa) or 22.04 (Jammy Jellyfish)

For a full set of documentation and guidance on upgrading from Shibboleth version 4 please refer to the [AAF IdPv4 Installer Knowledge base](https://support.aaf.edu.au/support/solutions/articles/19000159910-shibboleth-idp-version-5-installer).

## Status
This software is actively being developed and maintained. It is ready for use in production environments.

This version now enables the technical connection to eduGAIN, the global federation of federations.

This release is for Shibboleth version 5.1.3 running on Jetty 12.0.12

## License
Apache License Version 2.0, January 2004

# AAF Rapid IdP

Need a managed, secure and highly available cloud Identity Provider solution?

[Contact the AAF about Rapid IdP today.](https://aaf.edu.au/rapid/)

[<img src="https://aaf.edu.au/images/Rapid-IdP.png"  width="500"/>](https://aaf.edu.au/rapid/)

# Shibboleth IdP Version 5 Installer

### Actions undertaken during installation
The installation process will:

Optionally perform a yum -y update (system wide package upgrade). Please note that the installer uses yum or apt-get for the installation of all system components (except Jetty and Shibboleth IdP).

* Install all required dependencies (git, ansible, mariadb etc). With the previous step in mind, bootstrap will always use the latest versions of these packages.
* Create self signed keys for Jetty. These are for initial testing of the IdP and are replaced when further customising the Shibboleth IdP
* Install Jetty with Shibboleth IdP. Jetty runs on port 443 and optionally ports 80 and 8443 and creates the Shibboleth IdP web app context /idp.
* Install a MariaDB instance. A database is created (name: idp_db, user: idp_admin) with these schemas populated.
* Installs chrony for time synchronisation.
* Opens local firewall ports 443 and optionally ports 80 and 8443
* Optionally configures the IdP for integration with eduGAIN
* Optionally configures the IdP to enable various REFEDS specifications.

Here’s a complete guide to resolving the Python interpreter issue with Ansible by creating a new `ansible.cfg` file:

---

### Open terminal as a root (Linux Server). 

Run the following command:

```bash
su -
```

Enter the root password.

---

### Grab the latest packages
Update and Upgrade the database of available packages:
```bash
apt update && apt upgrade -y
```

---

### Ensure `Python` is Installed
First, confirm that Python 3 is installed. If it's not, install it with the following commands:

```bash
apt install python3
```

---

### Change TimeZone w.r.t to your country:
```bash
timedatectl set-timezone Asia/Karachi 
```

---

### Set the IdP hostname, The public domain name of the IdP may be used in determined the entityID of the IdP.

(ATTENTION: Replace `idp.example.org` with your IdP Full Qualified `Domain Name` and `<HOSTNAME>` with the IdP hostname)

```bash
vim /etc/hosts
```

`<YOUR SERVER IP ADDRESS> idp.example.org <HOSTNAME>`
`hostnamectl set-hostname <HOSTNAME>`

---

### `pipx` — Install and Run `Python` Applications in Isolated Environments
```bash
apt update
```
```bash
apt install pipx
```

---

### Installing and upgrading `Ansible` with `pipx`
On some systems, it may not be possible to install Ansible with pip, due to decisions made by the operating system developers. In such cases, pipx is a widely available alternative.

These instructions will not go over the steps to install pipx; if those instructions are needed, please continue to the pipx installation instructions for more information.

### Installing `Ansible`
Use pipx in your environment to install the full Ansible package:

```bash
pipx install --include-deps ansible
```

You can install the minimal ansible-core package:

```bash
pipx install ansible-core
```

### Upgrading `Ansible`
To upgrade an existing Ansible installation to the latest released version:

```bash
pipx upgrade --include-injected ansible
```

---

## Event Logging

The installer provides detailed information on each step undertaken during the setup process. If the installation completes successfully, this output can be disregarded. However, for future reference, all installer output is logged to:

```
/opt/shibboleth-idp5-installer/activity.log
```

## Preparing the `bootstrap-v5.ini` File

1. **Download `bootstrap-v5.ini`:**

   For new installations, download the `bootstrap-v5.ini` file with the following command:

   ```bash
   curl https://raw.githubusercontent.com/ausaccessfed/shibboleth-idp5-installer/main/bootstrap-v5.ini > bootstrap-v5.ini
   ```

2. **Edit `bootstrap-v5.ini`:**

   Open and configure the file using a text editor, for example:

   ```bash
   vi bootstrap-v5.ini
   ```

3. **Configure Required Sections:**

Press `i` to enter Insert Mode. Now you can type and edit the file.

   - **[main]**: Review, configure, and uncomment each field in this section.
   - **[ldap]**: If using LDAP, configure the settings here. TLS configuration can be updated post-bootstrap if needed.
   - **[logging, policy, and advanced]**: Carefully review and adjust settings as required, understanding the impact of each change.

Click the `Esc` key and then type `:wq!` to save and exit the configuration.

## Running the Installer

1. **Download and Prepare `bootstrap-v5.sh`:**

   Execute the following command to download and make the installer executable:

   ```bash
   curl https://raw.githubusercontent.com/ausaccessfed/shibboleth-idp5-installer/main/bootstrap-v5.sh > bootstrap-v5.sh && chmod u+x bootstrap-v5.sh
   ```

   Ensure `bootstrap-v5.ini` is in the same directory as `bootstrap-v5.sh`.

2. **Run the Installer as Root:**

   Execute the installer script as the root user:

   ```bash
   ./bootstrap-v5.sh
   ```

This process will install and configure the server to operate as a Shibboleth Identity Provider (IdP).

---

## Allowing the installer to run again
In general you will never need to re-run the bootstrap-v5.sh script after it has completed creating the `/opt/shibboleth-v5-installer` directory. You should use the deploy or upgrade scripts instead.

If you must re-run bootstrap-v5.sh then you remove the lock file first. Note this will overwrite any previous installations.

```bash
rm /root/.lock-idp-bootstrap-v5 && ./bootstrap-v5.sh
```

The bootstrap-v5 process will now start over and attempt to install and configure your server to operate as a Shibboleth IdP.
