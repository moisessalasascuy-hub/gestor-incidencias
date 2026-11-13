# Laboratorio: Despliegue de una aplicación web con Docker Compose

## Justificación de la Arquitectura y Redes

**1. ¿Por qué el frontend no necesita conectarse a PostgreSQL?**
Por el patrón de arquitectura de separación de responsabilidades. El frontend es únicamente la interfaz gráfica para el usuario. Las peticiones de datos se hacen a través de la API (backend), que es quien contiene la lógica de negocio y se conecta de forma segura a la base de datos.

**2. ¿Por qué el backend pertenece a dos redes?**
Porque actúa como un intermediario (puente). Debe pertenecer a la red frontend para poder recibir las peticiones que vienen desde la interfaz web, y al mismo tiempo debe pertenecer a la red datos para poder enviar consultas al contenedor de PostgreSQL.

**3. ¿Qué servicio necesita publicar un puerto en el anfitrión?**
Solamente el servicio frontend. Es el único punto de entrada público que necesita la aplicación para que el usuario pueda acceder desde su navegador.

**4. ¿Por qué publicar PostgreSQL hacia fuera no es la solución adecuada para un fallo de comunicación interno?**
Publicar el puerto de la base de datos hacia el host expone los datos al exterior, lo cual es una vulnerabilidad de seguridad. Los servicios internos deben comunicarse única y exclusivamente mediante las redes privadas de Docker.

**5. ¿Qué ventaja ofrece Docker Compose respecto a ejecutar los tres contenedores manualmente?**
Automatización y uso declarativo. Permite definir toda la infraestructura (contenedores, redes, volúmenes, dependencias) en un único archivo. Evita errores humanos y hace que el entorno sea fácilmente reproducible y desplegable con un solo comando.

**6. ¿Por qué hemos dividido la arquitectura en dos redes?**
Por seguridad (aislamiento). Separar en las redes frontend y datos garantiza que si el contenedor del frontend fuera comprometido, el atacante no tendría acceso de red directo hacia la base de datos.

**7. ¿Por qué el backend utiliza db como dirección de PostgreSQL?**
Porque Docker Compose proporciona un servidor DNS interno automático. El nombre lógico del servicio (db) se resuelve a la IP asignada a ese contenedor dentro de la red datos, sin necesidad de utilizar direcciones IP fijas.

**8. ¿Qué diferencia existe entre detener un proyecto y eliminar sus volúmenes?**
Detener el proyecto (docker compose down) destruye los contenedores y redes, pero conserva intactos los volúmenes. Eliminar los volúmenes (docker compose down -v) destruye físicamente el espacio de almacenamiento, borrando todos los datos de forma permanente.

**9. ¿Qué consecuencias tendría para la empresa perder el volumen de PostgreSQL?**
Supondría una pérdida crítica de información. Al eliminarse el volumen persistente, desaparecerían todas las incidencias registradas, derivando en una parada operativa y la pérdida total del trabajo previo.