# Pokédex Angular

[![code style: prettier](https://img.shields.io/badge/code_style-prettier-ff69b4.svg)](https://github.com/prettier/prettier)
[![codecov](https://codecov.io/gh/keilermora/pokedex-angular/branch/master/graph/badge.svg?token=9E0D28IOFT)](https://codecov.io/gh/keilermora/pokedex-angular)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)

[https://keilermora.github.io/pokedex-angular/](https://keilermora.github.io/pokedex-angular/)

La aplicación muestra el listado y el detalle de los Pokémon de las primeras 3 generaciones.

La imagen que representa un Pokémon en el listado muestra las variaciones que estos tuvieron durante las primeras versiones, desde la versión Green (1996) hasta la version Emerald (2005).

Los detalles de un Pokémon individual muestra sus estadísticas base y los registros de la Pokédex de las diferentes versiones.

El proyecto fue desarrollado usando la librería de JavaScript [Angular](https://angular.io/) para crear la interfaz de usuario, en comunicación con la Api RESTful [PokéAPI](https://pokeapi.co/).

## Requisitos mínimos

- [Nodejs](https://nodejs.org) con soporte de largo plazo (LTS).
- Un navegador web

## Ambiente de pruebas

Ejecutar en la raíz del proyecto:

```
npm start
```

## Tutorial: crear una cuenta y preparar Azure para el despliegue

### 1. Activar Azure for Students

Para esta actividad se utilizó Azure for Students con acceso institucional. Si la institución ya habilitó una cuenta y una suscripción, inicia sesión con ese usuario y continúa en la sección **Configuración inicial: comprobar la suscripción y el acceso**; no hace falta registrar una segunda suscripción.

Si todavía no tienes Azure for Students:

1. Abre [Azure for Students](https://azure.microsoft.com/free/students/) y selecciona **Start free** o el botón de activación disponible.
2. Inicia sesión con una cuenta Microsoft. Si no tienes una, crea una y continúa con el correo institucional que utilizarás para verificar tu condición de estudiante.
3. Completa la verificación académica solicitada. Revisa el correo de la institución y confirma el código o los pasos que Microsoft envíe.
4. Completa los datos solicitados, revisa y acepta las condiciones de la oferta para activar la suscripción.
5. Cuando termine la activación, abre el [Portal de Azure](https://portal.azure.com/) con la misma cuenta.

La oferta está dirigida a estudiantes que cumplen los requisitos de elegibilidad de Microsoft y verifican su condición académica. La oferta vigente de Azure for Students no requiere tarjeta de crédito y puede incluir USD 100 en créditos por 12 meses; verifica los términos actualizados antes de registrarte en [What is Azure for Students?](https://learn.microsoft.com/azure/education-hub/about-azure-for-students).

### 2. Configuración inicial: comprobar la suscripción y el acceso

1. En el Portal de Azure, inicia sesión con la cuenta institucional.
2. Busca **Suscripciones (Subscriptions)** y confirma que aparezca **Azure for Students** con estado activo.
3. Si Azure muestra varios directorios o suscripciones, selecciona el directorio de la institución y la suscripción que usarás para el proyecto.
4. No compartas la contraseña, códigos de verificación, claves ni credenciales de publicación. Activa la verificación en dos pasos si tu institución la ofrece o la exige.

### 3. Crear el grupo de recursos del proyecto

Un grupo de recursos es el contenedor de Azure que reúne los servicios relacionados con una solución. Para crear el grupo desde el portal:

1. Busca **Grupos de recursos (Resource groups)** y selecciona **Crear (Create)**.
2. Selecciona la suscripción **Azure for Students**.
3. Escribe `PokeDex-Actividad` como nombre del grupo de recursos.
4. Selecciona una región permitida por las políticas de la suscripción. Para esta actividad se utilizó **Chile Central**.
5. Selecciona **Revisar y crear (Review + create)**, revisa los datos y pulsa **Crear (Create)**.

La suscripción puede restringir las regiones disponibles. Si una región no aparece o Azure rechaza la creación, revisa la directiva de ubicaciones de la suscripción o consulta al administrador antes de volver a intentarlo en otra región. En este proyecto se usó Chile Central, donde se pudo crear App Service.

### 4. Revisar costos y continuar con el proyecto

Antes de crear recursos, confirma la suscripción seleccionada y revisa el nivel de precios del servicio. Para esta PokeDex se creó una Web App de App Service Windows con el plan **Gratis F1**. No cambies la suscripción a pago por uso si no tienes autorización y entiendes los cargos posibles. Al terminar la actividad, elimina los recursos de prueba que ya no necesites para evitar consumir créditos. Antes de borrar un grupo de recursos, confirma su contenido: esa acción también elimina los servicios que contiene.

Los pasos de configuración de App Service, compilación y publicación de PokeDex están en [Despliegue.md](Despliegue.md).

## Referencias

- [Angular](https://angular.io/): One framework.
- [Angular Folder Structure](https://angular-folder-structure.readthedocs.io/en/latest/): Create a skeleton structure which is flexible for projects big or small.
- [Font Awesome](https://fontawesome.com/): The web's most popular icon set and toolkit.
- [Normalize.css](https://necolas.github.io/normalize.css/): A modern, HTML5-ready alternative to CSS resets.
- [PokéAPI](https://pokeapi.co/): The RESTful Pokémon API.
- [Azure for Students: requisitos y beneficios](https://learn.microsoft.com/azure/education-hub/about-azure-for-students).
- [Crear y administrar grupos de recursos en Azure Portal](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal).
