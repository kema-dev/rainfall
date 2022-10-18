# level0

## Vulnerability: Simple atoi()

level0's password: `level0`

1. examinate existing files and permissions

```bash
ls -la
```

c file shows that the level0 executable belongs to level1, privilege escalation should be possible

2. get the executable (from an external device), decompile it using [ghidra](https://github.com/NationalSecurityAgency/ghidra) and examine the resulting code

```bash
scp -P 4242 level0@<host>:level0 .
```

shows `iVar1 = atoi(*(char **)(param_2 + 4)); if (iVar1 == 0x1a7) {` to prevent a shell from being spawned. `param_2 + 4` might just mean `argv[1]` (decompiler things)

3. send a proper arguments to the executable and get a shell (0x1a7 in hex is 423 in decimal)

```bash
./level0 423
```

4. get the flag

```bash
whoami
cat /home/user/level1/.pass
```

gives flag: `1fe8a524fa4bec01ca4ea2a869af2a02260d4a7d5fe7e7c24d8617e6dca12d3a`
