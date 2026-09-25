# CSSE4011 Student Workspace

This repository acts as the workspace for students developing application
throughout CSSE4011. It uses the [West T2 topology][zephyr-west-t2] and pulls
in the [CSSE4011 Zephyr SDK][csse4011-sdk].

## Setup
For full setup instructions, see the CSSE4011 Workspace Setup guide on Ed
Discussion.

## Usage
This repo, available locally as `firmware` when set up using `west`, will be
your working directory as you develop code throughout the semester.

Build your applications under `apps/`.

Assuming you've got the a working Zephyr installation, you can build the test
blinky application, available within `apps/prac0`.

```sh
(.venv) sam@raskolnikov:~/csse4011 (main)
$ west build -p -b nrf52840dk/nrf52840 -d firmware/apps/prac0/build prac0
...
[144/144] Linking C executable zephyr/zephyr.elf
Memory region         Used Size  Region Size  %age Used
           FLASH:       18652 B         1 MB      1.78%
             RAM:        4480 B       256 KB      1.71%
        IDT_LIST:          0 GB        32 KB      0.00%
Generating files from /home/sam/csse4011/firmware/apps/prac0/build/zephyr/zephyr.elf for board: nrf52840dk
```

Note that we are using `-p` for pristine and `-b` to select our board. I
strongly recommend using `pristine` builds to avoid a family of troublesome
errors. Additonally we use the `-d` switch to specify our build directory.
I strongly recommend using this to keep your prac build artifacts adjacent to
the prac source. You'll find yourself regularly digging through build artifacts
to debug devicetree and Kconfig errors.

You can flash the application to the board as follows:

```sh
west flash -d prac0/build
```

Again we use the `-d` switch to specify the location of our build artifacts;
when using a mono-repo for multiple applications, you can end up flashing the
wrong application if you're not careful.

When working on subsequent pracs, just build from the corresponding folder:

```sh
(.venv) sam@raskolnikov:~/csse4011/firmware (main)
$ west build -p -b nrf52840dk/nrf52840 -d prac1/build prac1
```

## Development

This section describes some additional tools that may be useful during
development.

### Just

If you want to save yourself some effort when repeatedly running commands,
I suggest using either `make` or `just` to write some simple recipes. I find
`just` more forgiving.

A simple `Justfile` is included in this repo and contains a parameterised
recipe for building arbitrary targets (assuming you follow the flat project
structure described above). Assuming you've installed `just`, you can run:

```
just build prac0
```

You can configure the `default_board` in the `Justfile`.

[zephyr-west-t2]:https://docs.zephyrproject.org/latest/develop/west/workspaces.html#west-t2
[csse4011-sdk]:https://github.com/skwort/csse4011-sdk
