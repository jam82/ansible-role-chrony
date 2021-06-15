# ansible-role-chrony

![GitHub](https://img.shields.io/github/license/jam82/ansible-role-chrony) ![GitHub last commit](https://img.shields.io/github/last-commit/jam82/ansible-role-chrony) ![GitHub issues](https://img.shields.io/github/issues-raw/jam82/ansible-role-chrony) [![Molecule](https://github.com/jam82/ansible-role-chrony/actions/workflows/molecule.yml/badge.svg)](https://github.com/jam82/ansible-role-chrony/actions/workflows/molecule.yml)

**Ansible role for setting up chrony.**

> ATTENTION: This role uninstalls `ntp` and masks `systemd-timesyncd`.

## Supported Platforms

| OS Family | Distribution  | Latest | Supported Version(s) | Comment |
|-----------|---------------|--------|----------------------|---------|
| Alpine    | Alpine        | :heavy_check_mark: | 3.11, 3.12, 3.13 | |
| Archlinux | Archlinux     | :heavy_check_mark: | - | |
|           | Manjaro       | :heavy_check_mark: | - | |
| Debian    | Debian        | :heavy_check_mark: | 10, 11 | |
|           | Ubuntu        | :heavy_check_mark: | 18.04, 20.04 | |
| RedHat    | Almalinux     | :heavy_check_mark: | 8 | |
|           | Amazonlinux   | :x: | - | not tested, image not working |
|           | Centos        | :heavy_check_mark: | 7, 8 | |
|           | Fedora        | :heavy_check_mark: | 33, 34, Rawhide | |
|           | Oraclelinux   | :heavy_check_mark: | 7, 8 | |
| Suse      | OpenSuse Leap | :heavy_check_mark: | 15.1, 15.2, 15.3 | |
|           | Tumbleweed    | :heavy_check_mark: | - | |

## Requirements

Ansible 2.9 or higher.

## Variables

Variables and defaults for this role.

### defaults/main/client.yml

### Cient defaults

```yaml
---
# role: ansible-role-chrony
# file: defaults/main.yml

# The role is disabled by default, so you do not get in trouble.
# Checked in tasks/main.yml which includes tasks.yml if enabled.
chrony_role_enabled: false

# List of allow/deny subnet directives. Order matters.
# see: https://chrony.tuxfamily.org/doc/3.4/chrony.conf.html allow/deny.
# This list is simply inserted as string in the conf file int the given order.
# EXAMPLE:
# allow all overrides the preceeding deny rule, so be careful.
# chrony_directives:
#   - allow 1.2.3.4
#   - deny 1.2.3
#   - allow all 1.2
chrony_directives: []

# Port 0 completely disables the ntp server functionality.
# Port 123 is the ntp default port, if you want ntp server functionality.
chrony_port: 0

# binds the socket on which chronyd listens for NTP requests.
# Not useful with multiple interfaces.
chrony_bindaddress: ""

# Bind chronyc only to local interface by default.
# NOTE: This only has any effect if cmdport > 0.
chrony_bindcmdaddress: 127.0.0.1

# Disable chronyc/command port.
# Only root and chrony user can access it via socket.
chrony_cmdport: 0

# List of subnets from which requests are denied.
chrony_deny: []

# Location of the drift file.
chrony_driftfile: /var/lib/chrony/chrony.drift

# This will reduce the time to catch up if chronyd is restarted,
# as it saves its state to a directory.
chrony_dumponexit: true

chrony_dumpdir: /var/lib/chrony

# List of network interfaces for hardware timestamping.
# Capabilities SOF_TIMESTAMPING_TX_HARDWARE,
# SOF_TIMESTAMPING_TX_SOFTWARE, HWTSTAMP_FILTER_ALL are needed.
# Check with:
# test $(ethtool -T {{ item }} | grep 'SOF_TIMESTAMPING_TX_HARDWARE\|SOF_TIMESTAMPING_TX_SOFTWARE\|HWTSTAMP_FILTER_ALL' | wc -l) == 3 && echo OK || echo NOK
chrony_hwtimestamp_interfaces: []

# If the system timezone database is kept up to date and includes the
# right/UTC timezone, chronyd can use it to determine the current
# TAI-UTC offset and when will the next leap second occur.
chrony_leapsectz: right/UTC

# Allow stepping. Useful for VMs that can be suspended.
# This allows stepping during the first 3 updates if the clock
# is more than 1 second away from the ntp server.
# NOTE: This results in "makestep 1 3" in chrony.conf file.
chrony_makestep_secs: 1

chrony_makestep_nums: 3

# List of NTP Pools with option list.
# see https://chrony.tuxfamily.org/faq.html for options and faq.
chrony_ntp_pools:
  - address: 0.de.pool.ntp.org
    options:
      - iburst
  - address: 1.de.pool.ntp.org
    options:
      - iburst
  - address: 2.de.pool.ntp.org
    options:
      - iburst
  - address: 3.de.pool.ntp.org
    options:
      - iburst

# List of NTP Servers with option list.
chrony_ntp_servers: []
# following options are only for LAN, a public ntp could block your
# for sending too many requests.
#  - address: 192.168.1.1
#    options:
#      - "minpoll 2"
#      - "maxpoll 4"
#      - "polltarget 30"
#      - iburst
#      - xleave

# Location of samba ntp signd socket dir when running as domain controller.
# chrony_ntpsigndsocket: "/var/lib/samba/ntp_signd"
chrony_ntpsigndsocket: ""

# Enables response rate limiting for NTP packets (interval|burst|leak).
# interval: power of 2 in seconds, default 3 = 8s, min = -19, max = 12.
# burst: number of responses in burst, default = 8, min = 1, max = 255.
# leak: randomly allowed packets if interval/burst limits exceeded,
#       power of 1/2, default 2, min = 1, max = 4.
# chrony_ratelimit: "interval 1 burst 16"
chrony_ratelimit: ""

# RealTime Clock in UTC
chrony_rtconutc: true

# Sync your RTC wit NTP
chrony_rtcsync: true
```

### Server defaults

Not implemented yet.

## Dependencies

None.

## Example Playbook

This will configure a client to use the first four *.de.pool.ntp.org servers.

```yaml
---
# role: ansible-role-chrony
# file: site.yml

- hosts: chrony_systems
  become: true
  gather_facts: true
  vars:
    chrony_role_enabled: true
  roles:
    - role: ansible-role-chrony
```

## License and Author

- Author:: [jam82](https://github.com/jam82/)
- Copyright:: 2020, [jam82](https://github.com/jam82/)

Licensed under [MIT License](https://opensource.org/licenses/MIT).
See [LICENSE](https://github.com/jam82/ansible-role-chrony/blob/master/LICENSE) file in repository.

## References

- [Red Hat - Understanding chrony and Its Configuration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-understanding_chrony_and-its_configuration)
- [Red Hat - Using the Chrony suite to configure NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)
- [chrony - Documentation](https://chrony.tuxfamily.org/documentation.html)
