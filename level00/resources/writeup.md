After SSH'ing into the server, the home directory is empty:

```shell
level00@SnowCrash:~$ ls -la
```

We searched for files owned by `flag00`:

```shell
level00@SnowCrash:~$ find / -user flag00 -ls 2>/dev/null
```

Results:

```shell
/usr/sbin/john
/rofs/usr/sbin/john
```

Both files contained the same string:

```shell
level00@SnowCrash:~$ cat /usr/sbin/john
cdiiddwpgswtgt
```

Using a ROT-15 [cipher](https://cryptii.com/pipes/caesar-cipher), we decoded `cdiiddwpgswtgt` to `nottoohardhere`. This worked as the password for `flag00`:

```shell
level00@SnowCrash:~$ su flag00
Password:
flag00@SnowCrash:~$
```

Finally, we retrieved the flag:

```shell
flag00@SnowCrash:~$ getflag
Check flag. Here is your token: x24ti5gi3x0ol2eh4esiuxias
```
