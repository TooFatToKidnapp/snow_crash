in this level we have to go throught the `getflag` binary itself to get the last flag.

here's the main function from binary ninja

```c
int32_t main(int32_t argc, char** argv, char** envp)
{
    void* gsbase;
    int32_t eax = *(gsbase + 0x14);
    int32_t var_120 = 0;
    int32_t result;
    
    if (ptrace(PTRACE_TRACEME, 0, 1, 0, 0) >= 0)
    {
        if (!getenv("LD_PRELOAD"))
        {
            if (open("/etc/ld.so.preload", 0) <= 0)
            {
                int32_t eax_5 = syscall_open("/proc/self/maps", 0);
                
                if (eax_5 != 0xffffffff)
                {
                    void var_114;
                    
                    while (syscall_gets(&var_114, 0x100, eax_5))
                    {
                        if (isLib(&var_114, "libc"))
                            var_120 = 1;
                        else if (var_120)
                        {
                            if (isLib(&var_114, &data_8049068))
                            {
                                fwrite("Check flag.Here is your token : ", 1, 0x20, stdout);
                                int32_t eax_8 = getuid();
                                
                                if (eax_8 == 0xbbe)
                                    fputs(ft_des("H8B8h_20B4J43><8>\ED<;j@3"), stdout);
                                else if (eax_8 > 0xbbe)
                                {
                                    if (eax_8 == 0xbc2)
                                        fputs(ft_des("74H9D^3ed7k05445J0E4e;Da4"), stdout);
                                    else if (eax_8 > 0xbc2)
                                    {
                                        if (eax_8 == 0xbc4)
                                            fputs(ft_des("8_Dw"4#?+3i]q&;p6 gtw88EC"), stdout);
                                        else if (eax_8 < 0xbc4)
                                            fputs(ft_des("70hCi,E44Df[A4B/J@3f<=:`D"), stdout);
                                        else if (eax_8 == 0xbc5)
                                            fputs(ft_des("boe]!ai0FB@.:|L6l@A?>qJ}I"), stdout);
                                        else if (eax_8 == 0xbc6)
                                            fputs(ft_des("g <t61:|4_|!@IF.-62FH&G~DCK/Ekrv…"), stdout);
                                        else
                                            fwrite("\nNope there is no token here fo…", 1, 0x38, stdout);
                                    }
                                    else if (eax_8 == 0xbc0)
                                        fputs(ft_des("bci`mC{)jxkn<"uD~6%g7FK`7"), stdout);
                                    else if (eax_8 > 0xbc0)
                                        fputs(ft_des("Dc6m~;}f8Cj#xFkel;#&ycfbK"), stdout);
                                    else
                                        fputs(ft_des("78H:J4<4<9i_I4k0J^5>B1j`9"), stdout);
                                }
                                else if (eax_8 == 0xbba)
                                    fputs(ft_des("<>B16\AD<C6,G_<1>^7ci>l4B"), stdout);
                                else if (eax_8 > 0xbba)
                                {
                                    if (eax_8 == 0xbbc)
                                        fputs(ft_des("?4d@:,C>8C60G>8:h:Gb4?l,A"), stdout);
                                    else if (eax_8 > 0xbbc)
                                        fputs(ft_des("G8H.6,=4k5J0<cd/D@>>B:>:4"), stdout);
                                    else
                                        fputs(ft_des("B8b:6,3fj7:,;bh>D@>8i:6@D"), stdout);
                                }
                                else if (eax_8 == 0xbb8)
                                    fputs(ft_des("I`fA>_88eEd:=`85h0D8HE>,D"), stdout);
                                else if (eax_8 > 0xbb8)
                                    fputs(ft_des("7`4Ci4=^d=J,?>i;6,7d416,7"), stdout);
                                else if (!eax_8)
                                    fwrite("You are root are you that dumb ?…", 1, 0x21, stdout);
                                else
                                    fwrite("\nNope there is no token here fo…", 1, 0x38, stdout);
                                
                                fputc(0xa, stdout);
                                break;
                            }
                            
                            if (!afterSubstr(&var_114, "00000000 00:00 0"))
                            {
                                fwrite("LD_PRELOAD detected through memo…", 1, 0x30, stderr);
                                break;
                            }
                        }
                    }
                    
                    result = 0;
                }
                else
                {
                    fwrite("/proc/self/maps is unaccessible,…", 1, 0x46, stderr);
                    result = 1;
                }
            }
            else
            {
                fwrite("Injection Linked lib detected ex…", 1, 0x25, stderr);
                result = 1;
            }
        }
        else
        {
            fwrite("Injection Linked lib detected ex…", 1, 0x25, stderr);
            result = 1;
        }
    }
    else
    {
        puts("You should not reverse this");
        result = 1;
    }
    
    if (eax == *(gsbase + 0x14))
        return result;
    
    __stack_chk_fail();
    /* no return */
}
```

we can easily bypass ptrace protection by jumping to the desired location in the code using gdb

we get every decrypted string by jumping to `fputs` lines.

after that it's just a matter of trial and error until we get the right flag

```

