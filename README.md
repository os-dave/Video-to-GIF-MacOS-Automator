# Video-to-GIF-MacOS-Automator

A macOS Automator Quick Action that converts video files to optimized GIFs (<15MB) directly from your right-click menu. Simple, fast, and high-quality conversion with just one click.

## Prerequisites

Before installation, you'll need:
1. macOS (tested on Monterey and later)
2. [Homebrew](https://brew.sh/) package manager
3. FFmpeg installed via Homebrew:

   ```bash
   brew install ffmpeg
   ```

## Installation

1. Open Automator (from Applications or Spotlight)
2. Choose "Quick Action" when prompted for new document type
3. Configure the workflow settings at the top:
   - Set "Workflow receives" to "video files"
   - Set "in" to "any application"
4. Add a "Run Shell Script" action (search for it in the library)
5. Configure the shell script action:
   - Set "Shell" to "/bin/bash"
   - Set "Pass input" to "as arguments"
6. Copy and paste this script:
   
```bash
#!/bin/bash

PATH="/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/homebrew/bin:$PATH"

input_file="$1"
output_file="${input_file%.*}.gif"
palette="/tmp/palette.png"
target_size_mb=15
width=800  # Starting with a reasonable width

# Function to get file size in MB
get_size_mb() {
    size_bytes=$(stat -f %z "$1")
    echo "scale=2; $size_bytes / 1048576" | bc
}

# First pass: Generate palette
ffmpeg -i "$input_file" \
    -vf "fps=24,scale=$width:-1:flags=lanczos,palettegen=max_colors=256:stats_mode=single" \
    -y "$palette"

# Second pass: Create GIF with the palette
ffmpeg -i "$input_file" -i "$palette" \
    -lavfi "fps=24,scale=$width:-1:flags=lanczos [x]; [x][1:v] paletteuse=dither=sierra2_4a:diff_mode=rectangle" \
    -y "$output_file"

# Check size and reduce quality if needed
current_size=$(get_size_mb "$output_file")
while (( $(echo "$current_size > $target_size_mb" | bc -l) )); do
    # Reduce width by 10%
    width=$(echo "scale=0; $width * 0.9 / 1" | bc)
    
    # Regenerate with new width
    ffmpeg -i "$input_file" \
        -vf "fps=24,scale=$width:-1:flags=lanczos,palettegen=max_colors=256:stats_mode=single" \
        -y "$palette"
    
    ffmpeg -i "$input_file" -i "$palette" \
        -lavfi "fps=24,scale=$width:-1:flags=lanczos [x]; [x][1:v] paletteuse=dither=sierra2_4a:diff_mode=rectangle" \
        -y "$output_file"
    
    current_size=$(get_size_mb "$output_file")
    
    # Prevent infinite loop if size can't be reduced enough
    if [ $width -lt 200 ]; then
        break
    fi
done

# Clean up
rm "$palette"

final_size=$(get_size_mb "$output_file")
osascript -e "display notification \"GIF created (${final_size}MB) at: $output_file\" with title \"Video to GIF Converter\""
```
7. Save the Quick Action as "Convert to GIF"

## Usage

1. Right-click any video file in Finder
2. Go to Quick Actions (or Services in older macOS versions)
3. Select "Convert to GIF"
4. Your GIF will be created in the same location as the video file

## Features

- Creates high-quality GIFs optimized under 15MB
- 24fps for smooth motion
- Smart width scaling
- High-quality dithering
- Optimized color palette
- Maintains aspect ratio
- Shows notification when complete

## Troubleshooting

If the Quick Action doesn't appear:
1. Check System Settings > Extensions > Finder
2. Ensure "Convert to GIF" is enabled
3. Verify FFmpeg installation by running in Terminal:
   
   ```bash
   which ffmpeg
   ```
5. Check permissions in System Settings > Privacy & Security > Automation

## License

MIT License - feel free to use and modify as needed.

## Acknowledgments

- Uses [FFmpeg](https://ffmpeg.org/) for video processing
- Inspired by the need for quick, high-quality GIF creation on macOS
