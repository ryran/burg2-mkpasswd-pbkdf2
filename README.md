# burg2-mkpasswd-pbkdf2 ... why?
GRUB2 comes with `grub2-mkpasswd-pbkdf2` but it forces you to interactively enter the passphrase twice and it prints other informative text to stdout.

You can get around this by piping (stdin) the passphrase twice (with newline) and using `awk` or `sed` or `cut` to grab the appropriate field, for example ...

- On single-user systems in a hurry:

    ```
    $ echo -e 'mypass\nmypass' | grub2-mkpasswd-pbkdf2 | awk '/grub.pbkdf/{print$NF}'
    grub.pbkdf2.sha512.10000.56C1452B27834D602359CBE80846D6CFA2727548791207D1401EE12666838CB901188877B9871A20184814244A68BD1E37283E93268EAA2271AABAA10659E5B2.5985ACADFCEF91F4C980A41920A4A2F2AF4FC27B3A3E2396AB4E1848B6105C61ED796C114A86D31CF001ADE56E4A8E9567F18D5C6A1006154ECFFDB47CE0A9CE
    ```

- Or, more secure approach -- especially on multi-user systems:

    ```
    $ cat ~/pfile
    my p@55phras3
    my p@55phras3
    $ cat ~/pfile | grub2-mkpasswd-pbkdf2 | awk '/grub.pbkdf/{print$NF}'
    grub.pbkdf2.sha512.10000.6CFB6582FDE0CCE0A1D90389D00AFB5FC48B5FEFE1213563AE52C8DBFE32E20CB70C88005F20C40FC420323A61BF5CA49B392CBE04DCE07CD1AE6DAD228C2BB2.984FB69725E7E6A1CA5BE39B1AC023B0E3B698EEF33949796D64B72495A97F533B99568FCA0A07F9FF98D4EDBE6AFD854388F2653863961207F901CDBDB683BD
    ```

If that's good enough for you, by all means, use it. Otherwise, read on.

### Requirements

- Requires Python 2.7 (`argparse`, `hashlib.pbkdf2_hmac`)
- Fails on RHEL 7 (use `grub2-mkpasswd-pbkdf2` method above)
- Tested on Fedora 22: no special packages needed (only standard library [python] modules used)
- Tested on RHEL 6 with python 2.7 from [RHSCL](https://access.redhat.com/solutions/472793)
    1. Enable the SCL repo (e.g., `subscription-manager repos --enable rhel-server-rhscl-6-rpms`)
    1. Install python 2.7 (i.e., `yum install python27`)
    1. Save `burg2-mkpasswd-pbkdf2` to somewhere in `PATH`
    1. Execute `scl enable python27 -- burg2-mkpasswd-pbkdf2 --help`
    1. Optionally create a shell-script wrapper or alias to avoid typing that every time
    
### Features

- Can read passphrase from 1st line of stdin
- Can read passphrase from cmdline argument
- Can read passphrase from 1st line of filename
- Can modify salt-size and PBKDF2 iteration count

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
```
