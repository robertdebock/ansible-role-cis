# [cis](#cis)

Apply and/or check recommendations from the CIS benchmarks.

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-cis.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-cis)|[![github](https://github.com/robertdebock/ansible-role-cis/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-cis/actions)|[![quality](https://img.shields.io/ansible/quality/49856)](https://galaxy.ansible.com/robertdebock/cis)|[![downloads](https://img.shields.io/ansible/role/d/49856)](https://galaxy.ansible.com/robertdebock/cis)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-cis.svg)](https://github.com/robertdebock/ansible-role-cis/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.cis
      # 1.1.5 Ensure noexec option set on /tmp partition (Scored)
      # `noexec` can't be set in a container.
      cis_tmp_noexec: no

      # 1.1.6 Ensure separate partition exists for /var (Scored)
      # Can't create a partition in CI.
      cis_var_partition: no

      # 1.1.7 Ensure separate partition exists for /var/tmp (Scored)
      # Can't create a partition in CI.
      cis_var_tmp_partition: no

      # 1.1.11 Ensure separate partition exists for /var/log (Scored)
      # Can't create a partition in CI.
      cis_var_log_partition: no

      # 1.1.12 Ensure separate partition exists for /var/log/audit (Scored)
      # Can't create a partition in CI.
      cis_var_log_audit_partition: no

      # 1.1.13 Ensure separate partition exists for /home (Scored)
      # Can't create a partition in CI.
      cis_home_partition: no

      # 1.1.23 Disable USB Storage (Scored)
      # Modprobe does not work in containers.
      cis_usb_storage_disabled: no

      # 1.5.1 Ensure permissions on bootloader config are configured (Scored)
      # No bootloaders in a container.
      cis_permissions_bootloader: no

      # 1.5.2 Ensure bootloader password is set (Scored)
      # No bootloaders in a container.
      cis_bootloader_password_set: no

      # 1.7.1.2 Ensure SELinux is not disabled in bootloader configuration (Scored)
      # No bootloaders in a container.
      cis_selinux_not_disabled: no

      # 1.7.1.3 Ensure SELinux policy is configured (Scored)
      # Can't change an SELinux policy in a container.
      cis_selinux_policy_configured: no

      # 1.7.1.4 Ensure the SELinux state is enforcing (Scored)
      # Can't change an SELinux state in a container.
      cis_selinux_state_enforcing: no

      # 3.3.1 Ensure DCCP is disabled (Scored)
      # can't load modules in a container.
      cis_dccp_disabled: no
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: prepare
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.cron
    - role: robertdebock.update
```

For verification `molecule/resources/verify.yml` run after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  tasks:
    # Molecules verifier does not load variables in default and vars. These
    # tasks load the variables explicitly.
    - name: load vars/main.yml
      include_vars: ../../vars/main.yml

    - name: load defaults/main.yml
      include_vars: ../../defaults/main.yml

    # In verify (in CI), certain overwrites must occur because not all resources
    # are available in a container, like kernel modules or partitions.
    - name: load verifier overwrites
      include_vars: defaults.yml

    - name: include distribution specific version
      include: "{{ ansible_distribution ~ '-' ~ ansible_distribution_major_version }}/verify.yml"
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for cis

# The CIS guidelines determines many settings of a system. The values used in
# this file will make a system compliant to the CIS specifications.
# There are many reasons why you do not want to adhere to one or more specific
# rules. You can overwrite values in you group_vars, host_vars, inventory or
# playbook.

# 1.1.1.1 Ensure mounting of cramfs filesystems is disabled (Scored)
cis_cramfs_disabled: yes

# 1.1.1.2 Ensure mounting of vFAT filesystems is limited (Not Scored)
cis_vfat_disabled: yes

# 1.1.1.3 Ensure mounting of squashfs filesystems is disabled (Scored)
cis_squashfs_disabled: yes

# 1.1.1.4 Ensure mounting of udf filesystems is disabled (Scored)
cis_udf_disabled: yes

# 1.1.2 Ensure /tmp is configured (Scored)
cis_tmp_configured: yes

# 1.1.3 Ensure nodev option set on /tmp partition (Scored)
cis_tmp_nodev: yes

# 1.1.4 Ensure nosuid option set on /tmp partition (Scored)
cis_tmp_nosuid: yes

# 1.1.5 Ensure noexec option set on /tmp partition (Scored)
cis_tmp_noexec: yes

# 1.1.6 Ensure separate partition exists for /var (Scored)
cis_var_partition: yes

# 1.1.7 Ensure separate partition exists for /var/tmp (Scored)
cis_var_tmp_partition: yes

# 1.1.8 Ensure nodev option set on /var/tmp partition (Scored)
cis_var_tmp_nodev: yes

# 1.1.9 Ensure nosuid option set on /var/tmp partition (Scored)
cis_var_tmp_nosuid: yes

# 1.1.10 Ensure noexec option set on /var/tmp partition (Scored)
cis_var_tmp_noexec: yes

# 1.1.11 Ensure separate partition exists for /var/log (Scored)
cis_var_log_partition: yes

# 1.1.12 Ensure separate partition exists for /var/log/audit (Scored)
cis_var_log_audit_partition: yes

# 1.1.13 Ensure separate partition exists for /home (Scored)
cis_home_partition: yes

# 1.1.14 Ensure nodev option set on /home partition (Scored)
cis_home_nodev: yes

# 1.1.15 Ensure nodev option set on /dev/shm partition (Scored)
cis_dev_shm_nodev: yes

# 1.1.16 Ensure nosuid option set on /dev/shm partition (Scored)
cis_dev_shm_nosuid: yes

# 1.1.17 Ensure noexec option set on /dev/shm partition (Scored)
cis_dev_shm_noexec: yes

# 1.1.18 Ensure nodev option set on removable media partitions (Not Scored)
cis_removable_media_nodev: yes

# 1.1.19 Ensure nosuid option set on removable media partitions (Not Scored)
cis_removable_media_nosuid: yes

# 1.1.20 Ensure noexec option set on removable media partitions (Not Scored)
cis_removable_media_noexec: yes

# 1.1.21 Ensure sticky bit is set on all world-writable directories (Scored)
cis_fix_sticky_bit: yes

# 1.1.22 Disable Automounting
cis_disable_automount: yes

# 1.1.23 Disable USB Storage (Scored)
cis_usb_storage_disabled: yes

# 1.2.1 Ensure GPG keys are configured (Not Scored)
cis_gpg_keys_configured: yes

# 1.2.2 Ensure gpgcheck is globally activated (Scored)
cis_gpgcheck_enabled: yes

# 1.2.3 Ensure package manager repositories are configured (Not Scored)
cis_repositories_configured: yes

# 1.3.1 Ensure sudo is installed (Scored)
cis_sudo_installed: yes

# 1.3.2 Ensure sudo commands use pty (Scored)
cis_sudo_use_pty: yes

# 1.3.3 Ensure sudo log file exists (Scored)
cis_sudo_logfile: yes

# 1.4.1 Ensure AIDE is installed (Scored)
cis_aide_installed: yes

# 1.4.2 Ensure filesystem integrity is regularly checked (Scored)
cis_filesystem_integrity_checked: yes

# 1.5.1 Ensure permissions on bootloader config are configured (Scored)
cis_permissions_bootloader: yes

# 1.5.2 Ensure bootloader password is set (Scored)
cis_bootloader_password_set: yes
cis_bootloader_password: changeme

# 1.5.3 Ensure authentication required for single user mode (Scored)
cis_authentication_single_user_mode: yes

# 1.6.1 Ensure core dumps are restricted (Scored)
cis_core_dumps_restricted: yes

# 1.6.2 Ensure address space layout randomization (ASLR) is enabled (Scored)
cis_aslr_enabled: yes

# 1.7.1.1 Ensure SELinux is installed (Scored)
cis_selinux_installed: yes

# 1.7.1.2 Ensure SELinux is not disabled in bootloader configuration (Scored)
cis_selinux_not_disabled: yes

# 1.7.1.3 Ensure SELinux policy is configured (Scored)
cis_selinux_policy_configured: yes
cis_selinux_policy: targeted

# 1.7.1.4 Ensure the SELinux state is enforcing (Scored)
cis_selinux_state_enforcing: yes

# 1.7.1.5 Ensure no unconfined services exist (Scored)
cis_no_unconfined_services: yes

# 1.7.1.6 Ensure SETroubleshoot is not installed (Scored)
cis_setroubleshoot_not_installed: yes

# 1.7.1.7 Ensure the MCS Translation Service (mcstrans) is not installed (Scored)
cis_mcs_translation_service_not_installed: yes

# 1.8.1.1 Ensure message of the day is configured properly (Scored)
cis_message_of_the_day_configured: yes
cis_message_of_the_day: |
  UNAUTHORIZED ACCESS TO THIS DEVICE IS PROHIBITED
  
  You must have explicit, authorized permission to access or configure this device. Unauthorized attempts and actions to access or use this system may result in civil and/or criminal penalties. All activities performed on this device are logged and monitored.

# 1.8.1.2 Ensure local login warning banner is configured properly (Scored)
cis_local_login_banner_configured: yes

# 1.8.1.3 Ensure remote login warning banner is configured properly (Scored)
cis_remote_login_banner_configured: yes

# 1.8.1.4 Ensure permissions on /etc/motd are configured (Scored)
cis_permissions_etc_motd: yes

# 1.8.1.5 Ensure permissions on /etc/issue are configured (Scored)
cis_permissions_etc_issue: yes

# 1.8.1.6 Ensure permissions on /etc/issue.net are configured (Scored)
cis_permissions_etc_issue_net: yes

# 1.8.2 Ensure GDM login banner is configured (Scored)
cis_gdm_login_banner_configured: yes

# 1.9 Ensure updates, patches, and additional security software are installed (Not Scored)
cis_updates_installed: yes

# 1.10 Ensure system-wide crypto policy is not legacy (Scored)
cis_crypto_policy_not_legacy: yes
cis_crypto_policy: FIPS

# 1.11 Ensure system-wide crypto policy is FUTURE or FIPS (Scored)
cis_ensure_crypto_policy: yes

# 2.1.1 Ensure xinetd is not installed (Scored)
cis_xinet_not_installed: yes

# 2.2.1.1 Ensure time synchronization is in use (Not Scored)
cis_time_synchronization: yes

# 2.2.1.2 Ensure chrony is configured (Scored)
cis_chrony_configured: yes
cis_chrony_servers: []
cis_chrony_pools:
  - name: 2.fedora.pool.ntp.org
    options: iburst

# 2.2.2 Ensure X Window System is not installed (Scored)
cis_x_windows_system_not_installed: yes

# 2.2.3 Ensure rsync service is not enabled (Scored)
cis_rsync_service_not_enabled: yes

# 2.2.4 Ensure Avahi Server is not enabled (Scored)
cis_avahi_server_not_enabled: yes

# 2.2.5 Ensure SNMP Server is not enabled (Scored)
cis_snmp_server_not_enabled: yes

# 2.2.6 Ensure HTTP Proxy Server is not enabled (Scored)
cis_http_proxy_server_not_enabled: yes

# 2.2.7 Ensure Samba is not enabled (Scored)
cis_samba_server_not_enabled: yes

# 2.2.8 Ensure IMAP and POP3 server is not enabled (Scored)
cis_imap_and_pop3_server_not_enabled: yes

# 2.2.9 Ensure HTTP server is not enabled (Scored)
cis_http_server_not_enabled: yes

# 2.2.10 Ensure FTP Server is not enabled (Scored)
cis_ftp_server_not_enabled: yes

# 2.2.11 Ensure DNS Server is not enabled (Scored)
cis_dns_server_not_enabled: yes

# 2.2.12 Ensure NFS is not enabled (Scored)
cis_nfs_server_not_enabled: yes

# 2.2.13 Ensure RPC is not enabled (Scored)
cis_rpc_not_enabled: yes

# 2.2.14 Ensure LDAP server is not enabled (Scored)
cis_ldap_server_not_enabled: yes

# 2.2.15 Ensure DHCP Server is not enabled (Scored)
cis_dhcp_server_not_enabled: yes

# 2.2.16 Ensure CUPS is not enabled (Scored)
cis_cups_not_enabled: yes

# 2.2.17 Ensure NIS Server is not enabled (Scored)
cis_nis_server_not_enabled: yes

# 2.2.18 Ensure mail transfer agent is configured for local-only mode (Scored)
cis_mta_local_only_mode: yes

# 2.3.1 Ensure NIS Client is not installed (Scored)
cis_nis_client_not_installed: yes

# 2.3.2 Ensure telnet client is not installed (Scored)
cis_telnet_client_not_installed: yes

# 2.3.3 Ensure LDAP client is not installed (Scored)
cis_ldap_client_not_installed: yes

# 3.1.1 Ensure IP forwarding is disabled (Scored)
cis_ip_forwarding_disabled: yes

# 3.1.2 Ensure packet redirect sending is disabled (Scored)
cis_packet_redirect_sending_disabled: yes

# 3.2.1 Ensure source routed packets are not accepted (Scored)
cis_source_routed_packets_not_accepted: yes

# 3.2.2 Ensure ICMP redirects are not accepted (Scored)
cis_icmp_redirects_not_accepted: yes

# 3.2.3 Ensure secure ICMP redirects are not accepted (Scored)
cis_secure_icmp_redirects_not_accepted: yes

# 3.2.4 Ensure suspicious packets are logged (Scored)
cis_suspicious_packets_logged: yes

# 3.2.5 Ensure broadcast ICMP requests are ignored (Scored)
cis_broadcast_icmp_requests_ignored: yes

# 3.2.6 Ensure bogus ICMP responses are ignored (Scored)
cis_bogus_icmp_responses_ignored: yes

# 3.2.7 Ensure Reverse Path Filtering is enabled (Scored)
cis_reverse_path_filtering: yes

# 3.2.8 Ensure TCP SYN Cookies is enabled (Scored)
cis_tcp_syn_cookies_enabled: yes

# 3.2.9 Ensure IPv6 router advertisements are not accepted (Scored)
cis_ipv6_router_advertisements_not_accepted: yes

# 3.3.1 Ensure DCCP is disabled (Scored)
cis_dccp_disabled: yes

# 3.3.2 Ensure SCTP is disabled (Scored)
cis_sctp_disabled: yes

# 3.3.3 Ensure RDS is disabled (Scored)
cis_rds_disabled: yes

# 3.3.4 Ensure TIPC is disabled (Scored)
cis_tipc_disabled: yes

# 3.4.1.1 Ensure a Firewall package is installed (Scored)
cis_firewall_package_installed: yes
cis_firewall_package: firewalld
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.cron
- robertdebock.update

```

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/cis.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|el|8|
|ubuntu|focal|

The minimum version of Ansible required is 2.8 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.



## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-cis) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-cis/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## [License](#license)

Apache-2.0

## [Contributors](#contributors)

I'd like to thank everybody that made contributions to this repository. It motivates me, improves the code and is just fun to collaborate.

- [arturmartins](https://github.com/arturmartins)

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
