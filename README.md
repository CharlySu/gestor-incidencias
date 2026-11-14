# Gestor de Incidencias - Laboratorio Docker Compose

## Respuestas a las preguntas de arquitectura

**1. ¿Por qué el frontend no necesita conectarse a PostgreSQL?**
Básicamente por seguridad y orden. La web (frontend) solo tiene que pedirle las cosas a la API (backend) y ya está. Si el frontend hablara directamente con la base de datos, cualquiera que use la web podría trastear con nuestros datos.

**2. ¿Por qué el backend pertenece a dos redes?**
Porque hace de puente. Tiene que estar en la red del `frontend` para enterarse de lo que pide la web, y a la vez en la red de `datos` para poder ir a buscar o guardar esa info en la base de datos de PostgreSQL.

**3. ¿Qué servicio necesita publicar un puerto en el anfitrión?**
Solo el `frontend` (la web). Es lo único que nosotros (o el usuario final) necesitamos ver desde nuestro navegador usando nuestro propio puerto (el 8080). Los demás trabajan por detrás de forma oculta.

**4. ¿Por qué publicar PostgreSQL hacia fuera no es la solución adecuada para un fallo de comunicación interno?**
Porque sería un fallo de seguridad brutal. Estarías abriendo la base de datos al público. Si hay un problema de comunicación entre contenedores, hay que arreglar las redes internas de Docker, no abrir la base de datos a todo internet.

**5. ¿Qué ventaja ofrece Docker Compose respecto a ejecutar los tres contenedores manualmente?**
Te ahorra tener que escribir un montón de comandos kilométricos en la terminal cada vez. Escribes todo en el archivo YAML una vez y levantas toda la aplicación, con sus redes y volúmenes, usando un solo comando. Es mucho más cómodo y rápido.

**6. ¿Por qué hemos dividido la arquitectura en dos redes?**
Para separar las cosas y que sea más seguro. Si alguien hackea la web (el frontend), como no está en la misma red que la base de datos, no puede llegar a ella ni robar la información.

**7. ¿Por qué el backend utiliza db como dirección de PostgreSQL?**
Porque Docker es listo y tiene su propio sistema de nombres interno (DNS). En vez de tener que buscar y poner la IP exacta del contenedor de PostgreSQL, usamos el nombre del servicio (`db`) y Docker ya se encarga de conectarlos.

**8. ¿Qué diferencia existe entre detener un proyecto y eliminar sus volúmenes?**
Si solo lo detienes (`stop` o `down`), los contenedores se apagan pero la información sigue guardada en el volumen. Si eliminas los volúmenes (`down -v`), borras el "disco duro" virtual y pierdes absolutamente todos los datos que habías metido.

**9. ¿Qué consecuencias tendría para la empresa perder el volumen de PostgreSQL?**
Pues que se liaría pardísima. Perderían absolutamente toda la información: las incidencias guardadas, los usuarios, etc. Si no tienen copias de seguridad por otro lado, se quedan en blanco y la aplicación dejaría de servir.