(gdb)
(gdb)
(gdb) jump *0x08048bbb
The program is not being run.
(gdb) break main
Breakpoint 1 at 0x804894a
(gdb) r
Starting program: /bin/getflag

Breakpoint 1, 0x0804894a in main ()
(gdb) jump *0x08048bbb
Continuing at 0x8048bbb.
7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
*** stack smashing detected ***: /bin/getflag terminated
======= Backtrace: =========
/lib/i386-linux-gnu/libc.so.6(__fortify_fail+0x45)[0xb7f2fd95]
/lib/i386-linux-gnu/libc.so.6(+0x103d4a)[0xb7f2fd4a]
/bin/getflag[0x8048ec7]
/lib/i386-linux-gnu/libc.so.6(__libc_start_main+0xf3)[0xb7e454d3]
/bin/getflag[0x8048571]
======= Memory map: ========
08048000-0804a000 r-xp 00000000 07:00 12700      /bin/getflag
0804a000-0804b000 r--p 00001000 07:00 12700      /bin/getflag
0804b000-0804c000 rw-p 00002000 07:00 12700      /bin/getflag
0804c000-0806d000 rw-p 00000000 00:00 0          [heap]
b7e07000-b7e23000 r-xp 00000000 07:00 14117      /lib/i386-linux-gnu/libgcc_s.so.1
b7e23000-b7e24000 r--p 0001b000 07:00 14117      /lib/i386-linux-gnu/libgcc_s.so.1
b7e24000-b7e25000 rw-p 0001c000 07:00 14117      /lib/i386-linux-gnu/libgcc_s.so.1
b7e2b000-b7e2c000 rw-p 00000000 00:00 0
b7e2c000-b7fcf000 r-xp 00000000 07:00 14123      /lib/i386-linux-gnu/libc-2.15.so
b7fcf000-b7fd1000 r--p 001a3000 07:00 14123      /lib/i386-linux-gnu/libc-2.15.so
b7fd1000-b7fd2000 rw-p 001a5000 07:00 14123      /lib/i386-linux-gnu/libc-2.15.so
b7fd2000-b7fd5000 rw-p 00000000 00:00 0
b7fd9000-b7fdd000 rw-p 00000000 00:00 0
b7fdd000-b7fde000 r-xp 00000000 00:00 0          [vdso]
b7fde000-b7ffe000 r-xp 00000000 07:00 14081      /lib/i386-linux-gnu/ld-2.15.so
b7ffe000-b7fff000 r--p 0001f000 07:00 14081      /lib/i386-linux-gnu/ld-2.15.so
b7fff000-b8000000 rw-p 00020000 07:00 14081      /lib/i386-linux-gnu/ld-2.15.so
bffdf000-c0000000 rw-p 00000000 00:00 0          [stack]

Program received signal SIGABRT, Aborted.
0xb7fdd428 in __kernel_vsyscall ()
(gdb) quit
A debugging session is active.

	Inferior 1 [process 3017] will be killed.

Quit anyway? (y or n) y
level14@SnowCrash:~$ su flag14
Password:
Congratulation. Type getflag to get the key and send it to me the owner of this livecd :)
```