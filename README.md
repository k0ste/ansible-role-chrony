# chrony

Role for deploy chrony - a versatile implementation of the NTP.

## Requirements

* Ansible 3.0.0+;

## Example configuration

```yaml
---
chrony:
# Enable chrony service or not
- enable: 'true'
# Restart chrony service after deploy or not
  restart: 'true'
# Install chrony package or not
  install_package: 'true'
# 'present' (do nothing if package is already installed) or 'latest' (always
# upgrade to last version)
  package_state: 'latest'
  settings:
# The server directive specifies an NTP server which can be used as a time
# source. The client-server relationship is strictly hierarchical - a client
# might synchronise its system time to that of the server, but the server’s
# system time will never be influenced by that of a client.
# The server directive is immediately followed by either the name of the server,
# or its IP address. The server directive supports the following options:
  - server:
    - address: '192.168.0.193'
# 'minpoll' - this option specifies the minimum interval between requests sent to
# the server as a power of 2 in seconds. For example, 'minpoll 5' would mean
# that the polling interval should not drop below 32 seconds. The default is '6'
# (64 seconds), the minimum is '-6' (1/64th of a second), and the maximum is
# '24' (6 months). Note that intervals shorter than '6' (64 seconds) should
# generally not be used with public servers on the Internet, because it might
# be considered abuse. A sub-second interval will be enabled only when the
# server is reachable and the round-trip delay is shorter than 10 milliseconds,
# i.e. the server should be in a local network.
      minpoll: '6'
# 'maxpoll' - this option specifies the maximum interval between requests sent to
# the server as a power of 2 in seconds. For example, 'maxpoll 9' indicates that
# the polling interval should stay at or below '9' (512 seconds). The default
# is '10' (1024 seconds), the minimum is '-6' (1/64th of a second), and the
# maximum is 24 (6 months).
      maxpoll: '10'
# 'iburst' - with this option, the interval between the first four requests sent
# to the server will be 2 seconds or less instead of the interval specified by
# the 'minpoll' option, which allows chronyd to make the first update of the
# clock shortly after start.
      iburst: 'true'
# 'burst' - with this option, chronyd will shorten the interval between up to
# four requests to 2 seconds or less when it cannot get a good measurement from
# the server. The number of requests in the burst is limited by the current
# polling interval to keep the average interval at or above the minimum
# interval, i.e. the current interval needs to be at least two times longer
# than the minimum interval in order to allow a burst with two requests.
      burst: 'false'
# 'key' - the NTP protocol supports a message authentication code (MAC) to
# prevent computers having their system time upset by rogue packets being sent
# to them. The MAC is generated as a function of a password specified in the key
# file, which is specified by the 'keyfile' directive. The key option specifies
# which key (with an ID in the range 1 through 2^32-1) should chronyd use to
# authenticate requests sent to the server and verify its responses. The server
# must have the same key for this number configured, otherwise no relationship
# between the computers will be possible. If the server is running ntpd and the
# output size of the hash function used by the key is longer than 160 bits
# (e.g. SHA256), the version option needs to be set to 4 for compatibility.
      key: ''
# 'maxdelay' - chronyd uses the network round-trip delay to the server to
# determine how accurate a particular measurement is likely to be. Lon
# round-trip delays indicate that the request, or the response, or both were
# delayed. If only one of the messages was delayed the measurement error is
# likely to be substantial. For small variations in the round-trip delay,
# chronyd uses a weighting scheme when processing the measurements. However,
# beyond a certain level of delay the measurements are likely to be so
# corrupted as to be useless. (This is particularly so on dial-up or other slow
# links, where a long delay probably indicates a highly asymmetric delay caused
# by the response waiting behind a lot of packets related to a download of some
# sort). If the user knows that round trip delays above a certain level should
# cause the measurement to be ignored, this level can be defined with the
# 'maxdelay' option. For example, maxdelay 0.3 would indicate that measurements
# with a round-trip delay of 0.3 seconds or more should be ignored. The default
# value is '3' seconds and the maximum value is '1000' seconds.
      maxdelay: '3'
# 'maxdelayratio' - this option is similar to the 'maxdelay' option. chronyd
# keeps a record of the minimum round-trip delay amongst the previous
# measurements that it has buffered. If a measurement has a round trip delay
# that is greater than the 'maxdelayratio' times the minimum delay, it will be
# rejected.
      maxdelayratio: ''
# 'maxdelaydevratio' - if a measurement has a ratio of the increase in the
# round-trip delay from the minimum delay amongst the previous measurements to
# the standard deviation of the previous measurements that is greater than the
# specified ratio, it will be rejected. The default is '10.0'.
      maxdelaydevratio: '10.0'
# 'mindelay' - this option specifies a fixed minimum round-trip delay to be used
# instead of the minimum amongst the previous measurements. This can be useful
# in networks with static configuration to improve the stability of corrections
# for asymmetric jitter, weighting of the measurements, and the 'maxdelayratio'
# and 'maxdelaydevratio' tests. The value should be set accurately in order to
# have a positive effect on the synchronisation.
      mindelay: ''
# 'asymmetry' - this option specifies the asymmetry of the network jitter on
# the path to the source, which is used to correct the measured offset
# according to the delay. The asymmetry can be between '-0.5' and '+0.5'. A
# negative value means the delay of packets sent to the source is more variable
# than the delay of packets sent from the source back. By default, chronyd
# estimates the asymmetry automatically.
      asymmetry: ''
# 'offset' - this option specifies a correction (in seconds) which will be
# applied to offsets measured with this source. It’s particularly useful to
# compensate for a known asymmetry in network delay or timestamping errors.
# For example, if packets sent to the source were on average delayed by 100
# microseconds more than packets sent from the source back, the correction
# would be -0.00005 (-50 microseconds). The default is '0.0'.
      offset: '0.0'
# 'minsamples' - set the minimum number of samples kept for this source. This
# overrides the minsamples directive.
      minsamples: ''
# 'maxsamples' - set the maximum number of samples kept for this source. This
# overrides the maxsamples directive.
      maxsamples: ''
# 'filter' - this option enables a median filter to reduce noise in NTP
# measurements. The filter will reduce the specified number of samples to a
# single sample. It is intended to be used with very short polling intervals in
# local networks where it is acceptable to generate a lot of NTP traffic.
      filter: ''
# 'offline' - if the server will not be reachable when chronyd is started, the
# offline option can be specified. chronyd will not try to poll the server
# until it is enabled to do so (by using the online command in chronyc).
      offline: 'false'
# 'auto_offline' - with this option, the server will be assumed to have gone
# offline when sending a request fails, e.g. due to a missing route to the
# network. This option avoids the need to run the offline command from chronyc
# when disconnecting the network link. (It will still be necessary to use the
# online command when the link has been established, to enable measurements to
# start.)
      auto_offline: 'false'
# 'prefer' - prefer this source over sources without the prefer option.
      prefer: 'false'
# 'noselect' - never select this source. This is particularly useful for
# monitoring.
      noselect: 'false'
# 'trust' - assume time from this source is always true. It can be rejected as
# a falseticker in the source selection only if another source with this option
# does not agree with it.
      trust: 'false'
# 'require' - require that at least one of the sources specified with this
# option is selectable (i.e. recently reachable and not a falseticker) before
# updating the clock. Together with the trust option this might be useful to
# allow a trusted authenticated source to be safely combined with
# unauthenticated sources in order to improve the accuracy of the clock. They
# can be selected and used for synchronisation only if they agree with the
# trusted and required source.
      require: ''
# 'xleave' - this option enables an interleaved mode which allows the server or
# the peer to send transmit timestamps captured after the actual transmission
# (e.g. when the server or the peer is running chronyd with software (kernel)
# or hardware timestamping). This can significantly improve the accuracy of the
# measurements. The interleaved mode is compatible with servers that support
# only the basic mode, but peers must both support and have enabled the
# interleaved mode, otherwise the synchronisation will work only in one
# direction. Note that even servers that support the interleaved mode might
# respond in the basic mode as the interleaved mode requires the servers to
# keep some state for each client and the state might be dropped when there are
# too many clients (e.g. 'clientloglimit' is too small), or it might be
# overwritten by other clients that have the same IP address (e.g. computers
# behind NAT or someone sending requests with a spoofed source address).
# The 'xleave' option can be combined with the presend option in order to
# shorten the interval in which the server has to keep the state to be able to
# respond in the interleaved mode.
      xleave: ''
# 'polltarget' - target number of measurements to use for the regression
# algorithm which chronyd will try to maintain by adjusting the polling
# interval between minpoll and maxpoll. A higher target makes chronyd prefer
# shorter polling intervals. The default is '8' and a useful range is from
# '6' to '60'.
      polltarget: ''
# 'port' - this option allows the UDP port on which the server understands NTP
# requests to be specified. For normal servers this option should not be
# required (the default is 123, the standard NTP port).
      port: '123'
# 'presend' - if the timing measurements being made by chronyd are the only
# network data passing between two computers, you might find that some
# measurements are badly skewed due to either the client or the server having
# to do an ARP lookup on the other party prior to transmitting a packet. This
# is more of a problem with long sampling intervals, which might be similar in
# duration to the lifetime of entries in the ARP caches of the machines. In
# order to avoid this problem, the presend option can be used. It takes a
# single integer argument, which is the smallest polling interval for which an
# extra pair of NTP packets will be exchanged between the client and the server
# prior to the actual measurement. When the polling interval is 512 seconds or
# more, an extra NTP client packet will be sent to the server a short time
# (2 seconds) before making the actual measurement. The presend option cannot
# be used in the peer directive. If it is used with the xleave option, chronyd
# will send two extra packets instead of one.
      presend: ''
# 'minstratum' - when the synchronisation source is selected from available
# sources, sources with lower stratum are normally slightly preferred. This
# option can be used to increase stratum of the source to the specified minimum,
# so chronyd will avoid selecting that source. This is useful with low stratum
# sources that are known to be unreliable or inaccurate and which should be
# used only when other sources are unreachable.
      minstratum: ''
# 'version' - this option sets the NTP version of packets sent to the server.
# This can be useful when the server runs an old NTP implementation that does
# not respond to requests using a newer version. The default version depends on
# whether a key is specified by the key option and which authentication hash
# function the key is using. If the output size of the hash function is longer
# than 160 bits, the default version is '3' for compatibility with older
# chronyd servers. Otherwise, the default version is '4'.
      version: '4'
    - address: '0.ru.pool.ntp.org'
      options: 'iburst'
    - address: '1.ru.pool.ntp.org'
      options: 'iburst'
    - address: '2.ru.pool.ntp.org'
      options: 'iburst'
    - address: '3.ru.pool.ntp.org'
      options: 'iburst'
# The syntax of this directive is similar to that for the 'server' directive,
# except that it is used to specify a pool of NTP servers rather than a single
# NTP server. The pool name is expected to resolve to multiple addresses which
# might change over time. All options valid in the 'server' directive can be
# used in this directive too. There is one option specific to the 'pool':
#
# 'maxsources' sets the maximum number of sources that can be used from the
# pool, the default value is '4'. On start, when the pool name is resolved,
# chronyd will add up to 16 sources, one for each resolved address. When the
# number of sources from which at least one valid reply was received reaches
# the number specified by the maxsources option, the other sources will be
# removed. When a pool source is unreachable, marked as a falseticker, or has a
# distance larger than the limit set by the 'maxdistance' directive, chronyd
# will try to replace the source with a newly resolved address from the pool.
    pool:
    - name: 'pool_name'
      address: 'pool.ntp.org'
      iburst: 'true'
      maxsources: '3'
# The syntax of this directive is identical to that for the 'server' directive,
# except that it specifies a symmetric association with an NTP peer instead of
# a client/server association with an NTP server. A single symmetric
# association allows the peers to be both servers and clients to each other.
# This is mainly useful when the NTP implementation of the peer (e.g. ntpd)
# supports ephemeral symmetric associations and does not need to be configured
# with an address of this host. chronyd does not support ephemeral associations.
# When a key is specified by the key option to enable authentication, both peers
# must use the same key and the same key number. Note that the symmetric mode is
# less secure than the client/server mode. A denial-of-service attack is
# possible on unauthenticated symmetric associations, i.e. when the peer was
# specified without the key option. An attacker who does not see network traffic
# between two hosts, but knows that they are peering with each other, can
# periodically send them unauthenticated packets with spoofed source addresses
# in order to disrupt their NTP state and prevent them from synchronising to
# each other. When the association is authenticated, an attacker who does see
# the network traffic, but cannot prevent the packets from reaching the other
# host, can still disrupt the state by replaying old packets. The attacker has
# effectively the same power as a man-in-the-middle attacker.
# A partial protection against this attack is implemented in chronyd, which can
# protect the peers if they are using the same polling interval and they never
# sent an authenticated packet with a timestamp from future, but it should not
# be relied on as it is difficult to ensure the conditions are met. If two
# hosts should be able to synchronise to each other in both directions, it is
# recommended to use two separate client/server associations (specified by the
# server directive on both hosts) instead.
    peer:
    - address: '5.128.220.1'
      iburst: 'true'
# In normal operation, chronyd slews the time when it needs to adjust the
# system clock. For example, to correct a system clock which is 1 second slow,
# chronyd slightly increases the amount by which the system clock is advanced
# on each clock interrupt, until the error is removed. Note that at no time
# does time run backwards with this method. On most Unix systems it is not
# desirable to step the system clock, because many programs rely on time
# advancing monotonically forwards. When the chronyd daemon is initially
# started, it is possible that the system clock is considerably in error.
# Attempting to correct such an error by slewing might not be sensible, since
# it might take several hours to correct the error by this means. The purpose
# of the initstepslew directive is to allow chronyd to make a rapid measurement
# of the system clock error at boot time, and to correct the system clock by
# stepping before normal operation begins. Since this would normally be
# performed only at an appropriate point in the system boot sequence, no other
# software should be adversely affected by the step.
# If the correction required is less than a specified threshold, a slew is used
# instead. This makes it safer to restart chronyd whilst the system is in
# normal operation. The 'initstepslew' directive takes a threshold and a list
# of NTP servers as arguments. Each of the servers is rapidly polled several
# times, and a majority voting mechanism used to find the most likely range of
# system clock error that is present. A step or slew is applied to the system
# clock to correct this error. chronyd then enters its normal operating mode. An
# example of the use of the directive is where 2 NTP servers are used to make
# the measurement. The '30' indicates that if the system’s error is found to be
# 30 seconds or less, a slew will be used to correct it, if the error is above
# 30 seconds, a step will be used. The 'initstepslew' directive can also be
# used in an isolated LAN environment, where the clocks are set manually. The
# most stable computer is chosen as the master, and the other computers are
# slaved to it. If each of the slaves is configured with the local directive,
# the master can be set up with an initstepslew directive which references some
# or all of the slaves. Then, if the master machine has to be rebooted, the
# slaves can be relied on to act analogously to a flywheel and preserve the
# time for a short period while the master completes its reboot. The
# 'initstepslew' directive is functionally similar to a combination of the
# 'makestep' and server directives with the 'iburst' option. The main
# difference is that the 'initstepslew' servers are used only before normal
# operation begins and that the foreground chronyd process waits for
# 'initstepslew' to finish before exiting. This is useful to prevent programs
# started in the boot sequence after chronyd from reading the clock before it
# has been stepped.
    initstepslew:
    - threshold: '30'
      address:
      - 'foo.example.net'
      - 'bar.example.net'
# The 'refclock' directive specifies a hardware reference clock to be used as a
# time source. It has two mandatory parameters, a driver name and a
# driver-specific parameter. The two parameters are followed by zero or more
# 'refclock' options. Some drivers have special options, which can be appended
# to the driver-specific parameter (separated by the ':' and ',' characters).
# There are drivers included in chronyd:
#
# 'PPS' - driver for the kernel PPS (pulse per second) API. The parameter is
# the path to the PPS device (typically /dev/pps?). As PPS refclocks do not
# supply full time, another time source (e.g. NTP server or non-PPS refclock)
# is needed to complete samples from the PPS refclock. An alternative is to
# enable the local directive to allow synchronisation with some unknown but
# constant offset. The driver supports the following option:
#
# 'clear' - by default, the PPS refclock uses assert events (rising edge) for
# synchronisation. With this option, it will use clear events (falling edge)
# instead.
#
# 'SHM' - NTP shared memory driver. This driver uses a shared memory segment to
# receive samples from another process (e.g. gpsd). The parameter 'path' is the
# number of the shared memory segment, typically a small number like 0, 1, 2,
# or 3.
# The driver supports the following option:
# 'perm' - this option specifies the permissions of the shared memory segment
# created by chronyd. They are specified as a numeric mode. The default value
# is '0600' (read-write access for owner only).
#
# 'SOCK' - Unix domain socket driver. It is similar to the SHM driver, but
# samples are received from a Unix domain socket instead of shared memory and
# the messages have a different format. The parameter is the path to the socket,
# which chronyd creates on start. An advantage over the SHM driver is that
# SOCK does not require polling and it can receive PPS samples with incomplete
# time. The format of the messages is described in the refclock_sock.c file in
# the chrony source code. An application which supports the SOCK protocol is
# the gpsd daemon. The path where gpsd expects the socket to be created is
# described in the gpsd man page.
#
# 'PHC' - PTP hardware clock (PHC) driver. The parameter is the path to the
# device of the PTP clock which should be used as a time source. If the clock
# is kept in TAI instead of UTC (e.g. it is synchronised by a PTP daemon), the
# current UTC-TAI offset needs to be specified by the offset option.
# Alternatively, the pps 'refclock' option can be enabled to treat the PHC as a
# PPS refclock, using only the sub-second offset for synchronisation. The
# driver supports the following options:
# 'nocrossts' - this option disables use of precise cross timestamping.
# 'extpps' - this option enables a PPS mode in which the PTP clock is
# timestamping pulses of an external PPS signal connected to the clock. The
# clock does not need to be synchronised, but another time source is needed to
# complete the PPS samples. Note that some PTP clocks cannot be configured to
# timestamp only assert or clear events, and it is necessary to use the width
# option to filter wrong PPS samples.
# 'pin' - this option specifies the index of the pin to which is connected the
# PPS signal. The default value is '0'.
# 'channel' - this option specifies the index of the channel for the PPS mode.
# The default value is '0'.
# 'clear' - this option enables timestamping of clear events (falling edge)
# instead of assert events (rising edge) in the PPS mode. This may not work
# with some clocks.
#
# The 'refclock' directive supports the following options:
#
# 'poll' - timestamps produced by refclock drivers are not used immediately, but
# they are stored and processed by a median filter in the polling interval
# specified by this option. This is defined as a power of 2 and can be negative
# to specify a sub-second interval. The default is 4 (16 seconds). A shorter
# interval allows chronyd to react faster to changes in the frequency of the
# system clock, but it might have a negative effect on its accuracy if the
# samples have a lot of jitter.
#
# 'dpoll' - some drivers do not listen for external events and try to produce
# samples in their own polling interval. This is defined as a power of 2 and
# can be negative to specify a sub-second interval.
# The default is '0' (1 second).
#
# 'refid' - this option is used to specify the reference ID of the refclock, as
# up to four ASCII characters. The default reference ID is composed from the
# first three characters of the driver name and the number of the 'refclock'.
# Each 'refclock' must have a unique reference ID.
#
# 'lock' - this option can be used to lock a PPS refclock to another 'refclock',
# which is specified by its reference ID. In this mode received PPS samples are
# paired directly with raw samples from the specified 'refclock'.
#
# 'rate' - this option sets the rate of the pulses in the PPS signal (in Hz).
# This option controls how the pulses will be completed with real time. To
# actually receive more than one pulse per second, a negative 'dpoll' has to be
# specified (-3 for a 5Hz signal). The default is '1'.
#
# 'maxlockage'
# This option specifies in number of pulses how old can be samples from the
# refclock specified by the lock option to be paired with the pulses.
# Increasing this value is useful when the samples are produced at a lower rate
# than the pulses. The default is '2'.
#
# 'width' - this option specifies the width of the pulses (in seconds). It is
# used to filter PPS samples when the driver provides samples for both rising
# and falling edges. Note that it reduces the maximum allowed error of the time
# source which completes the PPS samples. If the duty cycle is configurable,
# 50% should be preferred in order to maximise the allowed error.
#
# 'pps' - this options forces chronyd to treat any refclock (e.g. SHM or PHC)
# as a PPS refclock. This can be useful when the 'refclock' provides time with a
# variable offset of a whole number of seconds (e.g. it uses TAI instead of
# UTC). Another time source is needed to complete samples from the 'refclock'.
#
# 'offset' - this option can be used to compensate for a constant error. The
# specified offset (in seconds) is applied to all samples produced by the
# reference clock. The default is '0.0'.
#
# 'delay' - this option sets the NTP delay of the source (in seconds). Half of
# this value is included in the maximum assumed error which is used in the
# source selection algorithm. Increasing the delay is useful to avoid having no
# majority in the source selection or to make it prefer other sources. The
# default is 1e-9 (1 nanosecond).
#
# 'stratum' - this option sets the NTP stratum of the 'refclock'. This can be
# useful when the refclock provides time with a stratum other than 0.
# The default is '0'.
#
# 'precision' this option sets the precision of the reference clock
# (in seconds). The default value is the estimated precision of the system
# clock.
#
# 'maxdispersion'
# Maximum allowed dispersion for filtered samples (in seconds). Samples with
# larger estimated dispersion are ignored. By default, this limit is disabled.
#
# 'filter' - this option sets the length of the median filter which is used to
# reduce the noise in the measurements. With each poll about 40 percent of the
# stored samples are discarded and one final sample is calculated as an average
# of the remaining samples. If the length is 4 or more, at least 4 samples have
# to be collected between polls. For lengths below 4, the filter has to be full.
# The default is '64'.
#
# 'prefer' - Prefer this source over sources without the prefer option.
#
# 'noselect' - never select this source. This is useful for monitoring or with
# sources which are not very accurate, but are locked with a PPS refclock.
#
# 'trust' - assume time from this source is always true. It can be rejected as
# a falseticker in the source selection only if another source with this option
# does not agree with it.
#
# 'require' - require that at least one of the sources specified with this
# option is selectable (i.e. recently reachable and not a falseticker) before
# updating the clock. Together with the 'trust' option this can be useful to
# allow a trusted, but not very precise, reference clock to be safely combined
# with unauthenticated NTP sources in order to improve the accuracy of the
# clock. They can be selected and used for synchronisation only if they agree
# with the trusted and required source.
#
# 'tai' - this option indicates that the reference clock keeps time in TAI
# instead of UTC and that chronyd should correct its offset by the current
# TAI-UTC offset. The 'leapsectz' directive must be used with this option and
# the database must be kept up to date in order for this correction to work as
# expected. This option does not make sense with PPS refclocks.
#
# 'minsamples' - set the minimum number of samples kept for this source. This
# overrides the 'minsamples' directive.
#
# 'maxsamples' - set the maximum number of samples kept for this source. This
# overrides the 'maxsamples' directive.
    refclock:
    - driver: 'PPS'
      path: '/dev/pps0'
      driver_options:
      - 'clear'
      refid: 'GPS2'
      offset: '0.5'
      delay: '0.2'
    - driver: 'SHM'
      path: '0'
      driver_options:
      - 'perm=0644'
      poll: '3'
      refid: 'GPS1'
    - driver: 'PHC'
      path: '/dev/ptp0'
      driver_options:
      - 'extpps'
      - 'pin=1'
      width: '0.2'
      poll: '2'
# The 'manual' directive enables support at run-time for the settime command in
# chronyc. If no manual directive is included, any attempt to use the settime
# command in chronyc will be met with an error message. Note that the settime
# command can be enabled at run-time using the manual command in chronyc.
# The idea of the two commands is that the manual command controls the manual
# clock driver’s behaviour, whereas the settime command allows samples of
# manually entered time to be provided.)
    manual: 'false'
# By default, chronyd uses a separate client socket for each configured server
# and their source port is chosen arbitrarily by the operating system. However,
# you can use the acquisitionport directive to explicitly specify a port and
# use only one socket (per IPv4 or IPv6 address family) for all configured
# servers. This can be useful for getting through some firewalls. If set to '0',
# the source port of the socket will be chosen arbitrarily.
# It can be set to the same port as is used by the NTP server (which can be
# configured with the port directive) to use only one socket for all NTP
# packets.
    acquisitionport: '1123'
# The 'bindacqaddress' directive sets the network interface to which chronyd
# will bind its NTP client sockets. The syntax is similar to the 'bindaddress'
# and 'bindcmdaddress' directives.
# For each of the IPv4 and IPv6 protocols, only one 'bindacqaddress' directive
# can be specified.
    bindacqaddress: 'address'
# To compute the rate of gain or loss of time, chronyd has to store a
# measurement history for each of the time sources it uses. All supported
# systems, with the exception of macOS 10.12 and earlier, have operating system
# support for setting the rate of gain or loss to compensate for known errors.
# On macOS 10.12 and earlier, chronyd must simulate such a capability by
# periodically slewing the system clock forwards or backwards by a suitable
# amount to compensate for the error built up since the previous slew. For such
# systems, it is possible to save the measurement history across restarts of
# chronyd (assuming no changes are made to the system clock behaviour whilst it
# is not running). The dumpdir directive defines the directory where the
# measurement histories are saved when chronyd exits, or the dump command in
# chronyc is issued.
    dumpdir: '/var/run/chrony'
# The 'maxsamples' directive sets the default maximum number of samples that
# chronyd should keep for each source. This setting can be overridden for
# individual sources in the server and refclock directives. The default value
# is '0', which disables the configurable limit. The useful range is 4 to 64.
    maxsamples: '0'
# The 'minsamples' directive sets the default minimum number of samples that
# chronyd should keep for each source. This setting can be overridden for
# individual sources in the server and refclock directives. The default value
# is '6'. The useful range is 4 to 64. Forcing chronyd to keep more samples
# than it would normally keep reduces noise in the estimated frequency and
# offset, but slows down the response to changes in the frequency and offset of
# the clock. The offsets in the tracking and sourcestats reports (and the
# tracking.log and statistics.log files) may be smaller than the actual offsets.
    minsamples: '6'
# When chronyd has multiple sources available for synchronisation, it has to
# select one source as the synchronisation source. The measured offsets and
# frequencies of the system clock relative to the other sources, however, can
# be combined with the selected source to improve the accuracy of the
# system clock.
# The combinelimit directive limits which sources are included in the
# combining algorithm. Their synchronisation distance has to be shorter than the
# distance of the selected source multiplied by the value of the limit. Also,
# their measured frequencies have to be close to the frequency of the selected
# source. By default, the limit is '3'. Setting the limit to 0 effectively
# disables the source combining algorithm and only the selected source will be
# used to control the system clock.
    combinelimit: '3'
# The maxdistance directive sets the maximum allowed root distance of the
# sources to not be rejected by the source selection algorithm. The distance
# includes the accumulated dispersion, which might be large when the source is
# no longer synchronised, and half of the total round-trip delay to the primary
# source. By default, the maximum root distance is '3' seconds. Setting
# 'maxdistance' to a larger value can be useful to allow synchronisation with a
# server that only has a very infrequent connection to its sources and can
# accumulate a large dispersion between updates of its clock.
    maxdistance: '3'
# The maxjitter directive sets the maximum allowed jitter of the sources to not
# be rejected by the source selection algorithm. This prevents synchronisation
# with sources that have a small root distance, but their time is too variable.
# By default, the maximum jitter is 1 second.
    maxjitter: '1'
# The minsources directive sets the minimum number of sources that need to be
# considered as selectable in the source selection algorithm before the local
# clock is updated. The default value is '1'. Setting this option to a larger
# number can be used to improve the reliability. More sources will have to
# agree with each other and the clock will not be updated when only one source
# (which could be serving incorrect time) is reachable.
    minsources: '1'
# When chronyd selects a synchronisation source from available sources, it will
# prefer the one with the shortest synchronisation distance. However, to avoid
# frequent reselecting when there are sources with similar distance, a fixed
# distance is added to the distance for sources that are currently not selected.
# This can be set with the reselectdist directive. By default, the distance is
# '100' microseconds.
    reselectdist: '100'
# The 'stratumweight' directive sets how much distance should be added per
# stratum to the synchronisation distance when chronyd selects th
# synchronisation source from available sources. By default, the weight is
# '0.001' seconds. This means that the stratum of the sources in the selection
# process matters only when the differences between the distances are in
# milliseconds.
    stratumweight: '0.001'
# When chronyd is slewing the system clock to correct an offset, the rate at
# which it is slewing adds to the frequency error of the clock. On all
# supported systems, with the exception of macOS 12 and earlier, this rate can
# be controlled. The 'corrtimeratio' directive sets the ratio between the
# duration in which the clock is slewed for an average correction according to
# the source history and the interval in which the corrections are done
# (usually the NTP polling interval). Corrections larger than the average take
# less time and smaller corrections take more time, the amount of the
# correction and the correction time are inversely proportional. Increasing
# corrtimeratio improves the overall frequency error of the system clock, but
# increases the overall time error as the corrections take longer. By default,
# the ratio is set to '3', the time accuracy of the clock is preferred over its
# frequency accuracy. The maximum allowed slew rate can be set by the
# 'maxslewrate' directive. The current remaining correction is shown in the
# tracking report as the System time value.
    corrtimeratio: '3'
# One of the main activities of the chronyd program is to work out the rate at
# which the system clock gains or loses time relative to real time. Whenever
# chronyd computes a new value of the gain or loss rate, it is desirable to
# record it somewhere. This allows chronyd to begin compensating the system
# clock at that rate whenever it is restarted, even before it has had a chance
# to obtain an equally good estimate of the rate during the new run. This
# process can take many minutes, at least. The driftfile directive allows a
# file to be specified into which chronyd can store the rate information. Two
# parameters are recorded in the file. The first is the rate at which the
# system clock gains or loses time, expressed in parts per million, with gains
# positive. Therefore, a value of '100.0' indicates that when the system clock
# has advanced by a second, it has gained 100 microseconds in reality (so the
# true time has only advanced by 999900 microseconds). The second is an estimate
# of the error bound around the first value in which the true rate actually
# lies.
    driftfile: '/var/lib/chrony/drift'
# Fallback drifts are long-term averages of the system clock drift calculated
# over exponentially increasing intervals. They are used to avoid quickly
# drifting away from true time when the clock was not updated for a longer
# period of time and there was a short-term deviation in the drift before the
# updates stopped. The directive specifies the minimum and maximum interval
# since the last clock update to switch between fallback drifts. They are
# defined as a power of 2 (in seconds).
# In this example, the minimum interval is 16 (18 hours) and the maximum
# interval is 19 (6 days). The system clock frequency will be set to the first
# fallback 18 hours after last clock update, to the second after 36 hours, and
# so on. This might be a good setting to cover frequency changes due to daily
# and weekly temperature fluctuations. When the frequency is set to a fallback,
# the state of the clock will change to 'Not synchronised'. By default (or if
# the specified maximum or minimum is 0), no fallbacks are used and the clock
# frequency changes only with new easurements from NTP sources, reference
# clocks, or manual input.
    fallbackdrift: '16 19'
# A leap second is an adjustment that is occasionally applied to UTC to keep it
# close to the mean solar time. When a leap second is inserted, the last day of
# June or December has an extra second 23:59:60. For computer clocks that is a
# problem. The Unix time is defined as number of seconds since 00:00:00 UTC on
# 1 January 1970 without leap seconds. The system clock cannot have time
# 23:59:60, every minute has 60 seconds and every day has 86400 seconds by
# definition. The inserted leap second is skipped and the clock is suddenly
# ahead of UTC by one second. The leapsecmode directive selects how that error
# is corrected. There are options:
#
# 'system' - when inserting a leap second, the kernel steps the system clock
# backwards by one second when the clock gets to 00:00:00 UTC. When deleting a
# leap second, it steps forward by one second when the clock gets to
# 23:59:59 UTC. This is the default mode when the system driver supports leap
# seconds (i.e. all supported systems with the exception of macOS 12 and
# earlier).
#
# 'step' - this is similar to the 'system' mode, except the clock is stepped by
# chronyd instead of the kernel. It can be useful to avoid bugs in the kernel
# code that would be executed in the system mode. This is the default mode when
# the system driver does not support leap seconds.
#
# 'slew' - the clock is corrected by slewing started at 00:00:00 UTC when a leap
# second is inserted or 23:59:59 UTC when a leap second is deleted. This might
# be preferred over the system and step modes when applications running on the
# system are sensitive to jumps in the system time and it is acceptable that the
# clock will be off for a longer time. On Linux with the default 'maxslewrate'
# value the correction takes 12 seconds.
#
# 'ignore' - no correction is applied to the clock for the leap second. The
# clock will be corrected later in normal operation when new measurements are
# made and the estimated offset includes the one second error. When serving
# time to NTP clients that cannot be configured to correct their clocks for a
# leap second by slewing, or to clients that would correct at slightly different
# rates when it is necessary to keep them close together, the slew mode can be
# combined with the smoothtime directive to enable a server leap smear. When
# smearing a leap second, the leap status is suppressed on the server and the
# served time is corrected slowly be slewing instead of stepping. The clients
# do not need any special configuration as they do not know there is any leap
# second and they follow the server time which eventually brings them back to
# UTC. Care must be taken to ensure they use only NTP servers which smear the
# leap second in exactly the same way for synchronisation. This feature must be
# used carefully, because the server is intentionally not serving its best
# estimate of the true time.
    leapsecmode: 'slew'
# This directive specifies a timezone in the system tz database which chronyd
# can use to determine when will the next leap second occur and what is the
# current offset between TAI and UTC. It will periodically check if 23:59:59
# and 23:59:60 are valid times in the timezone. This typically works with the
# right/UTC timezone. When a leap second is announced, the timezone needs to be
# updated at least 12 hours before the leap second. It is not necessary to
# restart chronyd. This directive is useful with reference clocks and other time
# sources which do not announce leap seconds, or announce them too late for an
# NTP server to forward them to its own clients. Clients of leap smearing
# servers must not use this directive.
# It is also useful when the system clock is required to have correct TAI-UTC
# offset. Note that the offset is set only when leap seconds are handled by the
# kernel, i.e. leapsecmode is set to system.
# The specified timezone is not used as an exclusive source of information about
# leap seconds. If a majority of time sources announce on the last day of June
# or December that a leap second should be inserted or deleted, it will be
# accepted even if it is not included in the timezone.
    leapsectz: 'right/UTC'
# Normally chronyd will cause the system to gradually correct any time offset,
# by slowing down or speeding up the clock as required. In certain situations,
# the system clock might be so far adrift that this slewing process would take
# a very long time to correct the system clock. This directive forces chronyd to
# step the system clock if the adjustment is larger than a threshold value,
# but only if there were no more clock updates since chronyd was started than a
# specified limit (a negative value can be used to disable the limit). This is
# particularly useful when using reference clocks, because the initstepslew
# directive works only with NTP sources.
    makestep:
    - threshold: '10'
      limit: '1'
# This directive sets the maximum allowed offset corrected on a clock update.
# The check is performed only after the specified number of updates to allow a
# large initial adjustment of the system clock. When an offset larger than the
# specified maximum occurs, it will be ignored for the specified number of times
# and then chronyd will give up and exit (a negative value can be used to never
# exit). In both cases a message is sent to syslog. An example of the use of
# this directive is - after the first clock update, chronyd will check the
# offset on every clock update, it will ignore two adjustments larger
# than '1000' seconds and exit on another one.
    maxchange:
    - offset: '1000'
      start: '1'
      ignore: '2'
# The 'maxclockerror' directive sets the maximum assumed frequency error that
# the system clock can gain on its own between clock updates. It describes the
# stability of the clock. By default, the maximum error is '1' ppm.
# Typical values for error-in-ppm might be 10 for a low quality clock and 0.1
# for a high quality clock using a temperature compensated crystal oscillator.
    maxclockerror: '1'
# This directive specifies the maximum assumed drift (frequency error) of the
# system clock. It limits the frequency adjustment that chronyd is allowed to
# use to correct the measured drift. It is an additional limit to the maximum
# adjustment that can be set by the system driver (100000 ppm on Linux, 500 ppm
# on FreeBSD, NetBSD, and macOS 10.13+, 32500 ppm on Solaris). By default, the
# maximum assumed drift is '500000' ppm, i.e. the adjustment is limited by the
# system driver rather than this directive.
    maxdrift: '500000'
# One of chronyd’s tasks is to work out how fast or slow the computer’s clock
# runs relative to its reference sources. In addition, it computes an estimate
# of the error bounds around the estimated value. If the range of error is too
# large, it probably indicates that the measurements have not settled down yet,
# and that the estimated gain or loss rate is not very reliable.
# The 'maxupdateskew' directive sets the threshold for determining whether an
# estimate might be so unreliable that it should not be used. By default, the
# threshold is '1000' ppm.
# Typical values for skew-in-ppm might be 100 for a dial-up connection to
# servers over a phone line, and 5 or 10 for a computer on a LAN. It should be
# noted that this is not the only means of protection against using unreliable
# estimates. At all times, chronyd keeps track of both the estimated gain or
# loss rate, and the error bound on the estimate. When a new estimate is
# generated following another measurement from one of the sources, a weighted
# combination algorithm is used to update the master estimate. So if chronyd
# has an existing highly-reliable master estimate and a new estimate is
# generated which has large error bounds, the existing master estimate will
# dominate in the new master estimate.
    maxupdateskew: '1000'
# The 'maxslewrate' directive sets the maximum rate at which chronyd is allowed
# to slew the time. It limits the slew rate controlled by the correction time
# ratio (which can be set by the corrtimeratio directive) and is effective only
# on systems where chronyd is able to control the rate (i.e. all supported
# systems with the exception of macOS 12 or earlier). For each system there is a
# maximum frequency offset of the clock that can be set by the driver. On Linux
# it is 100000 ppm, on FreeBSD, NetBSD and macOS 10.13+ it is 5000 ppm, and on
# Solaris it is 32500 ppm. Also, due to a kernel limitation, setting
# 'maxslewrate' on FreeBSD, NetBSD, macOS 10.13+ to a value between 500 ppm and
# 5000 ppm will effectively set it to 500 ppm. In early beta releases of
# macOS 13 this capability is disabled because of a system kernel bug. When the
# kernel bug is fixed, chronyd will detect this and re-enable the capability
# (see above limitations) with no recompilation required. By default, the
# maximum slew rate is set to '83333.333' ppm (one twelfth).
    maxslewrate: '83333.333'
# This directive is used to designate a particular subnet from which NTP
# clients are allowed to access the computer as an NTP server.
# The default is that no clients are allowed access, i.e. chronyd operates
# purely as an NTP client. If the allow directive is used, chronyd will be both
# a client of its servers, and a server to other clients. Examples of the use
# of the directive are as follows:
    access_list:
# Allows a node with IPv4 address 1.2.3.4 to be an NTP client of this computer.
    - policy: 'allow'
      address: '1.2.3.4'
# Allows any node with an IPv4 address of the form 1.2.x.y (with x and y
# arbitrary) to be an NTP client of this computer.
    - policy: 'allow'
      address: '1.2'
# Allows any node with an IPv4 address of the form 3.4.5.x to have client
# NTP access.
    - policy: 'allow'
      address: '3.4.5'
# Allow access from any node with an IPv4 address of the form 6.7.8.x, 6.7.9.x,
# 6.7.10.x or 6.7.11.x (with x arbitrary), i.e. the value 22 is the number of
# bits defining the specified subnet.
    - policy: 'allow'
      address: '6.7.8/22'
# And here the final byte is ignored.
    - policy: 'allow'
      address: '6.7.8.9/22'
# Examle of allow IPv6 addresses.
    - policy: 'allow'
      address: '2001:db8::/32'
# Allow access to any ipv4 address.
    - policy: 'allow'
      address: '0/0'
# Allow access to any ipv6 address.
    - policy: 'allow'
      address: '::/0'
# Allow for all.
    - policy: 'allow'
      address: 'all'
# And example with 'deny':
# The effect is the same regardless of what order the three directives are
# given in. So the 1.2.x.y subnet is allowed access, except for the 1.2.3.x
# subnet, which is denied access, however the host 1.2.3.4 is allowed access.
    - policy: 'allow'
      address: '1.2.3.4'
    - policy: 'deny'
      address: '1.2.3'
    - policy: 'allow'
      address: '1.2'
# The 'bindaddress' directive binds the socket on which chronyd listens for NTP
# requests to a local address of the computer. On systems other than Linux, the
# address of the computer needs to be already configured when chronyd is
# started. Currently, for each of the IPv4 and IPv6 protocols, only one
# bindaddress directive can be specified. Therefore, it is not useful on
# computers which should serve NTP on multiple network interfaces.
    bindaddress: '192.168.1.1'
# The 'broadcast' directive is used to declare a broadcast address to which
# chronyd should send packets in the NTP broadcast mode (i.e. make chronyd act
# as a broadcast server). Broadcast clients on that subnet will be able to
# synchronise.
    broadcast:
# The interval in seconds between broadcast packets being sent.
    - interval: '30'
# The broadcast address to send the packet to. This should correspond to the
# broadcast address of one of the network interfaces on the computer where
# chronyd is running.
      address: '192.168.1.255'
    - interval: '60'
      address: '192.168.2.255'
# The destination port is specified as 12123.
      port: '12123'
    - interval: '60'
      address: 'ff02::101'
# 'clientloglimit' directive specifies the maximum amount of memory that
# chronyd is allowed to allocate for logging of client accesses and the state
# that chronyd as an NTP server needs to support the interleaved mode for its
# clients. The default limit is '524288' bytes, which is sufficient for
# monitoring about four thousand clients at the same time.
# In older chrony versions if the limit was set to '0', the memory allocation
# was unlimited.
    clientloglimit: '524288'
# Specifies that client accesses are not to be logged. Normally they are logged,
# allowing statistics to be reported using the 'clients' command in chronyc. This
# option also effectively disables server support for the NTP interleaved mode.
    noclientlog: 'false'
# The 'local' directive enables a local reference mode, which allows chronyd
# operating as an NTP server to appear synchronised to real time (from the
# viewpoint of clients polling it), even when it was never synchronised or the
# last update of the clock happened a long time ago. This directive is normally
# used in an isolated network, where computers are required to be synchronised
# to one another, but not necessarily to real time. The server can be kept
# vaguely in line with real time by manual input. The local directive has the
# following options:
#
# 'stratum' option sets the stratum of the server which will be reported to
# clients when the local reference is active. The specified value is in the
# range 1 through 15, and the default value is '10'. It should be larger than
# the maximum expected stratum in the network when external NTP servers are
# accessible. Stratum '1' indicates a computer that has a true real-time
# reference directly connected to it (e.g. GPS, atomic clock, etc.), such
# computers are expected to be very close to real time. Stratum '2' computers are
# those which have a stratum 1 server, stratum '3' computers have a stratum 2
# server and so on. A value of '10' indicates that the clock is so many hops
# away from a reference clock that its time is fairly unreliable.
#
# 'distance' option sets the threshold for the root distance which will
# activate the local reference. If chronyd was synchronised to some source, the
# local reference will not be activated until its root distance reaches the
# specified value (the rate at which the distance is increasing depends on how
# well the clock was tracking the source). The default value is '1' second.
#
# 'orphan' option enables a special "orphan" mode, where sources with stratum
# equal to the local stratum are assumed to not serve real time. They are
# ignored unless no other source is selectable and their reference IDs are
# smaller than the local reference ID. This allows multiple servers in the
# network to use the same local configuration and to be synchronised to one
# another, without confusing clients that poll more than one server. Each
# server needs to be configured to poll all other servers with the local
# directive. This ensures only the server with the smallest reference ID has
# the local reference active and others are synchronised to it. When that
# server fails, another will take over. The 'orphan' mode is compatible with
# the ntpd’s 'orphan' mode (enabled by the 'tos orphan' command).
    local:
    - stratum: '10'
      distance: '1'
      orphan: 'true'
# This directive specifies the location of the Samba ntp_signd socket when it
# is running as a Domain Controller (DC). If chronyd is compiled with this
# feature, responses to MS-SNTP clients will be signed by the smbd daemon.
# Note that MS-SNTP requests are not authenticated and any client that is
# allowed to access the server by the 'allow' directive, or the 'allow' command
# in chronyc, can get an MS-SNTP response signed with a trust account’s
# password and try to crack the password in a brute-force attack. Access to the
# server should be carefully controlled.
    ntpsigndsocket: '/var/lib/samba/ntp_signd'
# This option allows you to configure the port on which chronyd will listen for
# NTP requests. The port will be open only when an address is allowed by the
# 'allow' directive or the 'allow' command in chronyc, an NTP peer is
# configured, or the 'broadcast' server mode is enabled. The default value
# is '123', the standard NTP port. If set to '0', chronyd will never open the
# server port and will operate strictly in a client-only mode. The source port
# used in NTP client requests can be set by the 'acquisitionport' directive.
    port: '123'
# This directive enables response rate limiting for NTP packets. Its purpose is
# to reduce network traffic with misconfigured or broken NTP clients that are
# polling the server too frequently. The limits are applied to individual IP
# addresses. If multiple clients share one IP address (e.g. multiple hosts
# behind NAT), the sum of their traffic will be limited. If a client that
# increases its polling rate when it does not receive a reply is detected, its
# rate limiting will be temporarily suspended to avoid increasing the overall
# amount of traffic. The maximum number of IP addresses which can be monitored
# at the same time depends on the memory limit set by the 'clientloglimit'
# directive.
    ratelimit:
# 'interval' option sets the minimum interval between responses. It is defined
# as a power of 2 in seconds. The default value is '3' (8 seconds). The
# minimum value is '-19' (524288 packets per second) and the maximum value
# is '12' (one packet per 4096 seconds). Note that with values below '-4' the
# rate limiting is coarse (responses are allowed in bursts, even if the interval
# between them is shorter than the specified interval).
    - interval: '1'
# 'burst' option sets the maximum number of responses that can be sent in a
# burst, temporarily exceeding the limit specified by the interval option. This
# is useful for clients that make rapid measurements on start (e.g. chronyd
# with the iburst option). The default value is '8'. The minimum value is '1'
# and the maximum value is '255'.
      burst: '16'
# 'leak' option sets the rate at which responses are randomly allowed even if
# the limits specified by the interval and burst options are exceeded. This is
# necessary to prevent an attacker who is sending requests with a spoofed source
# address from completely blocking responses to that address. The leak rate is
# defined as a power of 1/2 and it is '2' by default, i.e. on average at least
# every fourth request has a response. The minimum value is '1' and the maximum
# value is '4'.
      leak: '2'
# Directive can be used to enable smoothing of the time that chronyd serves to
# its clients to make it easier for them to track it and keep their clocks
# close together even when large offset or frequency corrections are applied to
# the server’s clock, for example after being offline for a longer time.
# BE WARNED: The server is intentionally not serving its best estimate of the
# true time. If a large offset has been accumulated, it can take a very long
# time to smooth it out. This directive should be used only when the clients
# are not configured to also poll another NTP server, because they could reject
# this server as a falseticker or fail to select a source completely. The
# smoothing process is implemented with a quadratic spline function with two or
# three pieces. It is independent from any slewing applied to the local system
# clock, but the accumulated offset and frequency will be reset when the clock
# is corrected by stepping, e.g. by the makestep directive or the makestep
# command in chronyc. The process can be reset without stepping the clock by
# the smoothtime reset command. Maximum frequency offset of the smoothed time
# to the tracked NTP time (in ppm) and the maximum rate at which the frequency
# offset is allowed to change (in ppm per second). 'leaponly' enables a mode
# where only leap seconds are smoothed out and normal offset and frequency
# changes are ignored. The leaponly option is useful in a combination with the
# 'leapsecmode' slew directive to allow the clients to use multiple time
# smoothing servers safely. The smoothing process is activated automatically
# when 1/10000 of the estimated skew of the local clock falls below the maximum
# rate of frequency change. It can be also activated manually by the
# smoothtime activate command, which is particularly useful when the clock is
# synchronised only with manual input and the skew is always larger than the
# threshold. The smoothing command can be used to monitor the process. An
# example suitable for clients using chronyd on Linux could be:
    smoothtime:
    - max_freq: '50000'
      max_wander: '0.01'
      leaponly: 'true'
# Directive allows you to specify an IP address of an interface on which
# chronyd will listen for monitoring command packets (issued by chronyc). On
# systems other than Linux, the address of the interface needs to be already
# configured when chronyd is started. This directive can also change the path
# of the Unix domain command socket, which is used by chronyc to send
# configuration commands. The socket must be in a directory that is accessible
# only by the root or chrony user. The directory will be created on start if it
# does not exist. The compiled-in default path of the socket is
# '/var/run/chrony/chronyd.sock'. The socket can be disabled by setting the
# path to '/'. By default, chronyd binds to the loopback interface (with
# addresses '127.0.0.1' and '::1'). This blocks all access except from
# localhost. To listen for command packets on all interfaces:
    bindcmdaddress:
    - '0.0.0.0'
    - '::'
# This is similar to the access_list directive, except that it allows
# monitoring access (rather than NTP client access) to a particular subnet or
# host. By "monitoring access" is meant that chronyc can be run on those hosts
# and retrieve monitoring data from chronyd on this computer.
    cmdaccess_list:
    - policy: 'cmdallow'
      address: '127.0.0.1'
# The cmdport directive allows the port that is used for run-time monitoring
# (via the chronyc program) to be altered from its default '323'. If set to '0'
# chronyd will not open the port, this is useful to disable chronyc access from
# the Internet. It does not disable the Unix domain command socket.
    cmdport: '323'
# This directive enables response rate limiting for command packets. It is
# similar to the 'ratelimit' directive, except responses to localhost are never
# limited and the default interval is '-4' (16 packets per second).
    cmdratelimit:
    - interval: '2'
# This directive sets the location of the adjtime file which is used by the
# hwclock program on Linux. chronyd parses the file to find out if the RTC
# keeps local time or UTC. It overrides the rtconutc directive. The compiled-in
# default value is '/etc/adjtime'.
    hwclockfile: '/etc/adjtime'
# This irective is used to keep the RTC close to the system clock automatically.
# When the system clock is synchronised and the estimated error between the two
# clocks is larger than the specified threshold, chronyd will trim the RTC as
# if the trimrtc command in chronyc was issued. This directive is effective only
# with the 'rtcfile' directive.
    rtcautotrim: '30'
# This directive sets the path to the device file for accessing the RTC. The
# default path is '/dev/rtc'.
    rtcdevice: '/dev/rtc'
# This directive defines the name of the file in which chronyd can save
# parameters associated with tracking the accuracy of the RTC.
# chronyd saves information in this file when it exits and when the writertc
# command is issued in chronyc. The information saved is the RTC’s error at
# some epoch, that epoch (in seconds since January 1 1970), and the rate at
# which the RTC gains or loses time. So far, the support for real-time clocks is
# limited, their code is even more system-specific than the rest of the
# software. You can only use the RTC facilities (the 'rtcfile' directive) if the
# following three conditions apply:
# 1. You are running Linux.
# 2. The kernel is compiled with extended real-time clock support
# (i.e. the '/dev/rtc' device is capable of doing useful things).
# 3. You do not have other applications that need to make use of '/dev/rtc'
# at all.
    rtcfile: '/var/lib/chrony/rtc'
# chronyd assumes by default that the RTC keeps local time (including any
# daylight saving changes). This is convenient on PCs running Linux which are
# dual-booted with Windows. If you keep the RTC on local time and your computer
# is off when daylight saving (summer time) starts or ends, the computer’s
# system time will be one hour in error when you next boot and start chronyd.
# An alternative is for the RTC to keep Universal Coordinated Time (UTC). This
# does not suffer from the 1 hour problem when daylight saving starts or ends.
# If the 'rtconutc' directive appears, it means the RTC is required to keep UTC.
# Note that this setting is overridden when the 'hwclockfile' directive is
# specified.
    rtconutc: 'false'
# The 'rtcsync' directive enables a mode where the system time is periodically
# copied to the RTC and chronyd does not try to track its drift. This directive
# cannot be used with the rtcfile directive. On Linux, the RTC copy is
# performed by the kernel every 11 minutes. On macOS, chronyd will perform the
# RTC copy every 60 minutes when the system clock is in a synchronised state.
# On other systems this directive does nothing.
    rtcsync: 'false'
# The log files are written to the directory specified by the 'logdir'. A
# banner is periodically written to the files to indicate the meanings of the
# columns.
    log:
# 'rawmeasurements' option logs the raw NTP measurements and related
# information to a file called measurements.log. An entry is made for each
# packet received from the source. This can be useful when debugging a problem.
    - 'rawmeasurements'
# 'measurements' option is identical to the 'rawmeasurements' option, except it
# logs only valid measurements from synchronised sources, i.e. measurements
# which passed the RFC 5905 tests 1 through 7. This can be useful for producing
# graphs of the source’s performance.
    - 'measurements'
# 'statistics' option logs information about the regression processing to
# a file called "statistics.log".
    - 'statistics'
# 'tracking' option logs changes to the estimate of the system’s gain or loss
# rate, and any slews made, to a file called tracking.log.
    - 'tracking'
# 'rtc'option logs information about the system’s real-time clock.
    - 'rtc'
# 'refclocks' option logs the raw and filtered reference clock measurements to a
# file called "refclocks.log".
    - 'refclocks'
# 'tempcomp' option logs the temperature measurements and system rate
# compensations to a file called "tempcomp.log".
    - 'tempcomp'
# A banner is periodically written to the log files enabled by the 'log'
# directive to indicate the meanings of the columns. Directive specifies after
# how many entries in the log file should be the banner written.
# The default is '32', and '0' can be used to disable it entirely.
    logbanner: '32'
# This directive sets the threshold for the adjustment of the system clock that
# will generate a syslog message. Clock errors detected via NTP packets,
# reference clocks, or timestamps entered via the settime command of chronyc
# are logged. By default, the threshold is '1' second.
    logchange: '1'
# This directive allows the directory where log files are written to be
# specified.
    logdir: '/var/log/chrony'
# This directive defines an email address to which mail should be sent if
# chronyd applies a correction exceeding a particular threshold to the system
# clock.
# This would send a mail message to root if a change of more than 0.5 seconds
# were applied to the system clock. This directive cannot be used when a
# system call filter is enabled by the '-F' option as the chronyd process will
# not be allowed to fork and execute the sendmail binary.
    mailonchange:
    - address: 'root@localhost'
      threshold: '0.5'
# This directive enables hardware timestamping of NTP packets sent to and
# received from the specified network interface. NIC uses its own clock to
# accurately timestamp the actual transmissions and receptions, avoiding
# processing and queueing delays in the kernel, network driver, and hardware.
# This can significantly improve the accuracy of the timestamps and the
# measured offset, which is used for synchronisation of the system clock. In
# order to get the best results, both sides receiving and sending NTP packets
# (i.e. server and client, or two peers) need to use HW timestamping. If the
# server or peer supports the interleaved mode, it needs to be enabled by the
# xleave option in the server or the peer directive. This directive is
# supported on Linux 3.19 and newer. The NIC must support HW timestamping,
# which can be verified with the 'ethtool -T' command. The list of capabilities
# should include SOF_TIMESTAMPING_RAW_HARDWARE, SOF_TIMESTAMPING_TX_HARDWARE,
# and SOF_TIMESTAMPING_RX_HARDWARE. Receive filter HWTSTAMP_FILTER_ALL, or
# HWTSTAMP_FILTER_NTP_ALL, is necessary for timestamping of received packets.
# Timestamping of packets received from bridged and bonded interfaces is
# supported on Linux 4.13 and newer. When chronyd is running, no other process
# (e.g. a PTP daemon) should be working with the NIC clock.
# If the kernel supports software timestamping, it will be enabled for all
# interfaces. The source of timestamps (i.e. hardware, kernel, or daemon) is
# indicated in the "measurements.log" file if enabled by the log measurements
# directive, and the ntpdata report in chronyc. If the specified interface
# is '*', chronyd will try to enable HW timestamping on all available
# interfaces.
# The 'hwtimestamp' directive has the following options:
#
# 'minpoll' option specifies the minimum interval between readings of the NIC
# clock. It’s defined as a power of two. It should correspond to the minimum
# polling interval of all NTP sources and the minimum expected polling interval
# of NTP clients. The default value is '0' (1 second) and the minimum value is
# '-6' (1/64th of a second).
#
# 'minsamples' option specifies the minimum number of readings kept for
# tracking of the NIC clock. The default value is '2'.
#
# 'maxsamples' option specifies the maximum number of readings kept for
# tracking of the NIC clock. The default value is '16'.
#
# 'precision' option specifies the assumed precision of reading of the NIC
# clock. The default value is 100e-9 (100 nanoseconds).
#
# 'txcomp' option specifies the difference in seconds between the actual
# transmission time at the physical layer and the reported transmit timestamp.
# This value will be added to transmit timestamps obtained from the NIC. The
# default value is '0'.
#
# 'rxcomp' option specifies the difference in seconds between the reported
# receive timestamp and the actual reception time at the physical layer. This
# value will be subtracted from receive timestamps obtained from the NIC. The
# default value is '0'.
#
# 'nocrossts' - some hardware can precisely cross timestamp the NIC clock with
# the system clock. This option disables the use of the cross timestamping.
#
# 'rxfilter' option selects the receive timestamping filter. The filter can be
# one of the following:
# 'all' - enables timestamping of all received packets.
# 'ntp' - enables timestamping of received NTP packets.
# 'none' - disables timestamping of received packets.
# The most specific filter for timestamping NTP packets which is supported by
# the NIC is selected by default. Some NICs can timestamp only PTP packets,
# which limits the selection to the none filter. Forcing timestamping of all
# packets with the all filter when the NIC supports both all and ntp filters
# can be useful when packets are received from or on a non-standard UDP port
# (e.g. specified by the 'port' directive).
    hwtimestamp:
    - interface: 'eth0'
    - interface: 'eth1'
      txcomp: '300e-9'
      rxcomp: '645e-9'
    - interface: '*'
# 'keyfile' directive is used to specify the location of the file containing
# ID-key pairs for authentication of NTP packets.
    keyfile: '/etc/chrony.keys'
# The 'lock_all' directive will lock chronyd into RAM so that it will never be
# paged out. This mode is only supported on Linux. This directive uses the
# Linux mlockall() system call to prevent chronyd from ever being swapped out.
# This should result in lower and more consistent latency. It should not have
# significant impact on performance as chronyd’s memory usage is modest.
    lock_all: 'false'
# Unless chronyd is started with the '-Q' option, it writes its process ID to a
# file, and checks this file on startup to see if another chronyd might already
# be running on the system. By default, the file used is
# '/var/run/chrony/chronyd.pid'.
    pidfile: '/var/run/chrony/chronyd.pid'
# On Linux, the 'sched_priority' directive will select the SCHED_FIFO real-time
# scheduler at the specified priority (which must be between 0 and 100).
# On macOS, this option must have either a value of '0' (the default) to disable
# the thread time constraint policy or 1 for the policy to be enabled. Other
# systems do not support this option.
# On Linux, this directive uses the sched_setscheduler() system call to instruct
# the kernel to use the SCHED_FIFO first-in, first-out real-time scheduling
# policy for chronyd with the specified priority. This means that whenever
# chronyd is ready to run it will run, interrupting whatever else is running
# unless it is a higher priority real-time process. This should not impact
# performance as chronyd resource requirements are modest, but it should result
# in lower and more consistent latency since chronyd will not need to wait for
# the scheduler to get around to running it. You should not use this unless you
# really need it.
# On macOS, this directive uses the thread_policy_set() kernel call to specify
# real-time scheduling. As noted for Linux, you should not use this directive
# unless you really need it.
    sched_priority: ''
# Directive sets the name of the system user to which chronyd will switch after
# start in order to drop root privileges.
# On Linux, chronyd needs to be compiled with support for the libcap library.
# On macOS, FreeBSD, NetBSD and Solaris chronyd forks into two processes. The
# child process retains root privileges, but can only perform a very limited
# range of privileged system calls on behalf of the parent. The compiled-in
# default value is 'chrony'.
    user: 'chrony'
```
