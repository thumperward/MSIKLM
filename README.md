# MSIKLM

The MSI Keyboard Light Manager (MSIKLM) is an easy-to-use tool that allows to
configure the SteelSeries keyboards of MSI gaming notebooks with Linux / Unix
in almost the same way as the SteelSeries Engine can do using Windows.

## Installation

MSIKLM requires gcc, make, and libusb. There are no other requirements.

Install the dependencies with:

```sh
sudo apt install -y build-essentials libhidapi-dev
```

To permit the tool to be run as non-root, create a file at
`/etc/udev/rules.d/99-steelseries.rules` containing the following:

```cfg
SUBSYSTEM=="usb", ATTR{idVendor}=="1770", ATTR{idProduct}=="ff00", MODE="666"
```

Then run `sudo udevadm trigger`.

Run `make install` to build the tool and install it in `~/.local/bin/`.

Test the connection:

```sh
msiklm test
```

Run `make uninstall` to remove the tool.

To run the tool when you log in, add the following to your `~/.profile`:

```sh
msiklm red,white,blue
```

## Distribution

Currently, there are also the following packages available to install MSIKLM:

-   Arch Linux via [the AUR repository](https://aur.archlinux.org/packages/msiklm-git/)
-   FreeBSD via [the FreeBSD package repository](https://www.freshports.org/sysutils/msiklm/)

    ```sh
    pkg install msiklm
    ```

## Usage

MSIKLM is a pure command line application, however its keyboard illumination
control functionality is encapsulated such that it could easily be integrated
into a graphical user interface. However, I neither wrote one for it nor I plan
to do so. It is quite easy to use, and here is how to use it. It always has to
be called with at least one argument, i.e. running it without one will result
in an error. Here is an overview over the valid commands:

|command | valid arguments | example |
|--------|-----------------|---------|
|msiklm \<color\>                                         | either a predefined color or arbitrary RGB values ([R;G;B] or hex code), cf. explanation below | msiklm green                    |
|msiklm \<color1\>[,\<color2\>,\<color3\>,\<color4\>,...] | same as single color (important: no space between the colors!), cf. explanation below          | msiklm green,blue,red           |
|msiklm \<mode\>                                          | normal, gaming, breathe, demo, wave                                                            | msiklm wave                     |
|msiklm \<color\> \<brightness\>                          | color as above, brightness can be off, low, medium, high, rgb                                  | msiklm green high               |
|msiklm \<color\> \<mode\>                                | same as above                                                                                  | msiklm green,blue,red wave      |
|msiklm \<color\> \<brightness\> \<mode\>                 | same as above                                                                                  | msiklm green,blue,red high wave |

The predefined supported colors are: none, off (equivalent to none), red,
orange, yellow, green, sky, blue, purple and white. The color configuration can
also be performed in an more advanced way: At most seven zones are supported
(as long as supported by your device) and the respective colors have to be
supplied in the following order: left, middle, right, logo, front_left, front_
right and mouse. If there is only one supplied color, it is reused for the
first three zones, the remaining ones stay unchanged (i.e. green as single
argument is equivalent to green,green,green). The colors have to be separated
with no spaces between the colors, simply add a comma for a new zone. The last
four colors are fully optional, i.e. they are set if and only if they are
supplied. Consequently, if you want to change the last color (mouse), you have
to specify a color for all zones. Instead of a predefined color, each color can
alternatively be set in full RGB notation; the color values have to be either
enclosed by brackets and separated by semicolons, e.g. 'green' is equivalent to
using [0;255;0], or hex code notation can be used (0x000000 to 0xFFFFFF) where
the respective values have to be selected accordingly. It is possible to mix
these explicit color definitions with predefined ones, e.g. you can select a
custom color for the left zone and use predefined for the others by supplying
[R;G;B],green,blue. Please note that it might be necessary to put quotation
marks around explicit color definitions, otherwise the argument might not be
properly processed by the shell.

Further, the brightness argument can only be set to low, medium and high if
_no_ custom rgb-color is given, while not supplying it is equivalent to supply
'rgb'. The reason for this is two-fold: First, it makes little to no sense to
explicitly define the color and to give a brightness as well, second the
brightness can be used to switch to a different way of communicating with the
keyboard. Besides technical details (see function set_color() in msiklm.c for
further details if you are interested in them), it improves the compatibility
with different devices, however the brightness has to be explicitly given. For
example 'msiklm green' will set the color green using its rgb-values (i.e.
red=0, green=255, blue=0 or 0x00FF00 in hex code notation) while 'msiklm
green high' does basically the same but using a different way which might be
supported by keyboards that do not support full rgb-color selection. As I do
not have a bunch of different notebook available to test them, I cannot say
which command will work at which keyboard.

Additionally, there are three extra commands that might be useful if something
does not work:

-   `msiklm help`: shows the program's help
-   `msiklm test`: tests if a compatible keyboard is found
-   `msiklm list`: lists all found hid devices; this might be helpful if
    your keyboard is not detected by MSIKLM

## Developer information

The source code is split into three files:

-   Main application (`main.c`) that converts the input
-   Small library that contains the main features (`msiklm.h` and `msiklm.c`).
    This provides a simple C API and hence allows an easy integration into
    different programs like maybe a small graphical user interface.

## TODO

-   Let the program read from a config file (by default when started with no
    arguments), which then simplifies the udev rule and means all the shell
    scripts can go away. Though for now it's trivial to add to your `bashrc`.
-   Rewrite in Python.
-   Properly package.
-   CI/CD.
