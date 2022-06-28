# chroot-mounts

## Issue 1
I expect to be able to create a chroot in a codespace without having to modify how `/workspaces/` is mounted but actually, I do. 

## Issue 2
I expect 

```
cat < <(echo test)
```

to echo `test` in a chroot created in a codespace but the call fails with

```
bash: /dev/fd/63: No such file or directory
```

## Reproduction

```bash
1 $ sudo mount -i -o remount,exec,dev /workspaces/
2 $ sudo apt-get install -y binutils debootstrap
3 $ CHROOT=/workspaces/chroot
4 $ mkdir -p "${CHROOT}"
5 $ sudo debootstrap stable "${CHROOT}" http://deb.debian.org/debian/
6 $ sudo chroot "${CHROOT}"
7 $ cat < <(echo test)
bash: /dev/fd/63: No such file or directory
```

## Investigation

The way host directories are mounted in the chroot appear to be the issue. 
- Line 1 of the test case is necessary to get line 5 to run and has to do with mounts. Line 1 was harvested from [this](https://superuser.com/questions/620003/debootstrap-error-in-ubuntu-13-04-raring
) SO post.
- [This](https://unix.stackexchange.com/questions/405419/bash-process-substitution-in-chroot-without-dev-and-proc) SO post suggests changing the mount configuration to enable subprocess.
- Running `apt install sudo` will generate an error `E: Can not write log (Is /dev/pts mounted?) - posix_openpt (19: No such device)`.

For these reasons I suspect the mounts are unexpectedly configured. 
