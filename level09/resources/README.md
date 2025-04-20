we notice two files in the home directory, a `SUID` executable file `level09` and a `token` file

```shell
level09@SnowCrash:~$ ls -la
-rwsr-sr-x 1 flag09  level09 7640 Mar  5  2016 level09
----r--r-- 1 flag09  level09   26 Mar  5  2016 token
```

we can read read the contents of the `token` file but it looks like it had non `ASCII` characters
```shell
level09@SnowCrash:~$ cat token
f4kmm6p|=�p�n��DB�Du{��
```

after paying around with the program, it seems that index position is added to each character which gives following output

```shell
level09@SnowCrash:~$ ./level09 ABCDE
ACEGI
level09@SnowCrash:~$ ./level09 01234
02468
```

we suspect that the token file contains the token with the characters incremented by there index

to verify we create the following script


```python
with open("/home/user/level09/token", "r") as file:
    file_content = file.read()

result = ""
for i, char in enumerate(file_content):
    new_ascii = ord(char) - i
    if new_ascii < 0:
        new_ascii = 0
    result += chr(new_ascii)

print(result)
```

now we run the script and try out the result
```shell
level09@SnowCrash:~$ python /tmp/script.py
f3iji1ju5yuevaus41q1afiuq
level09@SnowCrash:~$ su flag09
Password: f3iji1ju5yuevaus41q1afiuq
Don't forget to launch getflag !
flag09@SnowCrash:~$
```

it worked, let get the flag and move on to the next level

```shell
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
```

