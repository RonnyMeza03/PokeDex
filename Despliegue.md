# Manual de despliegue de PokeDex en Azure App Service

## 1. Propósito y arquitectura

Esta guía explica cómo compilar y publicar la aplicación Angular PokeDex en una Web App de Azure App Service para Windows. La aplicación se entrega como archivos estáticos: Angular genera HTML, JavaScript, CSS e imágenes, IIS los sirve y `web.config` configura la navegación de Angular y las cabeceras HTTP.

La aplicación consulta la API pública de PokéAPI. No se despliega un backend propio ni una base de datos.

**Repositorio:** [RonnyMeza03/PokeDex](https://github.com/RonnyMeza03/PokeDex)  
**Aplicación publicada:** [Abrir PokeDex](https://pokedex-ronny-ftdkg6aje7crapba.chilecentral-01.azurewebsites.net/)  
**Panel de publicación Kudu:** [Zip Push Deploy](https://pokedex-ronny-ftdkg6aje7crapba.scm.chilecentral-01.azurewebsites.net/ZipDeployUI)

> Los valores de ejemplo entre `< >` se deben reemplazar por los de la máquina o cuenta que realiza el despliegue. Los valores concretos de esta actividad se muestran en cada paso.

## 2. Preparar el repositorio

El código debe estar en la raíz del repositorio, donde se encuentran `package.json`, `angular.json` y `src/`. La rama usada en esta actividad es `main`.

En `src/environments/environment.prod.ts`, la ruta de las imágenes locales debe corresponder a la raíz del sitio:

```typescript
imagesPath: '/assets/images'
```

La ruta anterior del proyecto fuente era `/pokedex-angular/assets/images`. Se usa `/assets/images` porque la aplicación se publica en la raíz del dominio de App Service.

## 3. Crear y configurar la Web App

En Azure Portal, busca **Web App** y selecciona **Crear**. En esta actividad se usó App Service porque la política regional de la suscripción no permitió crear Static Web Apps en las regiones disponibles. La Web App se pudo aprovisionar en Chile Central.

Completa **Aspectos básicos (Basics)** con estos valores:

| Campo | Valor de esta actividad |
|---|---|
| Suscripción | Azure for Students |
| Grupo de recursos | `PokeDex-Actividad` |
| Nombre | `pokedex-ronny` |
| Publicación | Código (Code) |
| Pila del entorno en tiempo de ejecución | ASP.NET V4.8 |
| Sistema operativo | Windows |
| Región | Chile Central |
| Plan de App Service | `ASP-PokeDexActividad-be46`, Gratis F1 |

ASP.NET V4.8 se seleccionó como pila disponible para el servidor Windows/IIS. La aplicación desplegada sigue siendo Angular estático; no se añadió código ASP.NET.

En las opciones adicionales:

- **Base de datos (Database):** no agregar una base de datos.
- **Implementación (Deployment):** desactivar implementación continua y autenticación básica de publicación. El paquete se sube manualmente por Kudu.
- **Redes (Networking):** mantener el acceso público habilitado y no configurar integración con red virtual.
- **Supervisión y protección (Monitoring + Protection):** Application Insights y Defender quedaron deshabilitados en esta actividad.
- **Etiquetas (Tags):** no se agregaron etiquetas.

Luego selecciona **Revisar y crear (Review + create)**. Cuando Azure valide la configuración, selecciona **Crear (Create)**. Al terminar el aprovisionamiento, selecciona **Ir al recurso (Go to resource)** y confirma que el estado de la Web App sea **En ejecución (Running)**.

La captura de evidencias muestra la vista general del recurso ya creado: estado, grupo de recursos, suscripción, ubicación, plan y sistema operativo.

![Vista general de PokeDex en Azure App Service](https://raw.githubusercontent.com/RonnyMeza03/PokeDex/main/evidencias/Captura%20de%20pantalla%202026-10-25%20014055.png)

## 4. Configurar IIS y la aplicación Angular

En la raíz del repositorio deben existir `web.config` e `index.html`. La regla de reescritura permite que las rutas de Angular, por ejemplo `/pokemon/7`, abran la aplicación al pegar la URL directamente en el navegador. Las cabeceras limitan el contenido externo y activan protecciones del navegador.

El `web.config` usado en esta actividad es:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Angular Routes" stopProcessing="true">
          <match url=".*" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="/index.html" />
        </rule>
      </rules>
    </rewrite>
    <httpProtocol>
      <customHeaders>
        <remove name="Content-Security-Policy" />
        <remove name="Strict-Transport-Security" />
        <remove name="X-Frame-Options" />
        <remove name="X-Content-Type-Options" />
        <remove name="Referrer-Policy" />
        <remove name="Permissions-Policy" />
        <remove name="X-Powered-By" />
        <add name="Content-Security-Policy" value="default-src 'self'; base-uri 'self'; object-src 'none'; frame-ancestors 'none'; form-action 'self'; upgrade-insecure-requests; script-src 'self'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com data:; img-src 'self' data: https://assets.pokemon.com; connect-src 'self' https://pokeapi.co https://beta.pokeapi.co" />
        <add name="Strict-Transport-Security" value="max-age=31536000" />
        <add name="X-Frame-Options" value="DENY" />
        <add name="X-Content-Type-Options" value="nosniff" />
        <add name="Referrer-Policy" value="strict-origin-when-cross-origin" />
        <add name="Permissions-Policy" value="camera=(), microphone=(), geolocation=()" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

La directiva `img-src` autoriza las ilustraciones externas de `assets.pokemon.com`; `connect-src` autoriza las llamadas REST y GraphQL de PokéAPI. Si se modifican los dominios usados por la aplicación, también se debe revisar la CSP.

El `src/index.html` de este proyecto puede contener un redireccionamiento que se usaba para GitHub Pages. Para esta publicación en IIS se quitó ese bloque: la regla anterior ya resuelve las rutas Angular.

## 5. Instalar dependencias y compilar

La compilación de esta actividad se realizó en Windows desde Git Bash con Node.js `v16.20.2` y npm `8.19.4`. Node.js se usó en versión portable; estos comandos agregan su carpeta al `PATH` de la ventana actual de Git Bash:

```bash
export PATH="/c/Users/RONNY/Compu Distribuida/node-v16.20.2-win-x64:$PATH"
hash -r
node -v
npm -v
```

En otra máquina, reemplaza esa ruta por la carpeta donde está `node.exe`. Después, desde la carpeta del repositorio ejecuta:

```bash
cd "/c/Users/RONNY/Compu Distribuida/PokeDex"
npm ci
npm run build -- --configuration production
```

`npm ci` instala las dependencias definidas por el proyecto. El segundo comando crea la compilación de producción en `dist/pokedex-angular`.

En la ejecución registrada, la compilación terminó correctamente. npm informó 124 vulnerabilidades en dependencias y Angular mostró advertencias de presupuesto para el CSS de detalles y el paquete inicial. No impidieron generar los archivos; no se ejecutó `npm audit fix`.

## 6. Preparar el ZIP de publicación

Después de compilar, copia `web.config` dentro de la salida de Angular y abre esa carpeta en el Explorador:

```bash
cp web.config dist/pokedex-angular/web.config
explorer.exe dist/pokedex-angular
```

En el Explorador de archivos:

1. Abre `dist/pokedex-angular`.
2. Selecciona **el contenido** de esa carpeta, incluyendo `index.html`, `web.config`, los archivos JavaScript y CSS, y las carpetas de recursos.
3. Crea un archivo ZIP con los elementos seleccionados.
4. Comprueba que `index.html` y `web.config` queden en la raíz del ZIP. No comprimas la carpeta exterior `pokedex-angular` ni incluyas `node_modules`.

La estructura esperada al abrir el ZIP se parece a esta:

```text
index.html
web.config
runtime.*.js
main.*.js
styles.*.css
assets/
```

## 7. Publicar el ZIP desde Kudu

1. En Azure Portal, abre la Web App `pokedex-ronny`.
2. En el menú lateral, abre **Herramientas de desarrollo (Development Tools) → Herramientas avanzadas (Advanced Tools)** y selecciona **Ir (Go)**.
3. En Kudu, abre **Tools → Zip Push Deploy**. La página termina en `/ZipDeployUI`.
4. Arrastra el ZIP preparado al área de carga o selecciónalo desde el equipo.
5. Espera a que finalice la publicación.
6. Abre la dirección pública de la aplicación y comprueba el resultado.

La captura de Kudu muestra los archivos publicados en `/wwwroot` y el estado **Deployment successful**.

![Publicación ZIP completada en Kudu](https://raw.githubusercontent.com/RonnyMeza03/PokeDex/main/evidencias/Captura%20de%20pantalla%202026-10-25%20014154.png)

En esta actividad se publicó manualmente por Kudu. No se usó Azure CLI, FTP ni una acción de GitHub Actions. Para actualizar la aplicación, vuelve a compilar, prepara un nuevo ZIP con la misma estructura y cárgalo por Kudu.

## 8. Comprobar el despliegue

Usa la dirección pública de App Service para realizar estas comprobaciones:

1. Abre la página principal y confirma que aparece el catálogo de 151 Pokémon.
2. Busca `pikachu` y abre su ficha.
3. Abre directamente `https://pokedex-ronny-ftdkg6aje7crapba.chilecentral-01.azurewebsites.net/pokemon/7`; debe cargar la ficha de Squirtle y su ilustración. La captura de evidencia también muestra la ruta directa `/pokemon/5`, con la ficha e ilustración de Charmelon.
4. Confirma que la página usa HTTPS.
5. Opcionalmente, analiza el dominio en [SecurityHeaders.com](https://securityheaders.com/) para comprobar las cabeceras HTTP.

Las comprobaciones de esta publicación fueron satisfactorias: catálogo, búsqueda, ficha, ilustración, ruta directa y HTTPS. SecurityHeaders.com asignó **A+** el 25 de octubre de 2026.

![Página principal con el catálogo de 151 Pokémon](https://raw.githubusercontent.com/RonnyMeza03/PokeDex/main/evidencias/Captura%20de%20pantalla%202026-10-25%20014113.png)

![Ficha de Charmelon abierta directamente en la ruta /pokemon/5](https://raw.githubusercontent.com/RonnyMeza03/PokeDex/main/evidencias/Captura%20de%20pantalla%202026-10-25%20014219.png)

![Captura del resultado A+ de SecurityHeaders](https://raw.githubusercontent.com/RonnyMeza03/PokeDex/main/evidencias/Captura%20de%20pantalla%202026-10-25%20011124.png)
