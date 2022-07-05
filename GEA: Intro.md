## Intro class

---

- Show emulator debugger
- Show code structure a bit, show data, wild route
- Talk about sprites
- Talk about game loop checkIfStartIsPressed
- Each map has is own scripts.
- Show route 8 script pointers
  - D601 current script id
  - Cuando esto es 0, esta revisando constantemente si estamos frente a un entrenador 
- Al cargar la ruta, esto esta guardado en wRouteXCurScript
   esta variable es parte del save file, esto es un estado.
- Podemos ver wram.asm 
- Cuando un entrenador esta visible en la pantalla wspriteStatedata
  - C120
- Ahora vamos a hablar de glitches
  - en los scripts para la ruta8, podemos ver checkFightingMapTrainers
  - este se ejecuta un instante antes de que el valor cambie
  - checkIfStartIsPressed overworld.asm
  - runmapScript
- Ahorita estamos en ruta 8, si al instante en que el nos vea, el juego va 
  - a entrar en un loop en DisplayEnemyTrainerTextAndStartBattle, mientras espera a que el entrenador camine, esta esperando a mostrar el texto
- CD60 es player is engaged by trainer
- D730 es player walk up to player
- As CD60 is 0, sets Route 25 script id to 1
  - se llama a la funcion trainer engage
- Cuando iniciamos la batalla
  - battle core.asm > 6255
  -  pouede ver que se llama a copyData con los stats del pokemon que va a usar el oponente a esta variablesss
  -  si vemos aca (CD2D y CD2E), esta variable engaged trained class, se usa para el stat special del slowPoke
- Cuando terminamos la batalla
  - Engaged by trainer = 1
  - setea bit 0 de D730
  - TrainerWAlkUpToPlayer = 1
  - Route8MapScript = 1
  - engagedTrainerClasss = 15
- Route8MapScript sigue trabado (D601)
  - Route8MapScript = 1
  - EngagedTrainerClasss = 15
- hagamos fly a lavander town para poder caminar de regreso a la ruta8
  - al instante que entramos en la ruta 8, el menu se abrio
  - esto pasa porque el script, de la ruta 8 DisplayEnemyTrainerTextAndStartBattle
  - este metodo llama al metodo para iniciar la batalla
  - pero que batalla debe iniciar?
  - la batalla esta que esta en la variable engagedTrainerClass
  - si la variable es mayor a 200, es una batalla con un entrenador
  - en este caso, nosotros la seteamos con 15
  - y x15 es el id de mew

## Why do we build a game engine?

By game engine, I mean the core technology foundation that underlies a game
an engine abstracts game code from the specific hardware platforM
provides key functionality such as rendering, anIamtion, physics and networking

Cuales son algunos engines(?)
Unreal
Unity
Torque
CryEngine

los precios van de free a cheap to a share
the features we need are mostly supported
all our target platforms are supported
good workflows are already available
anything missing we can add
Focus on your tech 
Save money

Its fun, and you'll learn.

Re inventing the wheel is a great way to learn about wheel building
but if you want to race cars, would you use a wheel you invented?
en general, las ruedas hechas por quen hace las ruedas de todos los demas van a funcionar mejor
pero son ruedas genericas, 
quiza, solo quizas, esas ruedas hagan la diferencia 

There are many game engines, how can I contribute
how can I extend an existing engine
You need practice
game engine is a magical black box
to make better programmers

historia sobre google
  developers who dont know how to code

## C++

there is no best practice
there is what some people do and what other people do
you cannot go online and search the best way
lo que les voy a comentar es Mi manera de hacerlo, 
es la que me gusta 

yo aprendi usando makefiles

default:
    g++ src/main.cpp -o a.out

Podemos programar directamente en un makefile, pero
tenemos una herramienta que se llama CMake que hace los makefiles por nosotros

cmake puede hacer mucho de lo que queremos, solo tenemos que pasarle el folder donde esta nuestro
source y el folder donde queremos que quede el Build
no pongan el mismo folder como src y build

la mayoria de gente crea un folder build

cmake -S . -B build/

makedir math.cpp

Ejemplo de librerias

Crear nuestra propia lib

target_link_directories(SampleE PRIVATE src/Math)

include directories

ejemplo de inclusion de librerias

glfw

copy git url

https://github.com/glfw/glfw.git

[dennis@Ryza Lesson-3]$ mkdir external
[dennis@Ryza Lesson-3]$ git submodule add https://github.com/glfw/glfw.git external

add_subdirectory(external/glfw)

options

https://leimao.github.io/blog/CMake-Public-Private-Interface/

git submodule update --recursive

// references
// https://www.haroldserrano.com/blog/understanding-references-in-c

http://gamecodeschool.com/c-plus-plus/controlling-game-memory-with-c-pointers/

A reference is a name constant for an address. You need to initialise the reference during declaration. int & iRef; // Error: ‘iRef’ declared as reference but not initialised.
Once a reference is established to a variable, you cannot change the reference to reference another variable.
To get the value pointed to by a pointer, you need to use the dereferencing operator  (e.g., if pNumber is a int pointer, pNumber returns the value pointed to by pNumber. It is called dereferencing or indirection). To assign an address of a variable into a pointer, you need to use the address-of operator & (e.g., pNumber = &number).
On the other hand, referencing and dereferencing are done on the references implicitly. For example, if refNumber is a reference (alias) to another int variable, refNumber returns the value of the variable. No explicit dereferencing operator * should be used. Furthermore, to assign an address of a variable to a reference variable, no address-of operator & is needed.

// references in parameters

In pass-by-value, a clone is made and passed into the function. The caller’s copy cannot be modified.
In pass-by-reference, a pointer is passed into the function. The caller’s copy could be modified inside the function.
In pass-by-reference with reference arguments, you use the variable name as the argument.
In pass-by-reference with pointer arguments, you need to use &varName (an address) as the argument.

A quick analogy about pointers. If a variable is a house and its contents are the value it holds, then a pointer is the address of the house. Using this analogy, when we pass values to or return values from a function, we are actually making a new house the exact same as the previous one (except when we use a reference).











glfw
glm

SDL


install 
libsdl2dev

https://lazyfoo.net/tutorials/SDL/01_hello_SDL/index2.php

game loop

while game is running

we handle user input

update all objects

render changes to display

// now we will be using SDL image

add   SDL2_image to target libraries

https://lazyfoo.net/tutorials/SDL/02_getting_an_image_on_the_screen/index.php#:~:text=An%20SDL%20surface%20is%20just,it%20the%20easy%20way%20first.

An SDL surface is just an image data type that contains the pixels of an image along with all data needed to render it. SDL surfaces use software rendering which means it uses the CPU to render. It is possible to render hardware images but it's a bit more difficult so we're going to learn it the easy way first.

add to init

add to render

  SDL_RenderClear(renderer);
  SDL_RenderCopy(renderer, playerTex, NULL, NULL);
  SDL_RenderPresent(renderer);


  add rects to header


  framerate
  declare ints

  FPS = 60
  frameDelay = 1000 / FPS;

  sdl delay just sleeps
framedelay is how much we will sleep every iteration


