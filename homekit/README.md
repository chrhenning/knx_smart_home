# KNX HomeKit Integration

In this folder, we track the configuration files of our KNX HomeKit integration, which utilizes [Homebridge], which provides an interface to HomeKit. In particular, we use the Homebridge plugin [Homebridge-KNX], that emulates an interface for Apple devices to  [knxd], which is the communication interface to the acual bus system.

The argument list for knxd is currently configured in the file `/etc/knxd.conf` as follows:


```
KNXD_OPTS="-e 1.1.0 -E 1.1.245:4 -DTRS -i 6720 -b ipt:<GATEWAY-IP>:<GATEWAY-PORT>"
```

Any changes to one of the configuration files only take effect if the corresponding system service is restarted.

The services can be restarted via the command:

```
$ sudo systemctl restart knxd
```

resp.

```
$ sudo systemctl restart homebridge
```

By replacing the keyword `restart` with `status`, one can ensure that the service is still running properly.

Note, when changing any of the Homebridge configuration files, ensure that the permissions are still correct:

```
$ sudo chown -R homebridge.homebridge /var/homebridge/
```

This assumes that a system user named *homebridge* controls the Homebridge process and has its home directory under `/var/homebridge/`.

## Remarks

### HomeKit bug for dimmable lights

There is a bug from Apple (08/04/17), such that if using the "Brightness" together with the "On" characteristic, a change in brightness will cause an "on" package to be send. This results in a situation, where the setting of the correct brightness level depends on the random order of the packages (either the "brightness" or "on" package is sent last). The [Brightness-Bug] is described in this thread. There is also a resolution described from user `dlt` (Post from 08/26/17). We used the proposed handler and stored it under `/usr/lib/node_modules/homebridge-knx/lib/addins/dltDIM.js`. This handler ensures, that no "on" packages are sent, if there have been any "brightness" packages in recent history. 

[Homebridge]: https://github.com/nfarina/homebridge
[Homebridge-KNX]: https://github.com/snowdd1/homebridge-knx
[knxd]: https://github.com/knxd/knxd
[Brightness-Bug]: https://github.com/snowdd1/homebridge-knx/issues/47

