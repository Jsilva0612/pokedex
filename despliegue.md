Proceso de Despliegue en Azure - PokeDex
1. Despliegue desde Azure
Para este proyecto decidimos usar Azure. Intentamos primero con la opción de "Static Web Apps", pero como nos daba errores al momento de subir los archivos, optamos por usar "App Services", que es una herramienta más completa para aplicaciones web.

2. Creación de la página y subida de archivos
Creamos nuestra aplicación llamada pokedex. Para subir el código, no subimos todo el proyecto de Angular, sino solamente el contenido de la carpeta "dist", que es la carpeta que se genera cuando ejecutamos el comando ng build.

Todo este contenido lo subimos usando las herramientas avanzadas de Azure (Kudu), específicamente dentro de la carpeta site/wwwroot, que es donde el servidor busca nuestra página para mostrarla al público.

3. Resolución de Errores en Producción
Error 404 en las Imágenes
Al principio tuvimos un problema: la página cargaba pero las imágenes de los Pokémon no se veían.

Causa: El servidor buscaba las imágenes en una ruta que no existía en Azure.

Solución: Entramos a la consola de Kudu y creamos la carpeta necesaria para que las rutas coincidieran con nuestro código. Movimos la carpeta assets a su lugar correcto y así logramos que todos los GIFs y fotos aparecieran.

Error 500 y Problemas de Seguridad
Cuando intentamos poner seguridad, la página se rompió y mostró un "Error 500".

Solución: Tuvimos que borrar el archivo de configuración, reiniciar el servicio desde el portal de Azure y probar con una versión más sencilla del archivo web.config que no generara conflictos con el servidor.

4. Configuración de Seguridad (Grado A)
Logramos alcanzar la Calificación A en la auditoría de Security Headers. Esto es muy importante porque protege nuestra aplicación de ataques comunes en internet.

Para lograrlo, creamos un archivo llamado web.config y configuramos los siguientes puntos:

HTTPS Obligatorio (HSTS): Configuramos el servidor para que la página siempre use una conexión segura y cifrada.

X-Frame-Options (DENY): Esto evita que otras personas puedan "secuestrar" nuestra página y ponerla dentro de otro sitio web falso.

X-Content-Type-Options (nosniff): Obligamos al navegador a que respete el tipo de archivo que enviamos, evitando que alguien intente ejecutar virus disfrazados de otros archivos.

Referrer-Policy: Protege la privacidad de quien visita nuestra página.

Content Security Policy (CSP): Esta fue la parte más difícil. Tuvimos que crear una "lista de permisos" para que la página pudiera hablar con la PokeAPI y descargar las fuentes de Google Fonts. Al principio bloqueamos todo por error, pero luego lo ajustamos para que la página fuera segura y funcional a la vez.

5. Conclusión
Al final, el proyecto no solo muestra los Pokémon de forma correcta, sino que también es un sitio seguro. Aprendimos a manejar la consola de Azure, a organizar archivos en el servidor y a configurar reglas de seguridad que protegen nuestra aplicación.
