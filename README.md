# GRAPHICS NOTES, Spring 2018
### Jerome Freudenberg

### Table of Contents
DATE | AIM
:---:| ---
1/31 | [Peering into the Depths of Color](#13118-peering-into-the-depths-of-color)
2/2  | [Image File Formats](#image-file-formats)
2/5  | [Bresenham's Line Algorithm](#2518-bresenhams-line-algorithm)
2/6  | [More Lines](#2618-testing-the-midpoint)
2/7  | [Even More](#other-octants)
2/12 | [Representing Image Data](#21218-representing-image-data)
2/13-4 | [Matrix Math for Graphics](#21318-matrix-multiplication)
2/26-7 | [Transformations](#22618-transformations)
3/5-8  | [Curves](#3518-curves)

## 3.5.18 Curves
- Define a curve with respect to a totally independent variable
- Define a curve as a system of equations with an independent variable (t)
  - x = f(t)
  - y = g(t)
  - z = h(t)
- Line (x<sub>0</sub>, y<sub>0</sub>) -> (x<sub>1</sub>, y<sub>1</sub>), t: 0 -> 1
  - f(t) = (Δx)t + x<sub>0</sub>
  - g(t) = (Δy)t + y<sub>0</sub>
- General Parametric Framework
  - for t: 0 -> 1
    - x = f(t)
    - y = g(t)
    - z = h(t)
    - add(x, y, z)
- When lower video game settings -> fewer edges so more angular
- Circles
  - x = rcos(theta) + x<sub>c</sub>
  - y = rsin(theta) + y<sub>c</sub>
  - theta: 0 -> 2π
  - theta = 2πt
- Splines
  - Splines are designed in such a way that they can smoothly connect curves
  - We will only use cubic splines
  - Hermite, Bezier
- Hermite Curves
  - Given information
    - P<sub>0</sub>, P<sub>1</sub>: endpoints
    - R<sub>0</sub>, R<sub>1</sub>: rates of change @ each endpoint
    - f(t) = at<sup>3</sup> + bt<sup>2</sup> + ct + d: points on the curve
    - f'(t) = 3at<sup>2</sup> + 2bt + c: rates of change
  - Mapping
    - when t = 0:
      - f(t) = d: P<sub>0</sub>
      - f'(t) = c: R<sub>0</sub>
    - when t = 1:
      - f(t) = a + b + c + d: P<sub>1</sub>
      - f'(t) = 3a + 2b + c: R<sub>1</sub>
      - system of equations
```
|0 0 0 1|  |a|         |P0|
|1 1 1 1|  |b|  _____  |P1|
|0 0 1 0|  |c|  _____  |R0|
|3 2 1 0|  |d|         |R1|
    H       C           G
```
  - H * C = G
  - H<sup>-1</sup> * G = C
  - will fix later lmao:
```
|2  -2  1   1|  |x0|                      |a<sub>x</sub>|
|-3  3  -2 -1|  |x1| _______              |b<sub>x</sub>|
|0   0  1   0|  |R0| _______              |c<sub>x</sub>|
|1   0  0   0|  |R1|                      |d<sub>x</sub>
      H^-1
```
- Bezier curves
  - Name of the game is still what are the coefficients
  - Curve of degree n needs n + 1 input points
  - Line
    - P<sub>0</sub> -> P<sub>1</sub>
    - P<sub>t</sub> = (1-t)P<sub>0</sub> + tP</sub>1</sub>
      - As t moves, drags line over; P<sub>1</sub> gets more influence on the line
      - As t: 0 -> 1, Pt moves along the line
  - Quadratic
    - ![Quad Bezier gif](https://upload.wikimedia.org/wikipedia/commons/3/3d/B%C3%A9zier_2_big.gif)
    - P<sub>0</sub> -> P<sub>1</sub> -> P<sub>2</sub>
    - Q<sub>0</sub> moves along first line towards Q<sub>1</sub> which goes down on second line (P<sub>1</sub> to P<sub>2</sub>
    - Q<sub>0</sub> moves linearly along P<sub>0</sub>P<sub>1</sub>
    - Q<sub>1</sub> moves linearly along P<sub>1</sub>P<sub>2</sub>
    - Q<sub>t</sub> moves linearly along Q<sub>0</sub>Q<sub>1</sub>
    - Q<sub>0</sub> = (1 - t)P<sub>0</sub> + tP<sub>1</sub>
    - Q<sub>1</sub> = (1 - t)P<sub>1</sub> + tP<sub>2</sub>
    - Q<sub>t</sub> = (1 - t)Q<sub>0</sub> + tQ<sub>1</sub> = (1 - t)<sup>2</sup>P<sub>0</sub> + 2t(1-t)P<sub>1</sub> + t<sup>2</sup>P<sub>2</sub>
  - Cubic
    - ![Cubic Bezier Gif](https://upload.wikimedia.org/wikipedia/commons/d/db/B%C3%A9zier_3_big.gif)
    - 4 points: P<sub>0</sub>, P<sub>1</sub>, P<sub>2</sub>, P<sub>3</sub>
    - R<sub>t</sub> moves linearly along R<sub>0</sub>R<sub>1</sub>
    - R<sub>0</sub> moves along the quadratic Q<sub>0</sub>Q<sub>1</sub>
    - R<sub>1</sub> moves along the quadratic Q<sub>1</sub>Q<sub>2</sub>
    - R<sub>t</sub> = (1-t)R<sub>0</sub> + tR<sub>1</sub> = (1-t)<sup>3</sup>P<sub>0</sub> + 3t(1-t)<sup>2</sup>P<sub>1</sub> + 3t<sup>2</sup>(1-t)P<sub>2</sub> + t<sup>3</sup>P<sub>3</sub>
      - do it twice for x and y coordinates
      - expand to (-P<sub>0</sub> + 3P<sub>1</sub> - 3P<sub>2</sub> + P<sub>3</sub>)t<sup>3</sup> + (3P<sub>0</sub> - 6P<sub>1</sub> + 3P<sub>2</sub>)t<sup>2</sup> + (-3P<sub>0</sub> + 3P<sub>1</sub>)t + P<sub>0</sub> 
        - a = -P<sub>0</sub> + 3P<sub>1</sub> - 3P<sub>2</sub> + P<sub>3</sub>
        - b = 3P<sub>0</sub> - 6P<sub>1</sub> + 3P<sub>2</sub>
        - c = -3P<sub>0</sub> + 3P<sub>1</sub>
        - d = P<sub>0</sub> 
    - Bezier Matrix
```
|-1  3 -3  1 | |P0|         |a|
| 3 -6  3  0 | |P1| ______  |b|
|-3  3  0  0 | |P2| ______  |c|
| 1  0  0  0 | |P3|         |d|
```
    - 
  -![Quint Curve](https://upload.wikimedia.org/wikipedia/commons/0/0b/BezierCurve.gif)


## 2.26.18 Transformations
- Translations
  - see below
- Dilations
  - (x, y, z) -> (ax, ay, az)
  - diagonals a w/ 1 in the last. Everything else 0. (Similar to identity matrix)
  - could make it (x, y, z) -> (ax, by, cz)
  - will be shifted so translate back
- Rotation
  - (x, y)R<sub>theta</sub> -> 
  - x = rcos(phi)
  - y = rsin(phi)
  - x1 = rcos(phi + theta) = rcos(phi)cos(theta) - rsin(phi)sin(theta) = x1 = xcos(theta) - ysin(theta)
  - y1 = rsin(phi + theta) = rsin(phi)cos(theta) + rcos(phi)sin(theta) = y1 = ycos(theta) + xsin(theta)
```
  |cos  -sin  0   0|  |x|         |xcos - ysin|
  |sin  cos   0   0|  |y|  ______ |ycos + xsin|
  |0    0     1   0|  |z|  ______ |     z     |
  |0    0     0   1|  |1|         |     1     |
  Rotation of theta degrees about the z-axis
```
  - To rotate around another axis just switch em up
    - To rotate around x, switch x with z
      - y1 = ycos - zsin
      - z1 = zcos + ysin
    - To rotate around y, x becomes vertical and z horizontal
      - z1 = zcos - xsin
      - x1 = xcos + zsin
- Combining Transformations
  - E<sub>0</sub>: Edges; T: Translate; R: Rotate; S: Scale
  - T * E<sub>0</sub> = E<sub>1</sub>
  - R * E<sub>1</sub> = E<sub>2</sub> : Translated, then rotated
  - S * E<sub>2</sub> = E<sub>3</sub>
  - E<sub>3</sub> = (S * R * T) * E<sub>0</sub>
    - 2 4x4s and 1 4xN multiplication--saving a lot of work
  
## 2.13.18 Matrix Multiplication
- A x B ⋅ B x D => A x C
  - number of rows in second matrix must be equal to the number of columns in the first
  - not commutative 
- Calulation
  - take the dot product of each row in the first with each column in the second to find each entry in the product
- Multiplicative Identity
  - M ⋅ I = M
  - Square matrix with an all 1 diagonal and 0s everywhere else
- Performing Transformations
  - affine transformations; preserves the number of vertices and the orientation
    - important for edge matrix--it's ordered and we don't want to lose points
  - E: Edge matrix
  - T: Transformation matrix (must be square)
  - T ⋅ E, because we want to preserve number of points
  - Translation
    - (x, y, z) T<sub>(a, b, c)</sub>&rarr; (x+a, y+b, z+c)
    - Can't add because we want an all-purpose transformation
    - add a fourth term with value 1 to point matrix in order to add constants
  - Edge Matrices: 4 x N
  - Transformation Matrcies: 4 x 4

## 2.12.18 Representing Image Data
- Good way to keep track of all points in your image?
  - Array of Points
  - Map type thing of point and its connections
  - n x n Matrix of Coordinates w/ 1 if connected and 0 if not
- Edge List (matrix)
  - Each point is added to a list such that every 2 points form a line
  - P<sub>0</sub>P<sub>1</sub>, P<sub>2</sub>P<sub>3</sub>, ... , P<sub>n-1</sub>P<sub>n</sub>
  - Triangle ABC: [A, C, A, B, B, C]
  - This allows for disjoint shapes, just add the other edges to the list
  - The downside is repeating a lot of values
    - The answer is we don't care (storage is cheap!)
  - If you think about it, they're two dimensional arrays
  - Let's allow for z-coordinates at the start (add the values to the point)
    - These become important for rotations
    - Doesn't mean we have to plot z; we're still drawing 2-dimensionally
  - Basis of storing images

## 2.6.18 Testing the Midpoint

### Continuing Bresenham
- Will look at ways to optimize algorithm in future
  - The less work the computer has to do, the faster the drawing goes

### How do we tell if we're above or below? Test (x + 1, y + 1/2)
  - y = mx + b -> mx - y + b = 0 -> (Δy)x - (Δx)y + (Δx)b
    - A = Δy, B = -(Δx), C = (Δx)b
    - 0 = Ax + By + C
    - f(x, y) = Ax + By + C
  - If on line, f(x, y) = 0
    - if y-value above line, then f(x, y) < 0, since we're multiplying by -(Δx)
    - if y-value below line, then f(x, y) > 0
  - First draft Algorithm (x<sub>0</sub>, y<sub>0</sub>) -> (x<sub>1</sub>, y<sub>1</sub>)
    - x = x<sub>0</sub>, y = y<sub>0</sub>  
    - d = f(x + 1, y + 1/2)  
    - while (x <= x<sub>1</sub>)  
        - plot (x, y)  
        - x++  
        - if d > 0  
          - y++  
        - d = f(x + 1, y + 1/2)  
  - Room for improvement in function f
    - when we add 1 to x: d = d + A
    - when we add 1 to y: d = d + B
    - so we don't actually have to do f every time
  - Second draft Algorithm (x<sub>0</sub>, y<sub>0</sub>) -> (x<sub>1</sub>, y<sub>1</sub>)
    - x = x<sub>0</sub>, y = y<sub>0</sub>  
    - d = f(x + 1, y + 1/2)  
    - while (x <= x<sub>1</sub>)  
        - plot (x, y)  
        - if d > 0
          - y++
          - d += B
        - x++
        - d += A
    - d<sub>0</sub> = f(x<sub>0</sub> + 1, y<sub>0</sub> + 1/2)
      - A(x<sub>0</sub> + 1) + B (y<sub>0</sub> + 1/2) + C
      - Ax<sub>0</sub> + By<sub>0</sub> + C + A + 1/2 B
      - d<sub>0</sub> = A + 1/2 B
      - 2d<sub>0</sub> = 2A + B
    - Replace in Third Draft
      - d = 2A + B, d += 2B, d += 2A
### Other Octants
  - if octant II: 
    - mp becomes (x + 1/2, y + 1) 
    - d = A + 2B
    - condition becomes if d < 0
  - octant VIII
    - mp becomes (x + 1, y - 1/2)
    - d = 2A - B
    - condition if d < 0
  - only need octants I, II, VII, VIII (since line goes into other octants)
  
  
  
  
## 2.5.18 Bresenham's Line Algorithm

### How to draw a line?
- Pixels digital, not analog. We don't have all the numbers we do in math. Have to decide which pixel best approximates a line.
- Use octants instead of quadrants
- Assume drawing a line in octant I
  - 0 < m < 1
  - Rules:
    - adjacent
    - to the tight
    - x >= y
  - Pixel options:
    - 1. (x+1, y+1)
    - 2. (x+1, y)
  - Use the midpoint; if line is below midpoint, then mostly in bottom square, if above then mostly in top.
    - (x + 1, y + 1/2) above line -> draw lower pixel
    - (x + 1, y + 1/2) below line -> draw upper pixel
    - (x + 1, y + 1/2) ontop line -> draw either pixel

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
