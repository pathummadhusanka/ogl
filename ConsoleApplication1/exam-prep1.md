# Computer Graphics OpenGL Lab 1-10 Exam Preparation Notes

This is focused on **practical exam style** where you are given existing code and asked to **modify/add features** like the activities in lab sheets.

The main strategy:

1. Understand the OpenGL pipeline.
2. Know where to add code.
3. Memorize common snippets:

   * changing vertices
   * changing colors
   * adding objects
   * transformations
   * 3D matrices
   * animation

---

# 1. OpenGL Basic Pipeline (Most Important)

Everything follows this flow:

```
Vertex Data
     |
     ↓
VBO (Vertex Buffer Object)
     |
     ↓
VAO (Vertex Array Object)
     |
     ↓
Vertex Shader
     |
     ↓
Fragment Shader
     |
     ↓
Draw Call
     |
     ↓
Screen
```

Remember:

* VBO stores vertex data.
* VAO remembers how vertex data is configured.
* Vertex shader controls position.
* Fragment shader controls color.
* glDrawArrays / glDrawElements actually draws.

---

# Lab 1 - Creating First Window

## Important libraries

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```

### GLFW

Responsible for:

* creating window
* keyboard input
* mouse input
* OpenGL context

### GLAD

Responsible for:

* loading OpenGL functions

---

## Initialization order

Always:

```cpp
glfwInit();

Create window

glfwMakeContextCurrent(window);

gladLoadGLLoader();
```

Order matters.

---

## Render loop

Every OpenGL program has:

```cpp
while(!glfwWindowShouldClose(window))
{

    glClear(...);


    // draw objects


    glfwSwapBuffers(window);

    glfwPollEvents();
}
```

Meaning:

```
Clear screen
     ↓
Draw
     ↓
Show frame
     ↓
Check input
```

---

# Lab 2 - Input and Background Color

## Clear color

```cpp
glClearColor(
    red,
    green,
    blue,
    alpha
);
```

Range:

```
0.0 - 1.0
```

Examples:

Red:

```cpp
glClearColor(1,0,0,1);
```

Green:

```cpp
glClearColor(0,1,0,1);
```

Blue:

```cpp
glClearColor(0,0,1,1);
```

Remember:

```cpp
glClearColor()
```

only sets the color.

Actual clearing:

```cpp
glClear(GL_COLOR_BUFFER_BIT);
```

---

# RGB Color Rules

Range:

```
0.0 - 1.0
```

Examples:

White:

```
R=1
G=1
B=1
```

Black:

```
R=0
G=0
B=0
```

Yellow:

```
1,1,0
```

Purple:

```
1,0,1
```

Cyan:

```
0,1,1
```

---

# Lab 3 - First Object

## Vertex format

A position:

```cpp
x,y,z
```

Example:

```cpp
float vertices[] =
{
    0.0f,0.5f,0.0f
};
```

means:

```
x = 0
y = 0.5
z = 0
```

---

# Coordinate System

OpenGL uses:

```
       +Y

        |
        |
-X -----+----- +X

        |
        |
       -Y
```

Range:

```
-1 to +1
```

called:

## Normalized Device Coordinates (NDC)

Anything outside:

```
<-1 or >1
```

can disappear.

---

# Drawing a Point

Vertex:

```cpp
0.0f,0.0f,0.0f
```

Draw:

```cpp
glDrawArrays(
    GL_POINTS,
    0,
    1
);
```

---

# Drawing Line

Two vertices:

```cpp
v1
v2
```

Draw:

```cpp
glDrawArrays(
    GL_LINES,
    0,
    2
);
```

---

# Drawing Triangle

Three vertices:

```cpp
glDrawArrays(
    GL_TRIANGLES,
    0,
    3
);
```

---

# Lab 4-5 Rectangle

OpenGL does not have rectangle.

Rectangle = 2 triangles.

Example:

```
Triangle 1

0 ---- 3
|    /
|  /
1


Triangle 2

3
|
|
1 ---- 2
```

---

# Using EBO

Instead of repeating vertices:

Vertices:

```
4 points
```

Indices:

```cpp
unsigned int indices[]
{
    0,1,3,
    1,2,3
};
```

Draw:

```cpp
glDrawElements(
    GL_TRIANGLES,
    6,
    GL_UNSIGNED_INT,
    0
);
```

---

# Lab 6 - Colors

Before:

```
position only
```

Example:

```cpp
x,y,z
```

After:

```
position + color
```

Format:

```cpp
x,y,z,
r,g,b
```

Example:

```cpp
float vertices[]
{
0.0f,0.5f,0.0f,
1.0f,0.0f,0.0f
};
```

Meaning:

```
position:
0,0.5,0

