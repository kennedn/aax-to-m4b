#!/bin/bash

# Split an AAX file into chapters using ffmpeg

print_usage() {
  echo "Usage: $0 [options] <input_file>"
  echo ""
  echo "Options:"
  echo "  -a, --activation-bytes <bytes>  Activation bytes required for ffmpeg (mandatory)"
  echo "  -o, --out <directory>           Custom output directory (optional)"
  echo "  -h, --help                      Print this help message and exit"
  echo ""
  echo "Example:"
  echo "  $0 --activation-bytes 12345678 input_file.aax"
}

# Default values
activation_bytes=""
out_dir=""

# Parse flags
while [[ "$#" -gt 0 ]]; do
  case $1 in
    -a|--activation-bytes)
      if [[ -z $2 || $2 == -* ]]; then
        echo "Error: --activation-bytes requires a value"
        print_usage
        exit 1
      fi
      activation_bytes="$2"
      shift 2
      ;;
    -o|--out)
      if [[ -z $2 || $2 == -* ]]; then
        echo "Error: --out requires a directory path"
        print_usage
        exit 1
      fi
      out_dir="$2"
      shift 2
      ;;
    -h|--help)
      print_usage
      exit 0
      ;;
    -*)
      echo "Unknown option: $1"
      print_usage
      exit 1
      ;;
    *)
      if [[ -z "$input_file" ]]; then
        input_file="$1"
      else
        echo "Error: Multiple input files specified"
        print_usage
        exit 1
      fi
      shift
      ;;
  esac
done

# Check for mandatory arguments and input file type
if [[ -z "$input_file" ]]; then
  echo "Error: No input file specified"
  print_usage
  exit 1
fi

if [[ "${input_file##*.}" != "aax" ]]; then
  echo "Error: Input file must be of type .aax"
  print_usage
  exit 1
fi

# Infer default output directory if not specified
if [[ -z "$out_dir" ]]; then
  base_name=$(basename "$input_file" .aax)
  out_dir="./${base_name}"
fi

# Create the output directory if it doesn't exist
mkdir "$out_dir" &> /dev/null

# Read chapters and prepare ffmpeg split commands
ffmpeg_args=("-i" "pipe:0" "-v" "fatal" "-activation_bytes" "$activation_bytes")
while read -r start end title; do
  ffmpeg_args+=("-c" "copy" "-ss" "$start" "-to" "$end" "$out_dir/$title.m4b")
done <<< "$(ffprobe -i "$input_file" -print_format json -show_chapters 2> /dev/null\
    | jq -r '.chapters[] | .start_time + " " + .end_time + " " + (.tags.title | gsub(" "; "_"))')"

# Run ffmpeg command
pv "${input_file}" | ffmpeg "${ffmpeg_args[@]}"
