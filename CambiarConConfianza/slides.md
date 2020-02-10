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
- Entonces, por qué escuchar a lo que voy a decir?
- He sido un mantenedor principal de Helm por 3 años
- Hice mucho trabajo con Helm 3, tal como programar y planificar todo el proceso
  de lanzamiento

---
# Agenda

- ¿Qué es Helm?
- El Feo
- El Malo
- El Bueno

---
# ¿Qué es Helm?

- El administrador de paquetes para Kubernetes
- Mas de 1 millón de descargas por mes
- Muchas compañías grandes lo usan en producción
- Lanzamos Helm 3 en Noviembre de 2019, nuestra primera versión mayor en mas de
  dos años

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
- Lua
--
.left-column[
- Falta de personas
]

???
---
# Lecciones

- Tomar el tiempo al principio para averiguar lo que necesitan sus usuarios

???
- Aunque algo parece muy útil, tal vez no sea necesario. También pueden hacerlo
  mas tarde si es posible. Con Helm, aprendimos con rapidez que la "función" que
  mas queían era retirar Tiller.
--
- Cuidar a su gente

???
- Solo 1 o 2 personas podian trabajar y estaban quemandose (burning out)

---
# El Malo

- Trabajo con un único subproceso (single threaded)

???
- 
--
- Unclear on how the community could help

???
--
- Demasiado tiempo para completar trabajo

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
