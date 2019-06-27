# sourcejedi.atop #

The `atop` service provides daily logging of system and process activity for analyzing trends.
I like having it to log memory usage per process (and system-wide as well).

> https://lwn.net/Articles/387202/
>
> The normal installation of the `atop` package starts an `atop` daemon nightly. This daemon takes snapshots and writes them to a log file (`/var/log/atop/atop_YYYYMMDD`). The default snapshot interval for a logging `atop` is 10 minutes, but obviously this is configurable. Every logfile is preserved for a month (also configurable), so performance events a full month back can still be observed.
>
> The log file can be viewed using `atop -r log_filename`. The subcommand `t` forwards to the next sample in the atop logfile (i.e. 10 minutes by default), subcommand `T` rewinds one sample. Subcommand `b` branches to a specific time in the current logfile. All other subcommands to zoom in on specific resources also work. The logfile that the atop daemon creates can also be viewed using a sar-like interface using the command atopsar.

Unfortunately if you use the current Fedora package and suspend your laptop overnight, the log file won't be rotated.
So this role includes a script to work around the Fedora issue.

[https://bugzilla.redhat.com/show_bug.cgi?id=1571866](https://bugzilla.redhat.com/show_bug.cgi?id=1571866)

You may notice that atop's strategy to rotate the log file is unsually crude.  It simply restarts the atop service.  This shows up in the atop log file, as a sample with a flashing message "system and process activity since boot", with the "elapsed" time showing time since boot instead of the time since the previous sample.  (E.g., it would nicer if atop could check that the logfile was actually going to be rotated to a new day, before it restarted).


## Requirements

This should work on any distribution that has a package for `atop`.
However it is probably most useful on Fedora.


## Status

There is a common issue with atop upgrades.  When you upgrade, the log format may change.  Then when you re-run this role on the same day, you would notice "Start atop service" always appears as "changed".  The service has stopped and logged an error message:

```
$ cat /var/log/atop/daily.log
existing file /var/log/atop/atop_20190627 has incompatible header
(created by version 2.3 - current version 2.4)
```

The service will start properly if you delete that day's log file.  (Or if you wait until the next day :-).

I have added a little hack to try and detect this.  I try to show a "failed" result, and a message telling you to read the log file.


## License

This role is licensed GPLv3, please open an issue if this creates any problem.
