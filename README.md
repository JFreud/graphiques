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
3/13 | [3D Shapes](#31318-3d)
3/20 | [Vector Math](#32018-vector-math-review)
3/28 | [Backface Culling](#32818-backface-culling)
4/11 | [Relative Coordinates](#41118-relative-coordinate-system)
4/17 | [Filling in colors](#41718-scanline-conversion)
4/26 | [Lighting](#42618-lighting)
5/7  | [Compilers](#50718-compilers)
5/17 | [Animation](#51718-animation)

## 5.17.18 Animation
- Generate multiple frames with small differences between each.
- Applying transformations over time
- move by percentage each frame
```
move 400 0 0 (m0) <- knob

move 400 0 0 (0.0)
move 400 0 0 (.25)
move 400 0 0 (.50)
move 400 0 0 (.75)
move 400 0 0 (1.0)
```
- these be knobs (cuz dialing the transformation)
- we want knob to start at 0 and end at 1 + vary based on number of frames
- next variable is: vary
  - define knob behavior
  - knob: m0; start frame: 0; end frame: 4; start value: 0; end value: 1;
  - # of total frames



## 5.07.18 Compilers
- source code -> executable
#### 5 pieces
1. Lexer
2. Syntactic Analyzer
3. Semantic Analyzer
4. Optimizer*
5. Code Generator
###### * might not always be there
#### Lexer
- Performs Lexical Analysis
- Look at code and find meaningful code symbols, does not interpret code
- "Knows" the valid keywords, literal formats, and identifier formats.
- Does not perform any structural analysis 
- Outputs a list of tokens from your source code
- tools: lex, flex (free lex), javacc
- matches with language definition
- reg expressions 
```
code                   token list
int main() {          |int
  long x = 5 + 6;  -> |main
  printf("%d", x);    |(
  return 0;           |)
}                     |{
                      |long
                      |x
                      |5
                      |+
                      |6
                      |;
                      |etc... 
```
##### Types of Language tokens
- grouping symbols (;,{},(),:,\t,...)
- operators
- identifiers (variable names, function names)
- keywords
- literals
- comments (will get skipped over)

#### Parser
- forms syntax tree (groups tokens)
![syntax tree](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/Abstract_syntax_tree_for_Euclidean_algorithm.svg/400px-Abstract_syntax_tree_for_Euclidean_algorithm.svg.png)
- performs syntax analysis
- checks token list for valid structure
- know the grammar of the language
- outputs a syntax tree
- Tools: yacc (compiler compiler), bison, javacc

#### Semantic Analyzer
- Evaluate the parse tree to create a list of instructions that represent the program.
- Create a list of identifiers and associated information (symbol table)
- Type mismatches are caught as the symbol table is created.
- Outputs the operation list and symbol table
- Also using yacc/bison (for C at least)

#### Optimizer
- Not always there
- Looks through operation list and looks if it can replace pattern or series with more efficient one
- Rewrites how you did things
- Some compilers more aggressive than others

#### Code Generator
- Translate the operation list and the symbol table to translate into the binary assembly code.
- Creates the actual machine instructions
- Calls the image creation/manipulation functions


| Name  | Input       | Output     |
|-------|-------------|------------|
| lexer | source code | token list |
| parser | token list | syntax tree |
| Semantic Analyzer | Syntax Tree | Operation List and Symbol Table |
| Optimizer | Operation List and Symbol Table | Optimized Operation List and symbol table |
| Code Generator | Optimized Operation list and Symbol table | Binary Executable |

Image Code -> Token List -> Syntax Tree -> Operation List + Symbol Table -> image(s)


## 4.26.18 Lighting
- colors are reflected--must be simulated
- The color of an object is based on:
  1. The refelective properties of the object
  2. The color, intensity, and location of any light sources
- Types of light sources:
  - Ambient
    - General light in an image
    - Comes from all locations equally
  - Point
    - Comes from a specific location (for our purposes really far away so that if we move it'll have same relation e.g. sun)
### Phong Reflection Model
- Models real world reflection by breaking reflection into 3 parts: Ambient, Diffuse, Specular
- I = Ambient + Diffuse + Specular
- Diffuse and Specular are point sources
- Ambient Reflection
  - A : Ambient light (0 - 255)
  - K<sub>a</sub> : Constant of ambient reflection (0 - 1)
  - Ambient = A * K<sub>a</sub> (do this 3 times for rgb)
  - ![Diagram of Reflections](https://upload.wikimedia.org/wikipedia/commons/b/bd/Lambert2.gif)
- Diffuse Reflection
  - Reflection of a point source
  - Light is reflected back evenly in all directions
  - matte/dull objects (not shiny)
  - L: vector from surface to light (θ between N (normal) and L)
  - cosθ = N • L (percentage of reflection that we get from the relationship between the surface and the light)
  - P: point light color
  - K<sub>d</sub>: Constant of diffuse reflection (0-1)
  - Diffuse = PK<sub>d</sub>(N • L)
- Specular Reflection
  - Reflects a point source in a specific direction
  - Models glossy/shiny surfaces
  - Strength of specular reflection is based on:
    - The angle between R and V (∝) <- the one we care about
    - The angle between L and N (θ)
  - We want R • V
  - Let T be projection of L onto N
  - R = T • S (leg of triange formed between y-axis and R)
  - S = T - L
  - R = T + T - L = 2T - L
  - I = AK<sub>a</sub> + PK<sub>d</sub>(N • L) + S
  - cos∝: R • V
  - cosθ = L • N
  - cosθ = ||T|| / ||L|| = ||T||
  - T = ||T||(N)
  - T = (L • N)N
  - R = 2(N • L)N - L
  - cos∝ = [2(N • L)N - L] • V (costly to compute)
  - Specular = PK<sub>s</sub>([2(N • L)N - L] • V)<sup>x</sup>
  - x: arbitrary exponent used to simulate how quickly the reflection decreases
  	- no set value for x, just try it (e.g. 4)
  - I = AK<sub>a</sub> + PK<sub>d</sub>(N • L) + PK<sub>s</sub>([2(N • L)N - L] • V)<sup>x</sup>
    
    

## 4.17.18 Scanline Conversion
- Filling in a polygon by drawing consecutive horizontal (or vertical) lines
- Need to find the top, bottom, and middle vertices.
- Need to find the endpoints of each scanline.
  - y starts at: y<sub>B</sub>
  - y ends at: y<sub>T</sub>
  - y += 1
  - x will always be on edge of triangle
  - x<sub>0</sub> starts at: x<sub>B</sub>
  - x<sub>0</sub> ends at: x<sub>T</sub>
  - x0 += Δ0
    - Δ0 = (x<sub>T</sub> - x<sub>B</sub>) / (y<sub>T</sub> - y<sub>B</sub>)
  - x<sub>1</sub> starts at: x<sub>B</sub>
  - x<sub>1</sub> ends at: x<sub>T</sub>
  - x<sub>1</sub> changes at M (from edge BM to MT)
    - x<sub>1</sub> on BM until y = y<sub>M</sub>, then on MT
  - x<sub>1</sub> += Δ1
    - Δ1 = (x<sub>M</sub> - x<sub>B</sub>) / (y<sub>M</sub> - y<sub>B</sub>)
    - or Δ1 = (x<sub>T</sub> - x<sub>M</sub>) / (y<sub>T</sub> - y<sub>M</sub>)
- what if M is on the same horizontal as T or B?
  - not a special case because x<sub>1</sub> flip condition is based off of y value
- no special cases for scanline conversion
- make sure not dividing by zero ( check x = x<sub>M</sub> )
- will not fill in triangles in front or behind because we're not drawing by z-value but by order of shapes listed
## 4.18.18 Z-Buffering
- not polygon by polygon determination, it's pixel by pixel
- Create a 2-D array of floating point values, where each entry directly corresponds to a pixel on the screen
- We store the z-values of every plotted pixel in this array
- We check new z-values against the z-buffer before plotting
- Initialize z-buffer with most negative value possible (theoretically negative infinity but use lowest possible long value in C)
- draw_line needs to generate z values
- scanline conversion needs to generate z values
- plot needs to look at/modify z buffer

## 4.11.18 Relative Coordinate System
- Currently, we generate triangles/edges first and then apply transformations to those matrices.
  - but tis makes drawing hard
- In an RCS, transformations are applied to the "current", coordinate system, which modifies shapes as soon as they are generated.
  - E.g. rotate the world, then move it, then draw the sphere 
```
|1 0 0 0|     |2 0 0 3|
|0 1 0 0| ___ |0 2 0 1|
|0 0 1 0| ___ |0 0 2 0|
|0 0 0 1|     |0 0 0 1|
    A             B
```
### Drawing in an RCS
1. Generate polygons/edges
2. Modify those points by the current coordinate system
3. Draw to the image
- gain fully independent control of the shapes in our scene
- but if you draw smth like a robot, you don't want independent control. You want to modify them all together.
- Make a stack of coordinate systems
- Like Processing push and pop matrix
  
  
## 3.28.18 Backface Culling
- Only drawing the polygons that are forward-facing.
- N: surface normal ⟂ to the surface
- ![Surface Normal Image](https://upload.wikimedia.org/wikipedia/commons/thumb/5/53/Surface_normal_illustration.svg/220px-Surface_normal_illustration.svg.png)
- V: view vector (vector from the surface to the "viewer/eye/camera"
- ![Second Surface Normal Image](http://archive.gamedev.net/archive/reference/articles/codex/culling.gif)
- Can't see anything greater than 90° away from you.
1. Calculate N
  - Cross product P0 to P1 (vector A) and P0 to P2 (vector B)
  - N = A x B
  - A = <P<sub>1</sub> - P<sub>0</sub>>
  - B = <P<sub>2</sub> - P<sub>0</sub>>
2. Find θ
  - N • V = ||N|| ||V|| cosθ
  - cosθ > 0 if -90° < θ < 90°
  - magnitudes always positive
  - V = <0, 0, 1>
  - N = <x, y, z>
  - All you need is sine of z component
    - z tells us what direction in z the polygon is facing, we don't care about x and y
3. If -90° < θ < 90° or dot product > 0, draw polygon.


## 3.20.18 Vector Math Review
- Vectors have magnitude and direction
  - (x, y, z) vs <x, y, z>
- Magnitude ||A|| = √(Ax<sup>2</sup> + Ay<sup>2</sup> + Az<sup>2</sup>)
- Normalized vector is a unit vector 
  - A<sub>hat</sub> = 1/||A|| <A<sub>x</sub>, A<sub>y</sub>, A<sub>z</sub>>
- Dot Product
  - ![Dot Product Image](https://betterexplained.com/wp-content/uploads/dotproduct/dot_product_components.png)
  - Gives scalar number back, not vector
  - Multiply one magnitude by another. Influence of each magnitude on resulting product depends on angle between vectors.
  - since cosine is inverse with angle size:
    - A • B = ||B|| ||A|| cosθ
    - A • B = A<sub>x</sub>B<sub>x</sub> + A<sub>y</sub>B<sub>y</sub> + A<sub>z</sub>B<sub>z</sub>
  - You can get cosine by taking the dot product
- Cross Product
  - Results in vector ⟂ to both A and B
  - ![Cross Product Image](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/Cross_product_parallelogram.svg/220px-Cross_product_parallelogram.svg.png)
  - Area of triangle is ||A|| ||B|| sinθ
  - There are two N vectors ⟂
  - Vector Math
Cross Product
A x B

Wireframe Mesh
shape is defined by connecting points on the surface with edges
defines 3d object using lines
generate_sphere -> add_sphere -> add_edge -> add_point
instead of connecting point to self, connect points to other points | draw_lines -> draw_line

need surface


Polygon Mesh
shape is defined by connecting points on surface with polygons (planes) instead of edges (triangles/quadrilaterals)
generate_sphere - add_sphere -> add_polygons/triangles -> add_point | draw_polygons -> draw_line

Edge Matrix
Polygon Matrix
instead of storing lines 2 points at a time, store triangles three points at a time
[P0,P1,P2,   P2,P3,P4] <- two triangles that share vertices at P2 ‘

## 3.13.18 3D
- Box
  - Defining points: vertices
  - Given:
    - P0 (top left front)
    - Height - y
    - Width - x
    - Depth - z
  - call addEdge twelve times
- Sphere
  - Defining points: points along a surface
  - Given:
    - Center
    - Radius
  - There are a couple of ways to draw sphere. Our options are:
    1. Draw a circle and rotate 180 degrees
    2. Draw a semi-circle and rotate it 360 degrees (this is the better option).
  - call addCircle and rotate it
    - If you rotate about the z-axis, there is no 3D shape. Rotate about the x or y-axis.  
  - rotating around x-axis and y-axis will give sphere, rotating around z-axis will not
  - x = rcosθ 
  - y = rsinθcosϕ
  - z = rsinθsinϕ
  x-rotation: (theta is angle of circle creation, phi is angle of circle rotation)
```
| 1  0      0  |   | rcosθ |   | x |   |rcosθ + cx    |
| 0 cosϕ -sinϕ | · | rsinθ | = | y | = |rsinθcosϕ + cy|
| 0 sinϕ  cosϕ |   |   0   |   | z |   |rsinθsinϕ + cz|
```
  - theta 0 -> 2pi, phi 0 -> pi (rotating circle)
  - theta 0 -> pi, phi 0 -> 2pi (rotating semicircle)
  - Pseudo-code
```
for (ϕ: 0 -> 2π) {
    for (θ: 0 -> π) { // Semicircle
        x = rcosθ + cx
	      y = rsinθcosϕ + cy
	      z = rsinθsinϕ + cz
    }
}
```
- Torus
  - **Defining points:** Points on surface
  - **Given:** r (radius of cross section), R (radius of torus edge to center), cx, cy, cz
  - Generate the image by translating a circle and rotating it.  
  - If we move over x, rotate about y.  
  - If we move over y, rotate about x. 
```
Circle (+ R/translation)
[  rcosθ (+ R)  ]
[  rsinθ (+ R)  ]
[       θ       ]

    y-rotation          circle               torus
|  cosϕ  0  sinϕ |  | rcosθ + R |   |  cosϕ(rcosθ + R) + cx |   | x |
|   0    1    0  |  |   rsinθ   | = |      rsinθ + cy       | = | y |
| -sinϕ  0  cosϕ |  |     0     |   | -sinϕ(rcosθ + R) + cz |   | z |
```
  - θ = 0 -> 2π  
  - ϕ = 0 -> 2π

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
