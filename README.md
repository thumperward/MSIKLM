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
msiklm red,white,blue,sky,purple,blue,yellow
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

```sh
msiklm color[,color2,color3,color4,...] [brightness] [mode]
```

| option | value | example |
| ------ | ----- | ------- |
| color(s) | predefined color or RGB values ([R;G;B] or hex code): if specifying multiple colors, leave no spaces between values. | `msiklm green,red,blue` |
| brightness | `low`, `medium`, `high`, or `rgb`. | `msiklm green medium` |
| mode | `normal`, `gaming`, `breathe`, `demo`, `wave`. If no colors are specified, the existing colors are used. | `msiklm wave` |

### Colors

The predefined supported colors are:

-   `none` / `off`
-   `red`
-   `orange`
-   `yellow`
-   `green`
-   `sky`
-   `blue`
-   `purple`
-   `white`

### Zones

Up to seven zones are supported, depending on the device:

-   left
-   middle
-   right
-   logo
-   front left
-   front right
-   trackpad

Each zone is assigned a color in order from the comma-separated list of values
given. Any remaining ones are left unchanged. If there is only one supplied
color, it is used for the first three zones. (i.e. `green` as single argument
is equivalent to `green,green,green`). The last four colors are set only when a
comma-separated list is supplied with values for those positions.

Instead of a predefined color, each color can alternatively be set in full RGB
notation; the color values have to be either enclosed by brackets and separated
by semicolons, e.g. 'green' is equivalent to using `[0;255;0]`, or hex code
notation (`0x000000` to `0xFFFFFF`) where the respective values have to be
selected accordingly. Note that quotation marks are required around RGB values
to avoid the brackets being processed by the shell.

Custom color definitions and predefined ones can be mixed.

## Brightness

The brightness can be used to switch to a different way of communicating with
the keyboard. Besides technical details (see `set_color()` in `msiklm.c` for
further details if you are interested in them), it improves the compatibility
with different devices, however the brightness has to be explicitly given. For
example `msiklm green` will set the color green using its RGB values (i.e.
red=0, green=255, blue=0 or `0x00FF00` in hex code notation) while
`msiklm green high` does basically the same but using a different way which
might be supported by keyboards that do not support full RGB color selection.
As I do not have a bunch of different notebook available to test them, I cannot
say which command will work at which keyboard.

The brightness argument can only be set to low, medium and high if _no_ custom
RGB value is given, while not supplying it is equivalent to setting it to
`rgb`. The reason for this is two-fold: First, it makes little to no sense to
explicitly define the color and to give a brightness as well,

### Other options

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

-   Let the program read from a config file.
-   Rewrite in Python.
-   Properly package.
-   CI/CD.
