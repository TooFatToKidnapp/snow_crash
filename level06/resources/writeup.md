we notice two files in the home directory
```shell
level06@SnowCrash:~$ ls -al
-rwsr-x---+ 1 flag06  level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06  level06  356 Mar  5  2016 level06.php
```

using binary ninja we to inspect the source code of the `level06` binary, we fined the following snippet

```C
int32_t main(int32_t argc, char** argv, char** envp)
{
    void* const __return_addr_1 = __return_addr;
    int32_t* var_18 = &argc;
    char* esi = strdup(&data_80487d0[4]);
    char* ebx = strdup(&data_80487d0[4]);

    if (argv[1])
    {
        free(esi);
        esi = strdup(argv[1]);

        if (argv[2])
        {
            free(ebx);
            ebx = strdup(argv[2]);
        }
    }

    gid_t eax_6 = getegid();
    uid_t eax_7 = geteuid();
    setresgid(eax_6, eax_6, eax_6);
    setresuid(eax_7, eax_7, eax_7);
    char* var_2c = esi;
    char* var_28 = ebx;
    char const* const var_34 = "/usr/bin/php";
    char const* const var_30 = "/home/user/level06/level06.php";
    int32_t var_24 = 0;
    execve("/usr/bin/php", &var_34, envp, eax_7);
    return 0;
}
```

the binary expects two arguments and passes them to a child process, which runs an instance of the `level06.php` script.
we also notice that the binary file has the user id bit set , meaning that the binary file runs with the privileges of its owner

lets read the content of the php script
```php
#!/usr/bin/php
<?php
  function y($m) {
    $m = preg_replace("/\./", " x ", $m); // replace '.' with " x "
    $m = preg_replace("/@/", " y", $m); // replace '@' with " y"
    return $m;
  }
  function x($y, $z) {
    $a = file_get_contents($y); // open and read the file contents
    // vulnerability in /e flag of the regex
    // match "[x "something"]" and pass it as a argument to the y function as y("something"), it gets executed as php code
    $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
    $a = preg_replace("/\[/", "(", $a); // replace '[' with '('
    $a = preg_replace("/\]/", ")", $a); // replace ']' with ')'
    return $a;
  }
  $r = x($argv[1], $argv[2]); print $r;
?>
```
i added some comments to explain what each line of the script dose

the exploit is located at the following regex `"/(\[x (.*)\])/e"`, it allows for remote command execution because of the `/e` flag

