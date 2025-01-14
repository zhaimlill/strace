**Added a new option**: syscall_callback_script. You can use it to specify shell script to call back if sys-execve is executing in tracee.

**Usage**: strace -f --syscall_callback_script=<script> <executable>

**shell script example**:
```bash
#!/usr/bin/bash
#callback script once strace detecting system call execve. input args:
# $1: pid of execve target
# $2: execve targe. eg. /usr/bin/ls
# $3: tcp->flags, tcp is a structure in strace source code. 

#exit 100: let strace detach the target(pid=$1) so that you can attach it by gdb.
#other exit code: don't detach, strace will continue.

echo "tracee pid=$1, arg0(execve)=$2, flags=$3" >> mzhai.txt

bname=$(basename "$2")
if [ "$bname" = "exe2" ]; then
        echo "Found $bname" >> mzhai.txt
        #pause execve target
        kill -19 $1
        #hard code. Notify strace to detach execve target
        exit 100
else
    exit 0
fi

#1. you can use gdb to attach execve target,
#2. then in another session run 'kill -18 $1' to let target continue running.
#3. go back to gdb, enjoy debugging.
```

----------------------Original readme-----------------


strace - the linux syscall tracer
=================================

This is [strace](https://strace.io) -- a diagnostic, debugging and instructional userspace utility with a traditional command-line interface for Linux.  It is used to monitor and tamper with interactions between processes and the Linux kernel, which include system calls, signal deliveries, and changes of process state.  The operation of strace is made possible by the kernel feature known as [ptrace](http://man7.org/linux/man-pages/man2/ptrace.2.html).

strace is released under the terms of [the GNU Lesser General Public License version 2.1 or later](LGPL-2.1-or-later); see the file [COPYING](COPYING) for details.
strace test suite is released under the terms of [the GNU General Public License version 2 or later](tests/GPL-2.0-or-later); see the file [tests/COPYING](tests/COPYING) for details.

See the file [NEWS](NEWS) for information on what has changed in recent versions.

Please read the file [INSTALL-git](INSTALL-git.md) for installation instructions.

Please take a look at [the guide for new contributors](https://strace.io/wiki/NewContributorGuide) if you want to get involved in strace development.

The user discussion and development of strace take place on [the strace mailing list](https://lists.strace.io/mailman/listinfo/strace-devel) -- everyone is welcome to post bug reports, feature requests, comments and patches to strace-devel@lists.strace.io.  The mailing list archives are available at https://lists.strace.io/pipermail/strace-devel/ and other archival sites.

The GIT repository of strace is available at [GitHub](https://github.com/strace/strace/) and [GitLab](https://gitlab.com/strace/strace/).

The latest binary strace packages are available in many repositories, including
[OBS](https://build.opensuse.org/package/show/home:ldv_alt/strace/),
[Fedora rawhide](https://packages.fedoraproject.org/pkgs/strace/), and
[Sisyphus](https://packages.altlinux.org/en/Sisyphus/srpms/strace).

[![CI](https://github.com/strace/strace/workflows/CI/badge.svg?branch=master)](https://github.com/strace/strace/actions?query=workflow:CI+branch:master) [![Code Coverage](https://codecov.io/github/strace/strace/coverage.svg?branch=master)](https://codecov.io/github/strace/strace?branch=master)
