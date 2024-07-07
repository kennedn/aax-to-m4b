# AAX to M4B

## Description
Split aax audible file into m4b chapters using ffmpeg. Much faster than m4b-tool

## Prerequisites

- jq
- ffmpeg
- progress

e.g:

```bash
sudo apt install jq ffmpeg progress
```

# Usage

```
./aax_to_m4b.sh [options] <input_file>

Options:
  -a, --activation-bytes <bytes>  Activation bytes required for ffmpeg (required)
  -o, --out <directory>           Custom output directory (optional)
  -h, --help                      Print this help message and exit

Example:
  ./aax_to_m4b --activation-bytes 12345678 input_file.aax
```
