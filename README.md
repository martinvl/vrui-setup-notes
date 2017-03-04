On Ubuntu 16, here's how.

Most of this is based on http://doc-ok.org/?p=1508

## Step 1: Install SteamVR
Valve keeps breaking SteamVR, so you'll want to download an older version.

This is based on https://github.com/OSVR/OSVR-Vive/issues/20

1. Install `steamcmd` with `apt`
2. Run
```bash
$ steamcmd
    > login <username> 
        # enter password
        # enter guard
    > app_info_request 250820
    > app_license_request 250820
    > download_depot 250820 250823 9183475244149741279
    > quit
```

## Step 2: Install other stuff
```
$ sudo apt install libsdl2-dev
$ wget http://no.archive.ubuntu.com/ubuntu/pool/main/u/udev/libudev0_175-0ubuntu9_amd64.deb
$ sudo dpkg -i libudev0_175-0ubuntu9_amd64.deb
$ rm libudev0_175-0ubuntu9_amd64.deb
```

Also make sure `LD_LIBRARY_PATH` contains `/usr/lib` and friends:
```bash
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/lib:/usr/local/lib:/usr/lib/x86_64-linux-gnu/:/usr/local/lib/x86_64-linux-gnu/
```

## Step 3: Build VRUI
Download the build script to wherever, and run it:

```bash
$ wget http://idav.ucdavis.edu/~okreylos/ResDev/Vrui/Build-Ubuntu.sh
$ Build-Ubuntu.sh
```

This script will fail install Vive deps (without telling you), but we'll
rebuild it with the proper steamvr paths set.

Move to e.g. `$HOME/src/Vrui-4.2-006`, and run:

```bash
$ make clean
$ make STEAMVRDIR=$HOME/.steam/steamcmd/linux32/steamapps/content/app_250820/depot_250823 -j
$ sudo make STEAMVRDIR=$HOME/.steam/steamcmd/linux32/steamapps/content/app_250820/depot_250823 install
$ sudo make installudevrules
```

Disconnect and reconnect the USB-coord.

## Step 4: Verify everything works
Run `RunViveTracker.sh`, this should not fail, but will output a lot of
`ioctl (SFEATURE): Broken pipe`. Open another terminal and run
`DeviceTest localhost:8555 -t 0 -p`. This should output tracking data for the
HMI.

## Step 5: Try demos
First, perform room setup:
```bash
$ RoomSetup Vive
```

Next, try a demo:
```bash
$ ClusterJello -rootSection Vive
```

## Step 6: Fix prediction
Config is in `$HOME/.config/Vrui-4.2/Vrui.cfg`. Search for `predict`.

