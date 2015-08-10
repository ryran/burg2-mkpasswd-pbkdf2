# burg2-mkpasswd-pbkdf2 ... why?
GRUB2 comes with `grub2-mkpasswd-pbkdf2` but it forces you to interactively enter the passphrase twice and it prints other informative text to stdout.

You can get around this by piping the passphrase twice (with newline) and using (e.g.) `awk` to grab the appropriate field, for example:

```
$ echo -e 'mypass\nmypass' | grub2-mkpasswd-pbkdf2 | awk '/pbkdf/{print$7}'
grub.pbkdf2.sha512.10000.56C1452B27834D602359CBE80846D6CFA2727548791207D1401EE12666838CB901188877B9871A20184814244A68BD1E37283E93268EAA2271AABAA10659E5B2.5985ACADFCEF91F4C980A41920A4A2F2AF4FC27B3A3E2396AB4E1848B6105C61ED796C114A86D31CF001ADE56E4A8E9567F18D5C6A1006154ECFFDB47CE0A9CE
```

If that's good enough for you, by all means, use it. Otherwise, read on.

### Requirements

- Only tested on RHEL 7 and Fedora 22
- No special packages needed; only standard library (python) modules used
- Will not work on RHEL 6 with standard python 2.6.6 but would probably work just fine with python 2.7 from [RHSCL](https://access.redhat.com/solutions/472793)

### Passphrases

- Can read passphrase from 1st line of stdin
- Can read passphrase from cmdline argument
- Can read passphrase from 1st line of filename

### Help page

```
$ burg2-mkpasswd-pbkdf2 -h
usage: burg2-mkpasswd-pbkdf2 [-h] [-c NUM] [-s NUM] [-p PASS | -f FILE | -0]
                             [-d]

Non-interactively generate GRUB2-compatible PBKDF2 hashes to stdout

optional arguments:
  -h, --help            Show this help message and exit
  -c NUM, --iteration-count NUM
                        Number of PBKDF2 iterations
  -s NUM, --salt NUM    Length of salt
  -p PASS, --pass-str PASS
                        The actual passphrase is PASS (dangerous on multi-user
                        systems)
  -f FILE, --pass-file FILE
                        The first line of FILE is the passphrase
  -0, --pass-stdin      The first line of standard input is the passphrase
  -d, --debug           Enable some debugging (PRINTS PASSPHRASE TO STDERR)

Version info: burg2-mkpasswd-pbkdf2 v0.0.1 last mod 2015/09/10
bugs/RFEs: github.com/ryran/burg2-mkpasswd-pbkdf2 or rsaw@redhat.com
```
