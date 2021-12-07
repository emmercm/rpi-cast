# rpi-cast

A tool to automatically cast audio input from a Raspberry Pi.

## Overview

This tool will:

1. Listen on the default PulseAudio source (eg. a HiFiBerry DAC+ ADC 3.5mm jack)
2. Upon detecting enough volume (amplitude) (`-v, --volume-threshold`):
    1. Create a PulseAudio loopback for each loopback sink (`-l, --loopback-sink`)
    2. Cast to the Chromecast device defined (`-d, --device-name`), or the first one found
    3. Attempt to keep the cast as long as the silence timeout isn't met (`-s, --silence-timeout`)
3. Rinse, repeat!

Here's an example wiring diagram of how a [HiFiBerry DAC+ ADC Pro](https://www.hifiberry.com/shop/boards/hifiberry-dac-adc-pro/) can be used to cast audio from a turntable, as well as loopback output to a receiver:

![Diagram](./diagram.jpg)

## Dependencies

This script assumes you have a typical Raspbian install with `apt` and normal tools. It will install everything else it needs to run.

## Installation

```shell
wget https://raw.githubusercontent.com/emmercm/rpi-cast/main/rpi-cast && chmod +x rpi-cast
```

## Usage

```text
Usage: ./rpi-cast [OPTIONS]

Options:
  -c, --codec string               Audio codec to use
                                   Options: mp3, ogg, aac, opus, wav, flac
                                   Default: wav

  -d, --device-name string         Chromecast device name (eg. "Living Room TV")
                                   Default: the first device found, preferring
                                    groups over single devices

  -h, --hifiberry-overlay string   HiFiBerry overlay to configure
                                   Optional, only needs to be done once

  -l, --loopback-sink string       Pattern for PulseAudio sink to loopback to
                                   Default: alsa_output

  -s, --silence-timeout number     Stop casting after some seconds of silence
                                   Default: 300

  -v, --volume-threshold number    Minimum volume threshold to start casting
                                   Default: 0.005

HiFiBerry overlays:
  hifiberry-dacplusadc
  hifiberry-dacplusadcpro
```

## Automatic startup

To have `rpi-cast` automatically start on device boot, add the following to your `/etc/rc.local` file:

```shell
# (Optional) log all /etc/rc.local output to a temp file
exec &> /tmp/rc.local.log
set -x

# Start rpi-cast and log to a temp file
su pi -c '/home/pi/rpi-cast > /tmp/rpi-cast.log &'
```