color:
red
```

---

## Vertex Attribute

Position:

```cpp
location = 0
```

Color:

```cpp
location = 1
```

---

Position:

```cpp
glVertexAttribPointer(
0,
3,
GL_FLOAT,
GL_FALSE,
6*sizeof(float),
(void*)0
);
```

Color:

```cpp
glVertexAttribPointer(
1,
3,
GL_FLOAT,
GL_FALSE,
6*sizeof(float),
(void*)(3*sizeof(float))
);
```

---

# Lab 7-8 Transformations

Using GLM.

Include:

```cpp
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>
```

---

# Matrix

A matrix stores transformations.

Create identity:

```cpp
glm::mat4 transform =
glm::mat4(1.0f);
```

Means:

"No transformation yet"

---

# Translation (Move)

Syntax:

```cpp
glm::translate(
matrix,
vector
);
```

Example:

```cpp
transform =
glm::translate(
transform,
glm::vec3(0.5f,0,0)
);
```

Move right:

```
+X
```

Move left:

```
-X
```

Move up:

```
+Y
```

Move down:

```
-Y
```

---

# Rotation

Syntax:

```cpp
glm::rotate(
matrix,
angle,
axis
);
```

Example:

```cpp
glm::rotate(
transform,
glm::radians(45.0f),
glm::vec3(0,0,1)
);
```

45 degree rotation around Z.

---

Axis:

X axis:

```cpp
(1,0,0)
```

Y axis:

```cpp
(0,1,0)
```

Z axis:

```cpp
(0,0,1)
```

---

# Scaling

Example:

```cpp
glm::scale(
transform,
glm::vec3(2,2,2)
);
```

Double size.

Half:

```cpp
glm::vec3(0.5,0.5,0.5)
```

---

# Combining Transformations

Example:

```cpp
transform =
glm::translate(...);


transform =
glm::rotate(...);


transform =
glm::scale(...);
```

Order matters.

---

# Lab 8 - Multiple Objects

Important concept:

You can draw many objects:

```
Roof
 |
Body
 |
Door
 |
Window
```

Each object:

```
Vertices
 ↓
VBO
 ↓
VAO
 ↓
Draw
```

You can:

* duplicate vertex arrays
* change coordinates
* reuse drawing code

Example:

```cpp
drawTriangle(roof);

drawRectangle(body);
```

---

# Lab 9 - 3D Graphics

Main changes:

Before:

```
x,y
```

Now:

```
x,y,z
```

---

# Cube

Cube:

```
8 vertices
6 faces
12 triangles
36 vertices when using GL_TRIANGLES
```

Why 36?

```
6 faces

each face:
2 triangles

each triangle:
3 vertices


6*2*3 = 36
```

---

# Depth Testing

Enable:

After GLAD:

```cpp
glEnable(GL_DEPTH_TEST);
```

Clear:

```cpp
glClear(
GL_COLOR_BUFFER_BIT |
GL_DEPTH_BUFFER_BIT
);
```

---

# Model View Projection

Very important.

## Model Matrix

Controls object.

Examples:

* move
* rotate
* scale

---

## View Matrix

Controls camera.

Example:

```cpp
view =
glm::translate(
view,
glm::vec3(0,0,-3)
);
```

Camera moves backward.

---

## Projection Matrix

Creates perspective.

```cpp
glm::perspective(
glm::radians(45.0f),
800.0f/600.0f,
0.1f,
100.0f
);
```

Controls:

* field of view
* near/far distance

---

Vertex shader:

Must have:

```glsl
uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;
```

Final:

```glsl
gl_Position =
projection *
view *
model *
vec4(aPos,1.0);
```

---

# Lab 10 - Animation

Animation means:

Changing something over time.

---

Get time:

```cpp
glfwGetTime()
```

Returns seconds.

---

Rotation animation:

Inside render loop:

```cpp
glm::mat4 model =
glm::mat4(1.0f);


model =
glm::rotate(
model,
(float)glfwGetTime(),
glm::vec3(0,1,0)
);
```

---

Speed:

Normal:

```cpp
glfwGetTime()
```

Faster:

```cpp
glfwGetTime()*2
```

Slower:

```cpp
glfwGetTime()*0.5
```

---

# Common Exam Modifications

## 1. Change object position

Find:

```cpp
glm::translate()
```

Modify:

```cpp
glm::vec3(x,y,z)
```

---

## 2. Rotate object

Add:

```cpp
glm::rotate()
```

---

## 3. Increase size

Add:

```cpp
glm::scale()
```

---

## 4. Change color

Fragment shader:

```glsl
FragColor =
vec4(r,g,b,1.0);
```

---

## 5. Add another object

Copy:

```
vertices
VBO
VAO
draw call
```

or reuse same VAO with different model matrix.

---

# Most Important Things To Memorize

### OpenGL order

```
Init GLFW
↓
Create Window
↓
GLAD
↓
Shader
↓
VAO/VBO
↓
Render Loop
```

### Drawing commands

| Object    | Command      |
| --------- | ------------ |
| Point     | GL_POINTS    |
| Line      | GL_LINES     |
| Triangle  | GL_TRIANGLES |
| Rectangle | 2 triangles  |
| Cube      | 12 triangles |

### Coordinate ranges

2D:

```
x,y = -1 to +1
```

3D:

```
x,y,z
```

### Transform functions

Move:

```cpp
glm::translate()
```

Rotate:

```cpp
glm::rotate()
```

Scale:

```cpp
glm::scale()
```

### Matrices

Object:

```
Model
```

Camera:

```
View
```

Perspective:

```
Projection
```

### Animation

Always:

```
time
↓
change matrix
↓
send shader
↓
draw
```

For the practical exam, most questions will be variations of:

* "Move this object"
* "Rotate this object"
* "Scale this object"
* "Change color"
* "Add another object"
* "Make it animate"
* "Create 3D version"

If you understand where these snippets go, you can solve almost every activity from Lab 1-10.
