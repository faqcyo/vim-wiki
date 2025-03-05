Tuesday, July 30, 2024

# Graphics Engine

Today i will finish what was pending from yesterday:
  - [X] Test textures
  - [X] Correct activation of textures
  - [X] Fix Element buffer

New tasks:
  - [ ] Load models from .obj files
  - [ ] Create a Camera class
    - [ ] Listen to key events
    - [ ] Listen to mouse events
    - [ ] Zoom in and out
    - [ ] Configuring whether to use perspective or orthogonal view

For the future:
  - [ ] Make an event dispatcher. This will consist of a queue of functions to be executed whenever a certain event occurs.


# Notes on problems

* The VAO wasn't rendering anything with an EBO because the data passed to the EBO was incorrect. I had:

```
glBufferData(GL_ELEMENT_ARRAY_BUFFER, m_indices.size(), m_indices.data(), GL_STATIC_DRAW);
```
when it should have been:

```
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(unsigned int) * m_indices.size(), m_indices.data(), GL_STATIC_DRAW);
```

* The texture was being displayed as a solid color. This was because i didn't specify the texCoords in the vertex parameters (not to be confused with the vertex attribute pointers, this was configured correctly).

* For the camera movement:
  - I want it to move whenever i press a key with the keyboard (for now it will be WASD, but maybe in the future let the user select which keys to use). For this i should listen to a keyboard event and if its either WASD, then change the view matrix accordingly. 
