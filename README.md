# Check Point 3900 Serial Console for macOS

A small Bash utility that automatically detects a USB serial console connected to a Mac and opens a `screen` session at **9600 baud**.

It is intended for connecting to the console port of a **Check Point 3900 Security Appliance**, but it can also be useful with other appliances that use the same serial settings.

## Features

- Automatically detects serial devices under `/dev/cu.*`
- Filters USB and serial device names
- Automatically selects the device when only one matching port is found
- Displays a selection menu when multiple matching ports are available
- Shows the `screen` exit commands before connecting
- Waits for a keypress before starting the session
- Uses the console settings required for the Check Point 3950:
  - Baud rate: `9600`
  - Data bits: `8`
  - Parity: `None`
  - Stop bits: `1`
  - Flow control: `None`

## Requirements

- macOS
- A USB cable connected to the appliance console port
- The appropriate USB serial driver, if required by the adapter or appliance
- The `screen` command, normally included with macOS

Verify that `screen` is available:

```bash
command -v screen
```

## Installation

Clone the repository or download the script, then make it executable:

```bash
chmod +x checkpoint-console.sh
```

Run it from the repository directory:

```bash
./checkpoint-console.sh
```

## Optional system-wide installation

To run the utility from any directory, copy it to `/usr/local/bin`:

```bash
sudo mkdir -p /usr/local/bin
sudo cp checkpoint-console.sh /usr/local/bin/checkpoint-console
sudo chmod +x /usr/local/bin/checkpoint-console
```

You can then start it with:

```bash
checkpoint-console
```

## Usage

1. Connect the Mac to the USB console port of the Check Point 3950.
2. Open Terminal.
3. Run:

```bash
checkpoint-console
```

4. Review the displayed exit instructions.
5. Press any key to start the `screen` session.
6. If the console appears blank, press `Enter` once or twice.

## Exiting the console session

Inside `screen`, press the following keys in sequence:

1. `Ctrl-A`
2. `Ctrl-\`
3. Press `Y` to confirm

Do not press all keys at the same time. Press `Ctrl-A` first, release the keys, and then press `Ctrl-\`.

## Troubleshooting

### No USB serial port was found

List all available serial devices:

```bash
ls -l /dev/cu.*
```

Disconnect the USB cable, run the command, reconnect the cable, and run it again. The newly appearing device is normally the console device.

Typical names include:

```text
/dev/cu.usbserial-XXXX
/dev/cu.usbmodemXXXX
/dev/cu.SLAB_USBtoUART
/dev/cu.wchusbserialXXXX
```

If no new device appears:

- Try another USB cable. Some USB cables provide power only and do not carry data.
- Connect directly to the Mac instead of using a USB hub or docking station.
- Verify whether the USB serial adapter requires a macOS driver.
- Check **System Settings > Privacy & Security** for a blocked system extension.
- Open **System Information > USB** and verify that the device is detected.

### Permission denied

Check the device permissions:

```bash
ls -l /dev/cu.*
```

On macOS, serial devices are normally accessible without `sudo`. Avoid running `screen` with `sudo` unless it is strictly necessary.

### The port is already in use

Look for existing `screen` sessions:

```bash
screen -ls
```

Reconnect to an existing session:

```bash
screen -r
```

Or terminate a detached session:

```bash
screen -S SESSION_ID -X quit
```

Replace `SESSION_ID` with the identifier shown by `screen -ls`.

### The console is connected but no output is displayed

- Press `Enter` once or twice.
- Confirm that the session is using `9600` baud.
- Confirm that you selected the correct `/dev/cu.*` device.
- Verify that the cable is connected to the appliance console port, not a regular USB or network port.
- Close any other terminal application that may be using the same serial device.

## Security notes

- The script runs only local commands and does not transmit data.
- Review scripts downloaded from the Internet before executing them.
- Avoid committing console logs because they may contain sensitive configuration details.

## Disclaimer

This is an independent utility and is not an official Check Point Software Technologies product. Product names and trademarks belong to their respective owners.
