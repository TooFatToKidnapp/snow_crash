we found the following file in the home directory
```
level02@SnowCrash:~$ ls -l
total 12
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03
```

we notice a few key thing:
 - [Set-User-ID](https://en.wikipedia.org/wiki/Setuid) bit is set, meaning the the 
