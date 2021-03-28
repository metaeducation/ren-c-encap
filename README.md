# ren-c-encap

Embed Zipped data into ELF, Windows PE, or Generic Binaries

> This is a separate repository kept for tests and tracking issues of the
> Ren-C encap script:
>
> https://github.com/metaeducation/ren-c/blob/master/scripts/encap.reb
>
> Because de-encapping needs to be baked into the executable itself, the whole
> script is currently turned into C constant data and compiled in.  For this
> reason, the script itself is not kept in this repository at this time.  This
> makes it easier to keep in sync with the rest of the code.
## Description

Encapping grafts data into an already-compiled executable, to add resources to
it "after the fact".  Note that there are different executable formats used on
different operating systems, each with a header that tells the operating system
how to interpret the file:

* Linux: https://en.wikipedia.org/wiki/Executable_and_Linkable_Format
* Windows: https://en.wikipedia.org/wiki/Portable_Executable
* OS X: https://en.wikipedia.org/wiki/Mach-O

A "naive" form of adding data into an executable is to append the data at the
tail, which generally does not affect operation:

http://stackoverflow.com/a/5801598/

This is a common approach, yet it has some flaws.  e.g. on Linux, running the
`strip` command will see the added data as irrelevant, and remove it.  Other
manipulations like adding an icon resource may add the icon resource data to
the end.  There are other things, like executable compression (although some
executable compressors are aware of this well-known embedding tactic, and
account for it).

It may be reasonable to say that it is the burden of those doing executable
manipulations to de-encap it, do the modification, and then re-encap the
executable.  But all things being equal, it's desirable to find ways to
slipstream the information into the "valid/known" resource logic of the OS.

This can be done with OS-specific tools or system calls, but the advantage of
writing it standalone in Ren-C is that it reduces the dependencies.  It allows
encapping of executables built on a platform different than the one you are
running on.  So attempts are made here to manipulate the published formats with
usermode code itself.

For formats not supported currently by the encapper, the simple appending
strategy is used.
