# Ansible Role: ACAS Server

This [Ansible Role](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html) will
configure a RHEL 7 or 8 system as an [Assured Compliance Assessment System (ACAS)](https://intelshare.intelink.gov/sites/DISA-ID/ACAS) server.  This role does
not install the Tenable software that is included.  Instead, it configures the basic system in accordance
with the current DISA guidelines published to the [ACAS Program Site](https://intelshare.intelink.gov/sites/DISA-ID/ACAS).
This role will also ensure the system is running in FIPS mode, which may cause a reboot of the system.

This role will continue from step 3.3 of the [ACAS RHEL 8 User Guide](https://patches.csd.disa.mil/SecureDownload.aspx?sfs=asset&fGuid=249900).
Previous steps describe the required RHEL 8 installation options.

Of note, DISA has not digitally signed the acas_configure RPM.  There is no GPG verification available.  However,
DISA has provided a SHA256 hash signature for the RPM file which is included in `default/main.yml`.  

## Requirements

* Server must be configured IAW [ACAS EL8 User Guide](https://patches.csd.disa.mil/SecureDownload.aspx?sfs=asset&fGuid=249900)
* The acas_configure package must be hosted on an accessible web server--dnf / yum repositories are not supported at this time

## Role Variables

### Sensitive Variables

The following variables are defined and need to have values provided by the calline site.  STRONGLY recommend
using [Ansible Vault](https://docs.ansible.com/ansible/latest/vault_guide/vault.html) or some other secret-sharing method to
ensure these values are not stored in plaintext within your playbooks.

* grub2_password: Password used to modify boot settings or access single-user mode

### Non-Sensitive Variables

The following values can be provided directly in your playbooks without any security concerns.  They are for
site customization.

* acas_configure_url: URL for the acas_configure package
* acas_configure_shasum: SHA256 Hash for the acas_configure RPM; this will be provided at the DISA download site
* audit_rules_shasum: SHA256 Hash for the audit.rules file within the `files/` folder; this will be provided at the DISA download site
* verify_repository_ssl: Whether or not to verify the SSL certificate for your local site repository web server

## Dependencies

None

## Example Playbook

This is a sample playbook for this role:

```yaml
---
- name: Deploy ACAS Server
  become: true
  become_method: sudo
  gather_facts: true
  hosts: acas
  roles:
    - role: acas
      acas_configure_url: https://repo.lab.test/repo/acas/acas_configure-23.03-2.noarch.rpm
      verify_repository_ssl: false
      grub2_password: '{{ vault_grub_password }}'
```

## License

MIT

## Author Information

Alex Ackerman, X @darkhonor
