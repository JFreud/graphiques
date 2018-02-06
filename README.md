# GRAPHICS NOTES, Spring 2018
### Jerome Freudenberg

### Table of Contents
DATE | AIM
:---:| ---
1/31 | []
2/2  | [image-file-formats]
2/5  | []

## 2.1.18

### Image File Formats
- Uncompressed formats contain data for each pixel (BMP, TIFF, RAW)
- Compressed formats use a compression algorithm to minimize file size
- Lossless compression algorithms contain enough information to exactly recreate the original image (PNG (Portable Network Graphics), GIF (Graphics Interchange Format)
- Lossy compression algorithms do not retain all the details of the original image, JPEG (Joint Photographic Experts Group)
- PPM (Portable PixMAP)
  1. Lossless, uncompressed raster format
  2. Pixel data is represented by RGB triplets in either ASCII or binary
  3. All whitespace is equivalent
  4. Example file:
    1. P3
    2. 4 3 (dimensions)
    3. 255 (max value; if not 255, it will scale)
    4. 255 0 0   255 0 0   255 0 0   255 0 0
       0 255 0   0 255 0   0 255 0   0 255 0
       0 0 255   0 0 255   0 0 255   0 0 255

## 1.31.18 Peering into the Depths of Color
- Electron gun screen gray color, shoot to energize for intensity
### Color Depth
- The amount of data used to represent a single pixel
- Can be used to rep screen/monitor
size | color option
:---:| ---
1 bit | 1 color on/off
2 bit | 1 color on/off, plus 1 bit intensity
3 bit | RGB; on/off
4 bit | RGB w/ intensity
6 bit | RGB, each w/ intensity
3 byte | 1 byte for each color; each with 256 possible intensities

### Other Color Spaces
- RGBA (RGB + alpha for transparency)
- HSB (Hue, Saturation, Brightness)

### Image File Formats
- Raster: represent images as grid of pixels
- Vector: represent images as series of drawing instructions (infinitely scalable)
