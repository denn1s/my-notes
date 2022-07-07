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

## Por qué no debemos hacer un engine?
- Los precios van de free a cheap to a share
- The features we need are mostly supported
- All our target platforms are supported
- Good workflows are already available
- Anything missing we can add
- Focus on your tech 
- Save money
- You CANT make a better engine than Unity or Unreal (or godot or game maker)

## Por qué debemos hacer un engine?

Its fun, and you'll learn.

Re inventing the wheel is a great way to learn about wheel building
but if you want to race cars, would you use a wheel you invented?
en general, las ruedas hechas por quen hace las ruedas de todos los demas van a funcionar mejor
pero son ruedas genericas, 
quiza, solo quizas, esas ruedas hagan la diferencia 

- Novel tech. Qué pasa si quieren hacer un juego que sea algo que nadie nunca ha visto antes?
   - Una simulación masiva que necesita un performance nunca antes visto (Factorio)
   - Una idea tan única que no quepa en un molde existente (Noita, Miegakure)
   - Que pasa si quieren interactuar con hardware único? (play.date)
- Specialization
   - No necesitan todo lo que les da unity. Pueden hacer un workflow mucho más optimo si lo hacen ustedes. 
   - En general no pueden hacer un engine mejor que unity, pero para un caso en particular, pueden. 
- Independence
   - Realmente quieren depender de Unity y Unreal para siempre?
- There are many game engines, how can I contribute
- How can I extend an existing engine
- You need practice
- Game engine is a magical black box
- Game developer jobs
- Porting jobs
- To make better programmers

historia sobre google
  developers who dont know how to code

## Qué es lo que vamos a hacer entonces

Un engine es el framework sobre el cual hacemos un juego. Nos da la fundación y las piezas, los legos, sobre los que vamos a armar un juego. Permite que nuestro juego no tenga que preocuparse sobre como se debe mandar un triangulo a la tarjeta de video. 

Conexión con gráficas por computadora. 

Hay una gran variedad de cosas que puede hacer un engine. Hay unos que solo son basicamente engines de graficas, como Flash o Pico-8,  mientras que hay otros que casi hacen el juego entero por ustedes, como RPGMaker o Renpy.

Los engines se construyen sobre frameworks de bajo nivel, como OpenGl o SDL. Incluyen librerias para audio para física, para mate, para inteligencia artificial, y para cualquier cosa. No vamos  a hacer todo desde cero absolutamente, no cuando existen cosas tan útiles como SDL o GLM. Básicamente existen librerías allá afuera para cada uno de los sistemas.

## Que sí debemos hacer entonces?
- La parte más importante de un engine es la arquitectura. Queremos una arquitectura unificada. Una sola unidad de sistemas. 
- Orquestación de los sistemas. SDL hace casi todo eso. Pero podemos utilizar tambien algo como GLFW o SFML. 
- Frame Timing Control.
- Inputs
- Rendering
- Utilities
  - https://github.com/nothings/stb
- Game Objects
- Scene management
  -  Are your objects big inheritance trees? Are they made up of smaller components? Are you using ECS? (sidenote: you probably shouldn’t use “pure” ECS unless you have a really specific use case for it) What types of events do they respond to? How do you query for other objects to interact with? Do you care about memory layout?
- Audio
  - Esto es un poco más dificil solo porque las librerías principales de la industria, que son FMod y Wwise son comerciales. Y las opensourse son horribles. OpenAl y Faudio.
- Manejo de archivos
  - Loading (Genshin)
  - No queremos redundancia. 
  - Dynamic Loading and Unloading
  - Resource management
- Networking (??)
- Collision + Physics
  - Box2D o Bullet
- Serialization
- Animation and UI

En general, su engine va a ser simple y tener menos sistemas de lo que tiene Unity y Unreal. Este es el punto. Unity y Unreal son monolitos gordos, y cada juego solo usa una fracción de lo que pueden hacer. 

## Cómo vamos a lograr esto?

Necesitamos hacer juegos de la mano con el engine. Esta es la única regla de construir un engine. Hay que hacer un juego que valla de la mano con el. Un engine sin un juego no es nada. Si estamos reintentando la rueda pero no la estamos rodando, así es como paramos con ruedas cuadradas.

Las features del engine vienen de las necesidades de los juegos que corren en ellos. No podemos hacer un sistema de animaciones si no tenemos un juego que requiera animarse. No van a poder saber si su engine tiene buena performance sin un juego que cora en el. Por ejemplo, ¿Será que necesitamos crear un sistema que detecte cuando un objeto está muy lejos de la camara para poderelo desmontar de la memoria? Saber, nadie puede saber hasta que tengamos un juego que no corra. 

La metodología que vamos a usar en este curso es una metodología nueva, basada en ejemplos y evidencias de aprendizaje. En la clase, de la mano con cada tema del curso, vamos a ver ejemplos, juegos, que utilizan cada tema. Y ustedes deben implementar su engine y un juego que demuestre este tema. Pueden hacer un solo juego para todo el curso. Eso está bien. O pueden hacer varios. Pueden empezar a hacer un juego grande y cuando comienzen a sentirse abrumados pueden pasarse a hacer varios pequeños. Yo creo que en los ejemplos me vohy a tratar de enfocar en hacer un solo juego. Mi plan es hacer un RPG top down, así que no van a poder solo copiar, pues deben adaptar y expander lo que veamos en clase para su juego en particular.  

Tambien vamos a explorar el diseño y desarrollo de juegos desde una perspectiva distinta. Alhvi les ha enseñado un proceso verdad? Mi proceso es bien distinto, como ya saben, mi background es de narrativa y experiencia del usuario. 

Para lograr los objetivos del curso, necesitamos progamar a bajo nivel. Los ejemplos y el baseline en el que trabajen es C++. Las proximas clases van a estar enfocadas principalemnte en construir la base de un ambiente de desarrollo en C++. Pueden usar otros lenguajes, pero deben ser de bajo nivel. Nuevamente quisiera que nos alejemos de Unity y Unreal. Tambien quiero que pueden extender Unity y Unreal, y los engines open source. 

