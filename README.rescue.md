# HOWTO: backup, restore and read a Smaky hard or floppy disk

There are multiple sorts of hard and floppy disks which were used by the
[Smaky][8] computers. It can be a bit difficult to read some disks according to
their very old interface, like for example the Winchester hard disks.

For Smaky 100 and more, it's easier because mostly are using SCSI interfaces and
usual 5"1/4 or 3"1/2 floppy devices.

## Hardware

For SCSI hard disk, you can use an _Adaptec SCSI Card 29160_ controller for
example. It's using a PCI slot and it's very common. This controller is
perfectly compatible with the Linux kernel. A lot of other controller will be
fine too, of course; just try...

For 3"1/2 floppy disks, it's still possible to buy floppy devices that can be
plugged in USB.

About the 5"1/4 floppy disks it depends the generation; probably that Smaky 6
and Smaky 8 floppy disks are not readable with common 5"1/4 devices.

## The rescue

> Prefer Linux, it's possible with Windows but it's more complicated. Maybe you
> have no choice (no kernel support for the hardware). If it's the case, use
> Windows until you have your raw dump and go back under Linux after that.

If you can plug your disk on the PC, then you have done most of the hard work.
Now the first step is to realize a raw dump of the disk. The extractions will be
done on the image disk and never with the disk itself.

The simple way is just to use `dd`. But first, look at `dmesg` where is you disk
in the system, the use `dd` as it:

```bash
# must be run as root (sudo)
dd if=/dev/sdX of=smaky.di
```

Replace the `X` of `sdX` by the right value according to the `dmesg` output.

**Be very careful here**, `if` is for input file and `of` for output file. If
you invert both options, you will destroy your data.

> Please, continue to read this article, it's not the best way...

**It's not always the best idea** to just use `dd` because maybe the disk is
damaged (and it's old, prone to mechanical, eletric or electronic failures).
Then the best way will be to use [myrescue][1] instead of `dd`. This utility
tries to read as much as possible and keep a map of damaged area in order to
improve the rescue (re-rescue) and to be quick and more safe.

**First**, read the manpage of [myrescue][1], the procedure is very important.
You can install it via `apt-get install myrescue`.

```bash
# must be run as root (sudo)
myrescue -S /dev/sdX smaky.di
# continue this procedure if errors were encountered
myrescue -f1 /dev/sdX smaky.di      # repeat this command until the number of errors seems to have converged
# if necessary, you can repeat with more retry on the damaged blocks
myrescue -f1 -r3 /dev/sdX smaky.di  # for example, it retries 3x
```

Once this step is done, shutdown your computer and unplug everything. You have
done the most critical step; congrats.

## The extraction

There are multiple possibilities here. You can try to load the `smaky.di` image
in the [Smaky Infini][3] emulator. It's very easy and it works most of time. But
if the [Smaky Infini][3] is unable to mount your image, it's an other story. It
needs to understand how works the FOS (File Operating System) in order to
recover with `EDISK` for example. An other disasvantage it's that it's not very
efficient to copy quickly all files from the Smaky system to the host system
with the emulator.

> The [Smaky Infini][3] works on Linux by using [WINE][4].

Then I've written a tool which is able to read Smaky FOS image disks just with
your Linux system. This tool works (for some parts) on Windows but in this case
you lose the great advantage of [FUSE][2]. Then I recommand to work only on
Linux.

This tool is named [Fosfat][5] and it's available in the official [Debian][6]
repositories (thanks to Didier Raboud). You can install it just by typing
`apt-get install fosfat` in your terminal. [Fosfat][5] is available since 2006.

There are four commands:

| commands   |                                                |
| ---------- | ---------------------------------------------- |
| `fosread`  | frontend to list/get files from the image disk |
| `fosmount` | mount the image disk in the filesystem (FUSE)  |
| `fosrec`   | restore deleted files                          |
| `smascii`  | convert Smaky text encoding to ISO-8859-1      |

Now, just mount your image disk somewhere.

```bash
# create a directory for the mount point
mkdir ~/smaky
# just mount
fosmount smaky.di ~/smaky
```

With `fosmount`, the image disk is mount in your filesystem in the `~/smaky`
directory. Now you can browse the directory like any other directory on your
system.

> There is a funny feature here, you can mount the disk image with
> `fosmount -i -j` options. In this case, the `.IMAGE` are converted (on the
> fly) to `.PBM` and `.COLOR` to `.XPM` images. These formats can be read
> directly with [GIMP][7]. The advantage is that it's not necessary to use the
> [Smaky Infini][3] emulator for converting these files.

When you have done, just unmount with `fusermount` or `umount`.

```bash
fusermount -u ~/smaky
```

### Fault tolerance

The [Fosfat][5] commands try to read as much as possible all blocks that looks
fine in the file system. Even if your disk is damaged, it's possible to read a
lot of files where the [Smaky Infini][3] will just refuse to mount your disk or
read files which look unavailable.

If a file can be read just partially, then [Fosfat][5] will read this file until
it's no longer possible. You can try to fix this file yourself because it's
possible to open/copy damaged files.

An other interesting command is `fosrec`. this command browses the whole image
disk and extract all files marked as deleted even if the files are cropped. It's
the undelete feature, available via the `fosread` command too.

### Text files

When you extract text files, you can directly open these files in a text editor
but a problem will appear. Extended characters are not decoded correctly. It's
because the [Smaky][8] uses it's own charset (just unknown by the text editors).
It looks like western europe ASCII but it's not that.

You can use the `smascii` command in order to convert the text from Smaky
encoding to ISO-8859-1 encoding.

```bash
smascii smaky_text unix_text --unix
```

By using `--unix`, the carriage return `\r` will be converted to line feed `\n`.
Yes, the [Smaky][8] uses only the carriage return like old Macintosh.

---

[1]: http://myrescue.sourceforge.net
[2]: https://en.wikipedia.org/wiki/Filesystem_in_Userspace
[3]: https://www.smaky.ch/le-smaky-infini-libre/
[4]: https://www.winehq.org
[5]: http://fosfat.schroetersa.ch
[6]: https://tracker.debian.org/pkg/fosfat
[7]: https://www.gimp.org
[8]: http://smaky.ch
