# imgui-demo (SDL2 & OpenGL3)

A way to show the simplicity and minimalism of building a crossplatform gui app using Dear ImGui

## notes

9/18/2024

The plan is to compile using SDL2 and OpenGL3 statically.

### including Dear ImGui

First setup cmake to combine all the source files from imgui and use the imgui example
that will utilize SDL2 and OpenGL3 for our testing purposes

include imgui into the libs folder since its external code we have not written and not
necessarily a library since this is part of the source for our program.

```git submodule add https://github.com/ocornut/imgui.git```

then append all the sources to our executable thats needed and include the headers.

### Including SDL2

include SDL2 as a submodule as well since were going to statically link it into the
executable.

note: we want SDL2 not SDL3 so we have to specify branch when adding the repo

```git submodule add -b SDL2 https://github.com/libsdl-org/SDL.git```

now we have to add this to our cmake file as a static library. we can include this
into our project by using `add_subdirectory()` to include this cmake project into our
own cmake project then set then check the library for what targets it builds in SDL2
cmake files so we know what options to turn on/off.

```target_include_directories()``` will point to SDL include folder

finding the targets to link with by using ctrl-f in SDL's CMakeLists.txt for add_library().
We found `add_library(SDL2main STATIC ${SDLMAIN_SOURCES})` so we will link with that. I know
theres another library to link with after this one because i've read the docs on how to link it.

we found SDL_SHARED so we know we need to turn that off for sure

```cmake
if(SDL_SHARED)
  add_library(SDL2 SHARED ${SOURCE_FILES} ${VERSION_SOURCES})
```

then we found this which is the other library we need to link SDL2

```cmake
if(SDL_STATIC)
  add_library(SDL2-static STATIC ${SOURCE_FILES})
```

now that we found the libraries we link them with ```target_link_libraries()```

### Including OpenGL3

So this one embarssingly took to long for me to find out but windows ships opengl.lib on every
windows device we "care" about and linux users are expected to have it from the distribution
or install it themselves. So we will use `find_package()`to get the info for it and link it
like a normal library.

### attempting first build 

I was getting unreferenced symbol for calling main and it turns out the example does not work
right out of the box. I've referenced this 

https://stackoverflow.com/questions/6847360/error-lnk2019-unresolved-external-symbol-main-referenced-in-function-tmainc

which shows you need to undefine main from SDL as the chosen answer. However reading deeper in the documentation it says
it needs to be included like this

```c
#define SDL_MAIN_HANDLED
#include "SDL.h"
```

then references the documentation here

https://wiki.libsdl.org/SDL2/SDL_SetMainReady