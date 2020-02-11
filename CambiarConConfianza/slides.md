class: middle, center
<style>
.left-column  { width: 49%; float: left; }
.right-column { width: 49%; float: right; }
.right-column ~ p { clear: both; }
.right-column ~ ul { clear: both; }
</style>
# Cambiar con Confianza 
### Lecciones Aprendidas de Helm 3
##### Taylor Thomas - Ingeniero Senior del Software en Microsoft Azure

.footnote[DevOpsDays Guadalajara 2020]

???
- Tomen en cuenta que no necesitan ningún conocimiento de Helm para sacar algún
  beneficio de este discurso. Explicaré mas en un rato

---
# ¿Quién soy yo?

- Ingeniero Senior del Software en Microsoft Azure
- Mantenedor Principal de Helm
- GitHub: [@thomastaylor312](https://github.com/thomastaylor312)
- Twitter: [@_oftaylor](https://twitter.com/_oftaylor)
- Kubernetes Slack: @oftaylor

???

- Soy un ingeniero del Software en Azure enfocado en Helm, pero también con
  otros proyectos de código abierto. También trabajaba en AKS antes.
- Entonces, ¿por qué escuchar a lo que voy a decir?
- He sido un mantenedor principal de Helm por 3 años
- Hice mucho trabajo con Helm 3, tal como programar y planificar todo el proceso
  de lanzamiento con los otros mantenedores

---
# Agenda

- ¿Qué es Helm?
- El Feo
- El Malo
- El Bueno

---
# ¿Qué es Helm?

.left-column[
- El administrador de paquetes para Kubernetes
- Mas de 1 millón de descargas por mes
- Muchas compañías grandes lo usan en producción
- Lanzamos Helm 3 en Noviembre de 2019, nuestra primera versión mayor en mas de
  dos años
]

.right-column[
.middle[<img src="./assets/helm.svg" style="padding-left: 120px;" width="300">]
]

???
- ¿Cuántos han escuchado de Helm? ¿Lo han usado?
- Todo esto significa que habríamos podido tener un gran impacto en muchas
  compañías si no hubieramos hecho buen trabajo con la nueva versión. También no
  podríamos quebrantar los paquetes que ya existían porque no queríamos que
  nuestros usuarios tuvieran que cambiar todo para acutalizar
- Y esto es todo que se requiere saber de Helm. Es un buen ejemplo de un
  proyecto que impacta a muchas personas y las lecciones que aprendimos durante
  nuestro trabajo en Helm 3. Espero que puedan aprender de nuestras
  equivocaciones y lo que hicimos bien.

---
# El Feo

.left-column[
- Funciones que no necesitamos
]

.right-column[
.middle[<img src="./assets/fire.gif" style="padding-left: 20px;" width="450">]
]

???
- Creo que el mejor ejemplo de esto fue lo que pasó con Lua. Pensamos en tener
  Lua integrado en Helm para que los usuarios avanzados pudieran tener un
  lenguaje real para hacer lo que querían. Eso llevó a mucha confusion en la
  communidad y demoras con el proyecto. 
- También pensamos en re-diseñar muchas partes del código para que fuera mas
  fácil mantener. Pero también eso nos demoró porque no pudimos continuar con la
  ayuda de la comunidad sin tener esa fundación y las interfaces programáticas.
--
.left-column[
- Falta de personas
]

???
- Tal vez este punto les parece un poquito interesante, pero fue algo muy "feo"
  que aprendimos. Al empezar, solo 2 (o tal vez 3) tabajaban en Helm 3.
  Eventualmente, llegamos a tener muchos mas. Eso fue, en parte, debido a otras
  equivocaciones nuestras, pero también había falta de personas con tiempo. Hay
  que recordar que esto no solo pasa con proyectos de código abierto, sino
  también en proyectos de empresas.
---
# Lecciones

- Tomar el tiempo al principio para averiguar lo que necesitan sus usuarios

???
- Aunque algo parece muy útil, tal vez no sea necesario. También pueden hacerlo
  mas tarde si es posible. Con Helm, aprendimos con rapidez que la "función" que
  mas queían era retirar Tiller. Como dije antes, aprendimos esto con Lua. Era
  algo muy interesante que podría haber sido muy util para los usuarios
  avanzados de Helm, pero el trabajo necessario fue demasiado grande. Si lo
  hubieramos hecho, tal vez no tendríamos Helm 3 todavía.
--
- Cuidar a su gente

???
- Solo 2 o 3 personas podian trabajar y estaban quemandose (burning out). Si
  quieren que su proyecto sea exitoso, tienen que cuidar a su gente y tener un
  numero suficiente de personas para el proyecto. No puedo decir demasiado de
  este punto. En caso de Helm, casi todos los mantenedores que trabajaban en
  Helm 3 murieron de estrés por tener que contestar todas las preguntas de la
  communidad al mismo tiempo en trabajar con el código. Con codigo abierto,
  recuerden que los mantenedores son personas también y no existen para hacer lo
  que quieren. Y, en contexto de proyectos de sus empresas, recuerden que sus
  empleados harán su mejor trabajo cuando los tratan bien.

---
# El Malo

- Trabajo con un único subproceso (single threaded)

???
- 
--
- Unclear on how the community could help

???

---
# Lecciones

---
# El Bueno

- Migración
- Compatibilidad con versiones anteriores

???
- Aunque habían cosas malas, todavía hay muchas cosas buenas!
  
---
# Lecciones

---
class: middle, center
# ¿Preguntas, dudas, o aclaraciones?

---
class: middle, center
# ¡Muchas gracias!
https://slides.oftaylor.com/CambiarConConfianza
