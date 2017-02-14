ansible-role-chrony
==========================

Role for deploy chrony - a versatile implementation of the NTP.

Ansible versions
--------------------

Role is adapted for Ansible 2.0, should work on 1.9.

Extra
--------

Role mandatory stops services: 'ntpd', 'openntpd', 'systemd-timesyncd' and
delete ntpd.conf.

Example configuration
-------------------------

If you would like add server options (see `man chronyd`) just add to `opt` var
space separated.

This is mandatory option, chrony now ntp client:

```yaml
---
chrony:
- servers:
  - addr: '0.ru.pool.ntp.org'
    opt: 'iburst'
  - addr: '1.ru.pool.ntp.org'
    opt: 'iburst'
  - addr: '2.ru.pool.ntp.org'
    opt: 'iburst'
  - addr: '3.ru.pool.ntp.org'
    opt: 'iburst'
```

Another options is not mandatory. Also you can run chrony as ntp server, just
allow requests from some cidr's.

```yaml
---
chrony:
- servers:
  - addr: '0.ru.pool.ntp.org'
    opt: 'iburst'
  - addr: '1.ru.pool.ntp.org'
    opt: 'iburst'
  - addr: '2.ru.pool.ntp.org'
    opt: 'iburst'
  - addr: '3.ru.pool.ntp.org'
    opt: 'iburst'
  maxupdateskew: 5
  rtconutc: 'true'
  rtcsync: 'true'
  keyfile: '/etc/chrony.keys'
  makestep:
  - threshold: 10
    limit: 1
  allow:
  - 192.168.0.0/16
  - 198.18.0.0/15
  - 172.16.0.0/12
  - 10.0.0.0/8
```
