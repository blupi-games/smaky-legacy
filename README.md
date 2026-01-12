# Smaky legacy games

Here you can find the binaries, the source codes and the assets of some very old
Smaky games written by Daniel Roux in CALM (Common Assembly Language for
Microprocessors). Everything is published under the GPLv3+ license. All games
are playable with the Smaky Infini emulator (only for Windows) available here:
https://www.smaky.ch/le-smaky-infini-libre/

| Game            | Year | Language | Directory |
| --------------- | ---- | -------- | --------- |
| BONG            | 1987 | CALM     | `bong`    |
| MUR             | 1987 | CALM     | `mur`     |
| PING            | 1987 | CALM     | `ping`    |
| TOTO SE PROMÈNE | 1991 | CALM     | `totop`   |

For more resources, look at the `doc/` directory of each game. You will find PNG
images (sams as the `.IMAGE` and `.COLOR` files) and PDF files (same as the
`.PAGE` files).

The assembly files use an unusual charset. You can use the `smascii` tool
provided by the [Fosfat project][1] in order to convert the files into
iso-8859-1 charset.

## BONG

![BONG](bong/doc/bong_aide.png)

## MUR

![MUR](mur/doc/mur_aide.png)

## PING

![PING](ping/doc/ping_aide.png)

## TOTO SE PROMÈNE

![TOTO SE PROMÈNE](totop/doc/toto_p001.color.png)

# Other

Read [README.rescue.md](README.rescue.md) for an HOWTO (backup, restore and read
a Smaky hard or floppy disk).

[1]: https://github.com/Skywalker13/Fosfat
