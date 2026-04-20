# Paso a Paso del Despliegue - PokeDex

Este documento detalla el proceso técnico, los desafíos encontrados y las soluciones aplicadas para el despliegue de la PokeDex en **Microsoft Azure**.

## 1. Estrategia de Despliegue y Cambio de Rumbo

Inicialmente, se intentó realizar el despliegue utilizando **Azure Static Web Apps**. Sin embargo, el proceso no se pudo completar debido a un error de privacidad en la conexión.

Debido a esto, se tomó la decisión de migrar a **Azure App Service**. Esta herramienta ofreció un mejor control sobre el servidor y acceso a herramientas de depuración avanzada como **Kudu**.

<img width="1920" height="1080" alt="Captura de pantalla del Panel de Azure" src="https://github.com/user-attachments/assets/34fcfdd8-a25d-4e2e-9a4b-f586c170d435" />

---

## 2. Preparación y Carga de Archivos

A diferencia del entorno de desarrollo, en Kudu solo se subio la carpeta `dist`.

### ¿Por qué la carpeta `dist`?
Contiene el código minificado y optimizado que el navegador puede interpretar rápidamente, omitiendo archivos innecesarios para la ejecución (como la pesada carpeta `node_modules`).

### Método de carga
Utilizamos la consola de herramientas avanzadas **Kudu**, navegando hasta el directorio raíz del servidor en `site/wwwroot`.

<img width="1920" height="1080" alt="Consola Kudu" src="https://github.com/user-attachments/assets/3a6234f1-e9c4-4da3-9d26-c924453dffb8" />

---

## 3. Bitácora de Errores y Soluciones

### A. Error 404 (Rutas de Imágenes)
Al desplegar, la estructura de la aplicación cargaba correctamente pero las imágenes de los Pokémon fallaban (Error 404).

<img width="1920" height="1080" alt="Error de imágenes" src="https://github.com/user-attachments/assets/39737952-182b-42ce-a16a-9015d747a164" />

* **Causa:** En el entorno local, las rutas de los assets funcionan de forma distinta a como las interpreta un servidor web en la nube.
* **Solución:** Reorganizamos manualmente la estructura de carpetas para asegurar que las rutas relativas coincidieran con la configuración de construcción de Vite.

### B. Error 500 (Configuración de Seguridad)
Al intentar implementar las cabeceras de seguridad, la aplicación colapsó completamente con un **Error 500 (Internal Server Error)**.

<img width="1920" height="1080" alt="Error 500" src="https://github.com/user-attachments/assets/0b25273b-6812-4966-aa24-57f6938b6312" />

* **Causa:** El archivo `web.config` tenía errores de sintaxis XML o directivas de seguridad no soportadas inicialmente por la versión del servidor.
* **Solución aplicada:**
    1. Se borró el archivo corrupto para recuperar la estabilidad del sitio.
    2. Se reinició el **App Service** desde el portal de Azure.
    3. Se redactó un nuevo archivo `web.config` aplicando cambios hasta lograr la funcionalidad deseada.

---

## 4. Implementación de Seguridad Avanzada (Grado A)

Para lograr el grado A en el ambito de seguridad, se configuro manualmente el servidor para inyectar encabezados de respuesta HTTP que protegen al usuario.

### Configuraciones clave en `web.config`:

* **Strict-Transport-Security (HSTS):** Fuerza al navegador a usar siempre conexiones seguras (HTTPS).
* **X-Frame-Options (SAMEORIGIN):** Previene ataques de *Clickjacking* al prohibir que el sitio sea "enmarcado" por dominios externos.
* **X-Content-Type-Options (nosniff):** Evita que el navegador intente adivinar el tipo de contenido, bloqueando la ejecución de scripts maliciosos.
* **Referrer-Policy:** Protege la privacidad del usuario al limitar la información de origen enviada en las peticiones.

<img width="1920" height="1080" alt="Resultado Security Headers" src="https://github.com/user-attachments/assets/d36d5d12-f3ac-444b-bf13-1e2762890e5d" />
