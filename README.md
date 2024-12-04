# Video-to-GIF-MacOS-Automator

A simple yet powerful macOS Automator workflow that converts video files to high-quality GIFs with size optimization. Right-click any video file and convert it to a GIF instantly!

## Features

- ✨ One-click video to GIF conversion
- 🎯 Automatic size optimization (targets <15MB)
- 🖥️ Right-click menu integration
- 🎨 High-quality output with smart dithering
- 📏 Automatic aspect ratio preservation
- 🚀 Uses FFmpeg for reliable conversion

## Prerequisites

Before you begin, ensure you have:
- macOS (tested on Monterey and later)
- [Homebrew](https://brew.sh/) package manager
- FFmpeg installed via Homebrew:
  
  ```bash
  brew install ffmpeg
  ```

## Installation

1. Download the workflow file
2. Double-click to install in Automator
3. Allow any required permissions in System Preferences/Settings
4. The converter will appear in Finder's right-click menu under "Quick Actions"

## Usage

1. Right-click any video file in Finder
2. Select "Convert to GIF" from the Quick Actions menu
3. Wait for the conversion to complete
4. Find your GIF in the same location as the source video

## Technical Details

The converter uses these optimized settings:
- 24fps for smooth motion
- Smart width scaling (starts at 800px)
- High-quality dithering (sierra2_4a)
- Optimized color palette generation
- Automatic size adjustment to stay under 15MB

## Troubleshooting

If the Quick Action doesn't appear:
1. Check System Settings > Extensions > Finder
2. Ensure "Convert to GIF" is enabled
3. Verify FFmpeg installation: `which ffmpeg`
4. Check permissions in System Settings > Privacy & Security > Automation

## Contributing

Contributions are welcome! Feel free to:
- Open issues for bugs or suggestions
- Submit pull requests with improvements
- Share your use cases and feedback

## License

MIT License - feel free to use and modify as needed.

## Acknowledgments

- Uses [FFmpeg](https://ffmpeg.org/) for video processing
- Inspired by the need for quick, high-quality GIF creation on macOS
