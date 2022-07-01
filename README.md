# Goal
Standup the a chroot with the basic unix tool-set (bash, cp, touch, cat, etc) and any necessary dependencies need to run `apt get`. 

My SO post: [Subprocess launched inside a chroot created on a codespace with debootstrap fail with /dev/fd/62: No such file or directory](https://unix.stackexchange.com/questions/708066/subprocess-launched-inside-a-chroot-created-on-a-codespace-with-debootstrap-fail)

# Attempt
This gets me close. The basic tools are installed. I can run `apt get`. The problem is subprocess do not seem to work. I expect the last line to print `test` but actually I get an error saying that the subprocess file handle is no good.

# Reproduction
Sign up for codespaces beta [here](https://github.com/features/codespaces/signup). Then fire up a codespace for this project, open a bash shell (ctrl+`) and run the following.

```bash
$ sudo apt-get install -y binutils debootstrap
$ cd /home/codespace
$ CHROOT=/home/codespace/chroot
$ mkdir -p "${CHROOT}"
$ sudo debootstrap stable "${CHROOT}" http://deb.debian.org/debian/
$ sudo chroot "${CHROOT}"
$ cat < <(echo test)
bash: /dev/fd/63: No such file or directory
```

I'm able to reproduce this outside a codespace. So I the problem is not specific to codespaces. 
