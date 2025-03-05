[Back to notes index](index)
[Back to index](/index)

# OpenGL Notes

Reference: https://learnopengl.com/

OpenGL is a large state machine. The state of OpenGL is known as the **OpenGL context**
When using OpenGL we usually change its state by setting some options, and then rendering based on the current context.
State-changing functions and state-using functions.

An **object** in OpenGL is a collection of options that represents a subset of OpenGL's state.

The process of transforming 3D coordinates to 2D pixels is managed by the **graphics pipeline** of OpenGL. The graphics pipeline can be divided into two large parts:
  1. The first transforms your 3D coordinates into 2D coordinates,
  2. The second transforms the 2D coordinates into actual colored pixels.

The graphics pipeline can be divided into several steps, where each step receives as input the output of a previous step.

The processing cores of a GPU run small programs on the GPU for each step of the pipeline. This programs are called **shaders**. Shaders are written in **OpenGL Shading Language (GLSL)**.

The following are the steps that the graphics pipeline follows:
  1. Vertex Shader (We pass as input the vertex data)
  2. Geometry Shader
  3. Shape assembly
  4. Rasterization
  5. Fragment Shader
  6. Test and Blending
The vertex data we pass as input to the first step of the graphics pipeline is a list of 3D coordinates (collection of vertices). 
Then a vertex is a collection of data per 3D coordinate, represented using vertex attributes, that can have any data that we'd like, but for now, let's just consider the 3D coordinate system and some color value as the only data that we have in the vertex attributes.

The following are primitives in OpenGL: GL_POINTS, GL_TRIANGLES and GL_LINE_STRIP.

## Vertex Shader

Its the first step of the graphics pipeline. Its main purpose is to transform 3D coordinates into different 3D coordinates (more on that later), and it allows us to do some processing on the vertex attributes.

## Geometry Shader
 
The output of the vertex shader is optionally passed to the geometry shader. The geometry shader takes as input a collection of vertices that form a primitive and has the ability to generate other shapes by emitting new vertices to form new (or other) primitive(s).

## Shape assembly

The shape assembly (also called primitive assembly) stage takes as input all the vertices (or vertex if GL_POINTS is chosen) from the vertex (or geometry) shader that form one or more primitives and assembles all the points in the primitive shape given.

## Rasterization

Takes as input the output of the shape assembly. The rasterization stage maps the resulting primitives to the corresponding pixels on the final screen, resulting in fragments for the fragment shader to use. 

## Fragment Shader

Before the fragment shaders run, **clipping** is performed. Clipping discards all fragments that are outside your view, increasing performance. The main purpose of the fragment shader is to calculate the final color of a pixel and this is usually the stage where all the advanced OpenGL effects occur. Usually the fragment shader contains information about the 3D scene that it can then use to calculate the final pixel color (like lights, shadows, color of the light and so on).

A fragment in OpenGL is all the data required for OpenGL to render a single pixel.

## Test and Blending

After all the corresponding values have been determined, the final object will then pass through one more stage that we call the **alpha test and blending stage**. This stage checks the corresponding depth (or stencil) value and check if it is either in front or behind an object, and discard it accordingly. The stage also checks the alpha value (The transparency of an object) and blends the objects accordingly.


All of this stages complete the graphics pipeline of OpenGL. However, most of the time, we will use only the vertex shader and fragment shader only.

OpenGL only processes 3D coordinates when they're in a specific range between -1.0 and 1.0 on all 3 axes (x, y and z)

The **vertex buffer objects (VBO)** can store a large number of vertices in the GPU's memory. The advantage of using these VBOs is that we can send a batch of data all at once to the GPU, and keep it there if there is enough memory left, without having to send data one at a time. 
The VBO is an OpenGL object.

The following are options for telling the GPU how to manage the given data:
  * GL_STREAM_DRAW: the data is set only once and used by the GPU at most a few times.
  * Gl_ STATIC_DRAW: the data is set only once and used many times.
  * GL_DYNAMIC_DRAW: the data is changed a lot and used many times.

# Vertex Array Object

A vertex array object (VAO) can be bound just like a VBO and any subsequent vertex attribute calls from that point on will be stored inside the VAO.
The advantage of the VAO is that we only have to configure the vertex attribute pointers once and whenever we want to draw the object, we can just bind the corresponding VAO.

A Vertex Array Object contains the following:
  * Calls to glEnableVertexAttribArray or glDisableVertexAttribArray
  * Vertex attribute configurations via glVertexAttribPointer
  * Vertex buffer objects associated with vertex attributes by calls to glVertexAttribPointer
Then, a VAO can have multiple vertex attribute pointers that interpret the VAO differently.
Usually when we have multiple objects we want to draw, we first generate/configure all the VAOs (and thus the required VBOs and vertex attribute pointers) and store those for later use. The moment we want to draw one of our objects, we take the corresponding VAO, bind it, then draw the object and unbind the VAO again.

# Element Buffer Objects

An EBO is a vertex buffer object, that stores indices that OpenGL uses to decide what vertices to draw (indexed drawing).
