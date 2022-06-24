# Ansible — Convertigo Community Edition

This playbook installs [Convertigo Community Edition](https://convertigo.com/) on a Debian/Ubuntu instance. Nginx will handle the HTTPS connection from the browser and create a reverse proxy to [Tomcat](https://tomcat.apache.org/).

**Assumption:** Nginx installed using the Ansible script below,
* ansible__nginx
	* GitHub: [github.com/k3karthic/ansible__nginx](https://github.com/k3karthic/ansible__nginx)
	* Codeberg: [codeberg.org/k3karthic/ansible__nginx](https://codeberg.org/k3karthic/ansible__nginx)

**Assumption:** Instance deployed using the Terraform script below,
* terraform__oci-instance-3
	* GitHub: [github.com/k3karthic/terraform__oci-instance-3](https://github.com/k3karthic/terraform__oci-instance-3)
	* Codeberg: [codeberg.org/k3karthic/terraform__oci-instance-3](https://codeberg.org/k3karthic/terraform__oci-instance-3)

## Code Mirrors

* GitHub: [github.com/k3karthic/ansible__convertigo-server](https://github.com/k3karthic/ansible__convertigo-server/)
* Codeberg: [codeberg.org/k3karthic/ansible__convertigo-server](https://codeberg.org/k3karthic/ansible__convertigo-server)

## Requirements

Install the following before running the playbook,
```
pip install oci
ansible-galaxy collection install oracle.oci
```

## Dynamic Inventory

The Oracle [Ansible Inventory Plugin](https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/ansibleinventoryintro.htm) populates public Ubuntu instances.

All target Ubuntu instances must have the freeform tag `nginx_service: yes`.

## Configuration

1. Create an inventory file for the instance in `inventory/group_vars/`. Use `inventory/group_vars/tag_ydns_host=mg-oracle3.ydns.eu.yml.sample` as a reference. Specify the following variables,

	1. `tomcat_version`: Latest version of Tomcat 9
	1. `convertigo_version`: Latest version of Convertigo Server [https://github.com/convertigo/convertigo/releases](https://github.com/convertigo/convertigo/releases)
	1. `convertigo_workspace`: Convertigo workspace path
	1. `admin_username`: Username for administrator
	1. `admin_password`: Password for administrator as hex encoded SHA-512 hash.
	1. `testplatform_username`: Test Platform username
	1. `testplatform_password`: Test Platform password as hex encoded SHA-512 hash.
	1. `crypto_passphrase`: Passphrase that Convertigo will use to encrypt secrets
	1. `domain_name`: Domain name of the instance
1. Update `inventory/oracle.oci.yml`,
    1. Specify the region where you have deployed your server on Oracle Cloud. List of regions are at [docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm).
    1. Configure the authentication as per the [Oracle Guide](https://docs.oracle.com/en-us/iaas/Content/API/Concepts/sdkconfig.htm#SDK_and_CLI_Configuration_File)
4. Set username and ssh authentication in `inventory/group_vars/all.yml`

## Deployment

Run the playbook using the following command,
```
./bin/apply.sh
```

## Encryption

Encrypt sensitive files (SSH private key and `inventory/group_vars/tag_ydns_host=mg-oracle3.ydns.eu.yml`) before saving them. `.gitignore` must contain the unencrypted file paths.

Use the following command to decrypt the files after cloning the repository,

```
$ ./bin/decrypt.sh
```

Use the following command after running terraform to update the encrypted files,

```
$ ./bin/encrypt.sh <gpg key id>
```
