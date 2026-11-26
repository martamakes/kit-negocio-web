# Errores de instalación de dependencias

Informe de los intentos realizados en el ordenador de clase.

## Entorno

- Proyecto: `kit-negocio-web`
- Node.js: `v26.13.1`
- npm: `11.20.0`
- npm tenía `NPM_CONFIG_OFFLINE=true` (modo sin conexión).
- La web (`web/`) y el panel Sanity (`studio/`) tienen cada uno su propio `package-lock.json`.

## Errores observados

Al ejecutar `npm ci` en `web/` y `studio/`, npm terminó con:

```text
npm error code ENOTCACHED
npm error request to https://registry.npmjs.org/zwitch/-/zwitch-2.0.4.tgz failed:
cache mode is 'only-if-cached' but no cached response is available.
```

El paquete `zwitch@2.0.4` no estaba disponible en la caché local y npm no podía descargarlo por estar en modo sin conexión. Los dos proyectos encontraron el mismo error.

También aparecieron avisos `EPERM` al intentar limpiar carpetas de `node_modules` (`anymatch` en `web/` y `@architect/asap` en `studio/`). Esto puede indicar que Windows tenía archivos bloqueados durante la limpieza. npm dijo además que no pudo escribir sus archivos de registro en `%LOCALAPPDATA%\npm-cache\_logs`.

Se probó `npm ci --offline=false` en `web/`, pero no llegó a mostrar resultado y se interrumpió. Por tanto, no se confirmó que la instalación en línea terminara.

## Cómo reintentarlo

Abre PowerShell con conexión a Internet y ejecuta:

```powershell
cd C:\proyectos\kit-negocio-web
$env:NPM_CONFIG_OFFLINE = "false"
npm --prefix web ci
npm --prefix studio ci
```

Si reaparece `EPERM`, cierra procesos que puedan estar usando el proyecto (por ejemplo, servidores de desarrollo o el editor) y vuelve a ejecutar los comandos. No se pudo verificar una instalación completa en el intento anterior.
