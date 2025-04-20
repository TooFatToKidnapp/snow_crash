The home directory is empty:

```
level01@SnowCrash:~$ ls -la
```

We found the hash for `flag01` in `/etc/passwd`:

```
level01@SnowCrash:~$ cat /etc/passwd | grep flag01
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
```

Using [John the Ripper](https://en.wikipedia.org/wiki/John_the_Ripper), we cracked the hash:

```
$ echo "flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash" > /tmp/file
$ john --show /tmp/file
flag01:abcdefg
```

We logged in with the password `abcdefg`:

```
level01@SnowCrash:~$ su flag01
Password: abcdefg
flag01@SnowCrash:~$
```

Finally, we retrieved the flag:

```
flag01@SnowCrash:~$ getflag
Check flag. Here is your token: f2av5il02puano7naaf6adaaf
```
