# Proceso de Despliegue en Azure - PokeDex

## 1. Despliegue desde Azure
Para este proyecto decidimos usar **Azure**. Intentamos primero con la opción de "Static Web Apps", pero como nos daba errores al momento de subir los archivos, optamos por usar **"App Services"**, que es una herramienta más completa para aplicaciones web.

## 2. Creación de la página y subida de archivos
Creamos nuestra aplicación llamada **pokedex**. Para subir el código, preparamos el contenido de la aplicación para producción (carpeta dist) y lo subimos usando las herramientas avanzadas de Azure (Kudu).

Específicamente, colocamos los archivos dentro de la carpeta `site/wwwroot`, que es el lugar donde el servidor busca nuestra página para mostrarla al público.

## 3. Resolución de Errores en Producción

### Error 404 en las Imágenes
Al principio tuvimos un problema: la página cargaba pero las imágenes de los Pokémon no se veían. 
* **Causa:** El servidor buscaba las imágenes en una ruta que no existía en Azure.
* **Solución:** Entramos a la consola de Kudu y creamos la carpeta necesaria para que las rutas coincidieran con nuestro código. Organizamos la carpeta de recursos en su lugar correcto y así logramos que todos los GIFs y fotos aparecieran.

### Error 500 y Problemas de Seguridad
Cuando intentamos poner seguridad, la página se rompió y mostró un "Error 500". 
* **Solución:** Tuvimos que borrar el archivo de configuración que causaba el conflicto, reiniciar el servicio desde el portal de Azure y probar con una versión del archivo `web.config` que fuera compatible con el servidor.

## 4. Configuración de Seguridad (Grado A)
Logramos alcanzar la **Calificación A** en la auditoría de **Security Headers**. Esto es muy importante porque protege nuestra aplicación de ataques comunes en internet. Configuramos los siguientes puntos:

* **HTTPS Obligatorio (HSTS):** Configuramos el servidor para que la página siempre use una conexión segura.
* **X-Frame-Options (DENY):** Esto evita que otras personas puedan poner nuestra página dentro de otro sitio web falso.
* **X-Content-Type-Options (nosniff):** Obligamos al navegador a que respete el tipo de archivo que enviamos.
* **Referrer-Policy:** Protege la privacidad de quien visita nuestra página.
* **Content Security Policy (CSP):** Creamos una "lista de permisos" para que la página pudiera hablar con la **PokeAPI** y descargar las fuentes de **Google Fonts**. Al principio bloqueamos todo por error, pero luego lo ajustamos para que la página fuera segura y funcionara a la vez.

## 5. Conclusión
Al final, el proyecto no solo muestra los Pokémon de forma correcta, sino que también es un sitio seguro. Aprendimos a manejar la consola de Azure, a organizar archivos en el servidor y a configurar reglas de seguridad que protegen nuestra aplicación.
