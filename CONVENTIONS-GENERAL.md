# Convenciones generales de desarrollo

**Versión 3.0.0** · 2026-10-12 · historial al final del documento

Documento **transversal**: aplica a todos los proyectos, frontend y backend.

Este documento **no inventa convenciones**. Adopta los estándares públicos de cada
ecosistema y sólo documenta las decisiones que esos estándares dejan abiertas.
Cualquier persona externa que revise el código debe poder reconocer la convención
por su nombre y consultar su fuente original.

## Cómo se usa este documento

- Es el **único** documento de convenciones y aplica a todo proyecto, existente o nuevo.
  Cada repositorio incluye una copia **idéntica**; no existe un `CONVENTIONS.md` por proyecto.
- Lo específico de un proyecto no se escribe aquí: las excepciones a estas reglas se
  documentan, con su motivo, en el `README.md` de ese proyecto.
- Una excepción que no esté documentada no es una excepción: es un incumplimiento.
- **El original vive en el repositorio `conventions`** y sólo ahí se edita. Las copias de
  los proyectos se reemplazan completas, nunca se editan.
- Al modificarlo se sube la versión, se agrega una línea al historial y el cambio se
  replica en todas las copias en el mismo día. Una copia está al día si su versión
  coincide con la del original.

  | Versión | Cuándo | Ejemplo |
  |---|---|---|
  | **Mayor** (2.0.0 → 3.0.0) | Se cambia o elimina una regla existente: código que la cumplía deja de cumplir | Cambiar el casing de una categoría |
  | **Menor** (2.0.0 → 2.1.0) | Se agrega una regla o sección nueva; el código existente que no la cumpla se adapta o se declara como excepción en su `README.md` | Agregar los breakpoints responsivos |
  | **Parche** (2.0.0 → 2.0.1) | Redacción, erratas o ejemplos, sin cambiar reglas | Corregir una referencia rota |

  La versión va dentro del texto, no en el nombre del archivo (principio 3 del §3): git
  versiona cada repositorio, pero no puede comparar copias que viven en repositorios distintos.

---

## 1. Estándares en los que se basa este documento

| Ámbito | Estándar adoptado | Tipo | Fuente |
|---|---|---|---|
| Componentes y plantillas Vue | **Vue.js Style Guide** (prioridades A, B y C) | Oficial del framework | `vuejs.org/style-guide/` (Vue 3) · `v2.vuejs.org/v2/style-guide/` (Vue 2) |
| JavaScript | **JavaScript Standard Style** (vía `neostandard`; Vue 2 con Vue CLI: vía `@vue/standard`, §14) | De facto | `standardjs.com` · `github.com/neostandard/neostandard` |
| JavaScript — nombres de constantes | **Google JavaScript Style Guide §6.2.5** (sólo esta regla; el resto del formato es Standard) | De facto | `google.github.io/styleguide/jsguide.html` |
| PHP — nombres y estructura | **PSR-1** (Basic Coding Standard) | Formal (PHP-FIG) | `www.php-fig.org/psr/psr-1/` |
| PHP — formato | **PER Coding Style 3.0** (extiende y reemplaza a PSR-12; es la versión que aplica PHP-CS-Fixer con `@PER-CS3x0`, §14) | Formal (PHP-FIG) | `www.php-fig.org/per/coding-style/` |
| PHP — autocarga | **PSR-4** (Autoloader) | Formal (PHP-FIG) | `www.php-fig.org/psr/psr-4/` |
| CSS | **BEM** para componentes complejos | De facto | `getbem.com` |
| Breakpoints responsivos | **Bootstrap 5** (`sm` 576 · `md` 768 · `lg` 992 · `xl` 1200; sin `xxl`), mobile first | De facto | `getbootstrap.com/docs/5.3/layout/breakpoints/` |
| URLs | Minúsculas y guiones (guía de Google para URLs) | De facto / SEO | `developers.google.com/search` |
| Variables de entorno | **POSIX** (IEEE Std 1003.1): mayúsculas, dígitos y `_` | Formal | POSIX 8.1 |
| Mensajes de commit | **Conventional Commits 1.0.0** | De facto | `conventionalcommits.org` |
| Formato de editor | **EditorConfig** | De facto | `editorconfig.org` |
| SQL / nombres de tablas | snake_case minúsculas | Convención de facto (sin estándar formal) | — |
| Charset de la BD | `utf8mb4` + `utf8mb4_unicode_ci` | **Decisión propia** (común a MySQL y MariaDB, §7.1) | — |
| Migraciones de BD | **Phinx** | De facto | `phinx.org` |
| JSON de la API | snake_case | **Decisión propia** (ver §6) | — |
| HTTP: métodos, códigos y cabeceras | **RFC 9110** (HTTP Semantics) | Formal (IETF) | `rfc-editor.org/rfc/rfc9110` |
| Sesión | **JWT** (RFC 7519) de vida corta con `firebase/php-jwt` + token de renovación de un solo uso, ambos en cookies `httpOnly` | Formal (IETF) / OWASP | `rfc-editor.org/rfc/rfc7519` · `cheatsheetseries.owasp.org` |
| Fechas en la API | **ISO 8601** en UTC (`2026-10-11T22:03:00Z`) | Formal (ISO) | `iso.org/iso-8601-date-and-time-format.html` |
| Contraseñas | **NIST SP 800-63B-4** | Formal (NIST) | `pages.nist.gov/800-63-4/sp800-63b.html` |
| Cabeceras de seguridad de la API | **OWASP REST Security Cheat Sheet** | De facto (OWASP) | `cheatsheetseries.owasp.org` |

Lo que **no** tiene un estándar único y por eso se decide aquí de forma explícita:
nombres de carpetas, casing del JSON de la API, prefijos de tablas, el nombre de los
recursos y el idioma de las URLs, la envoltura de respuesta y el parámetro `type` (§6), y la
organización interna del frontend y del backend (§4.2, §5.2).

## 2. La regla de fondo: el casing indica la *categoría*, no el gusto

Esta es la respuesta a *"¿cuándo uso cada uno?"*. Es coherente entre lenguajes
porque todos los estándares del §1 comparten la misma lógica: **la forma del nombre
dice qué clase de cosa es**.

| Casing | Qué categoría representa | Dónde | Por qué |
|---|---|---|---|
| `PascalCase` | Un **tipo** o algo que se instancia | Clases PHP y sus carpetas PSR-4, componentes `.vue` | Un componente es un "tipo de UI"; se instancia como una clase |
| `camelCase` | Una **instancia** o un **comportamiento** | Variables, funciones, métodos, propiedades | Es el valor concreto, no el molde |
| `UPPER_SNAKE_CASE` | Un **valor fijo** conocido de antemano | Constantes, variables de entorno, mutations de Vuex (Vue 2) | Grita "esto no cambia en tiempo de ejecución" |
| `kebab-case` | Un nombre que es **ruta o marcado** | URLs, carpetas, archivos de asset, archivos de rutas de la API (copian su URL), clases CSS, atributos HTML | En rutas, una mayúscula crea ambigüedad: `/Users` y `/users` son distintos en Linux e iguales en Windows. Los atributos HTML no distinguen mayúsculas. El `_` se pierde al subrayar enlaces |
| `snake_case` | Mundo de **datos** | Tablas y columnas SQL, campos JSON (también cuando los usa el frontend) | En SQL las columnas no distinguen mayúsculas y las tablas sí, según el sistema (§7): en minúsculas no hay ambigüedad. El `_` es el separador tradicional de SQL |

Dos consecuencias prácticas que no son estéticas:

- **El casing de los archivos es una defensa técnica**, no una preferencia:
  Windows tiene sistema de archivos insensible a mayúsculas y Linux no. `UserCard.vue`
  importado como `usercard.vue` funciona en Windows y **rompe el build en el servidor**.
  Por eso carpetas, URLs y assets van en minúsculas (no hay mayúsculas que recordar; la
  única excepción son las carpetas de clases PSR-4, §5.2), y un archivo de código se
  importa exactamente como está escrito en disco.
- **Mutations de Vuex en mayúsculas** (Vue 2; Pinia no tiene mutations): el *nombre* de una
  mutation es una constante que se declara una vez y se usa en varios lugares (patrón de la
  documentación de Vuex), así que entra en la categoría de valor fijo. De paso, la mayúscula
  marca en un diff el único punto donde cambia el estado del store.

## 3. Principios

1. **Un nombre, una convención.** El mismo concepto se nombra igual en todos los proyectos.
2. **El nombre describe el rol, no el archivo.** `HomeHero`, no `MainComp`.
3. **El versionado es de git, no del nombre del archivo.** Nunca `V1`, `v2`, `-final`, ni la
   fecha en que se editó. Una fecha o versión que pertenece al **contenido** sí va en el nombre:
   el periodo de los datos (`period-2019-2024/`), el año de un informe (`annual-report-2026`)
   o la versión publicada por un tercero (`montserrat-v31`). Prueba: si al editar el archivo
   habría que renombrarlo, es versionado y no va.
4. **Sin ruido.** Un sufijo de tipo (`View`, `Service`, `Controller`…) se usa sólo cuando el
   nombre sale de su carpeta —en un `import`, un `use` o una traza de error— y ahí podría
   confundirse con otro del mismo nombre base: `UsersView`, `usersService`, `UsersController`
   y `UsersService` conviven en el mismo código. Si no hay con qué confundirlo, la marca de tipo
   es ruido, como prefijo o como sufijo: `Comp` (casi todo en Vue es un componente) o `store`
   dentro de `stores/` (`storeNotifications.js`, `notificationsStore.js`).
5. **ASCII, minúsculas y guiones** en carpetas, URLs y assets. Sin espacios, acentos ni `ñ`.
   Los archivos de código siguen el casing de lo que definen (§4.1, §5.1): `UserCard.vue`,
   `UsersController.php`, `visitTracker.js`. También en ASCII, sin espacios ni acentos.
   Única excepción en carpetas: las que contienen clases PHP con namespace van en
   PascalCase (`Controllers/`), porque PSR-4 las exige así (§5.2). Única excepción en
   archivos: los nombres que impone una herramienta, como las migraciones de Phinx (§7.2), los
   hooks de git (`pre-commit`) o los archivos de configuración (`phpunit.xml`, `vite.config.js`).
6. **Idioma:** identificadores y URLs de la API en **inglés**; textos de UI, URLs del frontend y nombres oficiales
   del dominio (siglas como CURP o RFC, figuras legales como `consejos-regionales`) en **español**.
   Los nombres de carpetas y archivos son identificadores: van en inglés (`period-2019-2024/`),
   salvo los que el usuario descarga o ve por URL, que siguen la regla de las URLs del
   frontend (`public/pdf/aviso-de-privacidad.pdf`).
   No mezclar idiomas dentro de un identificador salvo nombre oficial del dominio.
7. **Siglas oficiales en MAYÚSCULAS** en PascalCase (`CURPService`, `AboutRFCView`)
   y en minúsculas en rutas (`/curp`, `curp.routes.php`).
8. **Tests donde hay lógica.** Todo bug corregido lleva un test que lo reproduce, cuando es
   testeable, y todo código nuevo con lógica (Services del backend; services, composables y
   utilidades del frontend) lleva tests. No hay porcentaje de cobertura obligatorio, y la
   regla no es retroactiva: el código existente sin tests no se cubre de golpe. Los nombres
   de los tests son identificadores (inglés); sus descripciones son texto (español). Ver §4.5
   y §5.3.
9. **Un control que no se probó no existe.** Todo hook, paso de la CI, regla de `.htaccess` o
   escáner se prueba al crearlo o cambiarlo con un caso que **debe** fallar, y falla en voz
   alta: nada de `2>/dev/null` ni `|| true` que oculten errores. Un control que dice "limpio"
   sin haber revisado nada es peor que no tenerlo. Ejemplo: `grep` termina con 1 cuando no
   encuentra nada y con 2 o más cuando falla; un control trata distinto los dos casos (§14).
10. **Una corrección se cierra en tres pasos:** un test que reproduce el fallo (principio 8),
    un barrido de "¿dónde más vive este mismo patrón?" en todo el repo, y la verificación
    contra lo commiteado (`git show HEAD:<archivo>`), no contra el disco: corregido en disco
    no es corregido.

---

## 4. Frontend (Vue)

Los proyectos nuevos usan **Vue 3 + Vite + Pinia**. Los proyectos Vue 2 existentes siguen
con Vue CLI + Vuex. Los componentes Vue 3 se escriben con **Composition API y
`<script setup>`**; los Vue 2, con Options API. Las reglas de §4.1 a §4.3 aplican a ambos;
lo que cambia por stack está en §4.4.

### 4.1 Nomenclatura

La columna *Fuente* indica de dónde viene la regla: `Vue-A/B/C` = prioridad de la guía
oficial de Vue; `Standard` = JavaScript Standard Style; `Google` = Google JavaScript Style
Guide (§1); `Propio` = decisión de estos proyectos.

| Elemento | Convención | Ejemplo | Fuente |
|---|---|---|---|
| Componentes `.vue` | PascalCase, **2+ palabras obligatorio** | `UserCard.vue` | Vue-A + Vue-B |
| Componentes base/genéricos | Prefijo `Base` (la guía admite `Base`, `App` o `V`; aquí sólo `Base`) | `BaseButton.vue`, `BaseIcon.vue` | Vue-B |
| Componentes de instancia única | Prefijo `The` | `TheHeader.vue`, `TheFooter.vue`, `TheNavbar.vue` | Vue-B |
| Hijos acoplados a un padre | Nombre del padre como prefijo | `TodoListItem.vue` | Vue-B |
| Orden de palabras | Del sustantivo general al específico | `SearchButtonClear`, no `ClearSearchButton` | Vue-B |
| Palabras completas | Sin abreviaturas | `SettingsPanel`, no `SetPnl` | Vue-B |
| Props (declaración / template) | camelCase / kebab-case | `myProp` / `my-prop="x"` | Vue-B |
| Orden de bloques del `.vue` | Según stack (§4.4) | — | Vue-C |
| Orden interno del componente | Según stack (§4.4) | — | Vue-C / Propio |
| Eventos | Escucha en kebab-case en plantillas; al emitir, según stack (§4.4) | `@item-selected` | Vue (plantillas) |
| Vistas enrutadas | PascalCase + sufijo `View` | `BooksView.vue` | Propio |
| Layouts | PascalCase + sufijo `Layout` | `DashboardLayout.vue` | Propio |
| Archivos JS utilitarios | camelCase | `charts.js`, `visitTracker.js` | Propio |
| Módulos de estado | camelCase por dominio, sin prefijo ni sufijo `store` | `notifications.js` | Propio |
| Archivos de rutas | camelCase, sin prefijo ni sufijo `routes` | `router/dashboard.js` | Propio |
| Servicios de API | Recurso de la API en camelCase + sufijo `Service` | `usersService.js` (`/users`) | Propio |
| Variables y funciones | camelCase | `getUserData()` | Standard |
| Datos de la API | Se conservan en snake_case, tal como llegan (§4.3) | `user.created_at` | Propio |
| Constantes de módulo | UPPER_SNAKE_CASE | `MAX_NOTIFICATIONS` | Google |
| Constantes locales de función | camelCase | `const maxLength = 2` | Google |
| Ruta `path` | kebab-case | `/acerca-de` | Google URLs |
| Ruta `name` | PascalCase, sin `Comp`/`View` | `AboutUs` | Propio |
| Carpetas | kebab-case | `user-profile/` | Propio |
| Assets (img, json, fuentes) | kebab-case | `annual-report-2026-1.webp` | Propio |
| Variables de entorno | Prefijo del stack (§4.4) + UPPER_SNAKE_CASE | `VITE_API_SERVER` | Vite / Vue CLI (obligatorio) |

El esquema `Base` / `The` / prefijo-del-padre de la guía de Vue es el **reemplazo correcto
del sufijo `Comp`**: comunica la misma intención pero con información real.
`LoaderComp.vue` → `BaseLoader.vue`; `HeaderComp.vue` → `TheHeader.vue`.

### 4.2 Estructura

```
src/
  assets/        css/, fonts/, img/   (todo kebab-case)
  components/    reutilizables; subcarpeta por dominio (map/, dashboard/, charts/)
  views/         una vista por ruta; subcarpeta por sección
  layouts/       plantillas contenedoras
  router/        index.js + un archivo por sección (dashboard.js)
  stores/        un store por dominio (Pinia) · en Vue 2: store/ con index.js + módulos Vuex
  services/      todo el acceso HTTP a la API (axios)
  composables/   lógica compartida entre componentes (useIdleSession.js, §4.3) · en Vue 2: mixins/
  plugins/       inicialización de librerías (axios.js, vuetify.js, leaflet.js)
  js/            utilidades puras sin dependencia de Vue
  data/          catálogos y constantes estáticas
public/          img/, json/, pdf/   (kebab-case)
tests/           misma estructura que src/; <Nombre>.test.js (§4.5)
index.html       con la etiqueta viewport (§8.1)
vite.config.js   alias @ y configuración de Vitest (§4.5)
eslint.config.js (§14)
.env.development, .env.production   sólo variables públicas (§10)
.nvmrc           versión de Node que corre (§10)
.githooks/       hooks de git (§14)
.github/workflows/  ci.yml (§14)
```

### 4.3 Reglas

- **`UPPER_SNAKE_CASE` es para constantes de configuración a nivel de módulo**, no para todo
  lo declarado con `const`. Un `const maxLength = 2` dentro de una función es una variable
  que no se reasigna, no una constante de configuración: se queda en camelCase. La mayúscula
  significa "esto se consulta desde varios lugares y no cambia nunca".
  Además, la constante debe ser **profundamente inmutable**: `const` sólo impide reasignar la
  variable, no modificar el objeto que guarda.
  ```js
  const MAX_NOTIFICATIONS = 5      // constante: el valor no puede cambiar
  const activeUsers = new Set()    // no es constante: el Set se modifica → camelCase
  ```
- **Funciones con patrón verbo + sustantivo:** `getUserData()`, `fetchDocuments()`.
- **Nombres descriptivos, sin abreviaturas ambiguas.** Se acepta un identificador de una letra
  sólo en callbacks de una línea (`filter(n => n.id !== id)`); en cualquier función más larga
  se escribe completo (`notification`).
- **Carpetas de periodo con prefijo semántico y separadores**, nunca la fecha pegada:
  `20192024/` → `period-2019-2024/`. Si dos versiones del código deben **ejecutarse a la
  vez** durante una migración, `legacy/` y `current/`, nunca una fecha; `legacy/` se borra
  cuando la migración termina. Código que ya no se ejecuta es un respaldo (§11).
- **Nunca sufijos de versión ni de prueba en `src/`:** `ProductCard_V1..V3`, `DashboardViewV1.vue`,
  `main v1.js`, `testCharts.vue`. Sólo existe la versión vigente; el resto vive en git.
- **El `.` nunca separa palabras.** Sólo separa la extensión y, cuando una convención lo
  define, un sufijo de tipo (`user-profiles.routes.php`, `.env.example`, `UserCard.test.js`).
  `BarChart.js.js`
  (doble extensión) y `user.settings.json` (punto entre palabras) están mal formados.
- **Ni vistas ni componentes llaman a `axios` directamente**: usan una función de `src/services/`,
  nombrada con verbo (`getUsers`, `sendMessage`). Los services usan la instancia `api` de
  `src/plugins/axios.js`, la única que conoce la URL base (`<PREFIJO>API_SERVER`, §4.4). Envía
  las cookies de sesión y, ante un 401, renueva la sesión y reintenta una vez; si no puede,
  manda al login (§6.2). Probada con Vitest (renueva y reintenta; una sola renovación para
  varias peticiones que fallan a la vez; al login si la renovación o el reintento responden 401;
  sin renovar ante un 401 del login; sin redirigir si el servidor no responde) y en el navegador:
  ```js
  import axios from 'axios'
  import router from '@/router'

  // Instancia única para la API: envía las cookies de sesión y, ante un 401, renueva la sesión
  // y reintenta una vez; si no puede, manda al login (§6.2).
  export const api = axios.create({
    baseURL: import.meta.env.VITE_API_SERVER,
    withCredentials: true
  })

  // Hora de la última petición: useIdleSession mantiene viva la sesión mientras hay actividad.
  export const lastRequest = { at: Date.now() }

  let refreshing = null

  // Una sola renovación compartida por todas las peticiones que fallen a la vez.
  export function refreshSession () {
    refreshing ??= api.post('/auth/refresh').finally(() => { refreshing = null })
    return refreshing
  }

  api.interceptors.request.use(config => {
    lastRequest.at = Date.now()
    return config
  })

  api.interceptors.response.use(
    response => response,
    async error => {
      const { config, response } = error
      // Las rutas de /auth/ las maneja quien las llama: un 401 del login es una contraseña mal escrita.
      if (response?.status === 401 && !config.url.startsWith('/auth/')) {
        if (!config.retried) {
          config.retried = true
          const refreshed = await refreshSession().then(() => true, refreshError => {
            // Sin respuesta del servidor no es una sesión terminada: no se manda al login.
            if (refreshError.response?.status !== 401) throw refreshError
            return false
          })
          if (refreshed) return api(config)
        }
        router.push({ name: 'Login' })
      }
      return Promise.reject(error)
    }
  )
  ```
- **Cierre por inactividad,** con `src/composables/useIdleSession.js`, que se llama en el layout de
  las vistas que requieren sesión (`DashboardLayout.vue`). Tras `<PREFIJO>SESSION_IDLE_TTL`
  segundos sin actividad del usuario cierra la sesión; mientras la haya, la renueva antes de que
  el servidor la dé por inactiva, aunque el usuario sólo lea sin hacer peticiones. El servidor
  aplica el mismo límite por su cuenta (§6.2): este composable es la comodidad, no el control.
  Probado con Vitest (cierra a los 7 minutos sin actividad; con actividad continua sigue abierta
  pasados los 7 minutos; no renueva sin actividad; deja de vigilar al desmontar) y en el navegador:
  ```js
  import { onMounted, onBeforeUnmount } from 'vue'
  import { useRouter } from 'vue-router'
  import { api, lastRequest, refreshSession } from '@/plugins/axios'

  const ACTIVITY_EVENTS = ['pointerdown', 'pointermove', 'keydown', 'wheel', 'touchstart']

  // Cierra la sesión tras SESSION_IDLE_TTL sin actividad del usuario y, mientras la haya,
  // renueva la sesión antes de que el servidor la dé por inactiva (§6.2). Se usa en el layout
  // de las vistas que requieren sesión.
  export function useIdleSession () {
    const router = useRouter()
    const idleMs = Number(import.meta.env.VITE_SESSION_IDLE_TTL) * 1000
    let lastActivityAt = Date.now()
    let timer = null

    function markActivity () {
      lastActivityAt = Date.now()
    }

    function stop () {
      clearInterval(timer)
      ACTIVITY_EVENTS.forEach(name => window.removeEventListener(name, markActivity))
    }

    async function goToLogin () {
      stop()
      await router.push({ name: 'Login' })
    }

    function tick () {
      const now = Date.now()
      if (now - lastActivityAt >= idleMs) {
        // Si el cierre no llega al servidor, éste igual da la sesión por inactiva.
        api.post('/auth/logout').finally(goToLogin)
      } else if (lastActivityAt > lastRequest.at && now - lastRequest.at >= idleMs / 2) {
        refreshSession().catch(goToLogin)
      }
    }

    onMounted(() => {
      ACTIVITY_EVENTS.forEach(name => window.addEventListener(name, markActivity, { passive: true }))
      timer = setInterval(tick, 1000)
    })
    onBeforeUnmount(stop)
  }
  ```
  En Vue 2, la misma lógica va en un mixin del layout (§4.4).
- **Fechas en hora de México.** La API las entrega en UTC (§6.4) y se muestran con `Intl`,
  nunca formateando el texto a mano (probado):
  ```js
  new Intl.DateTimeFormat('es-MX', { dateStyle: 'medium', timeStyle: 'short', timeZone: 'America/Mexico_City' })
    .format(new Date(user.created_at)) // '2026-10-11T22:03:00Z' → '11 oct 2026, 4:03 p.m.'
  ```
  Una fecha sola (`YYYY-MM-DD`, §6.4) se formatea con `timeZone: 'UTC'`: `new Date('2026-10-11')`
  es la medianoche UTC, que en México todavía es el día 10 (probado: con la zona de México se
  muestra "10 oct 2026").
  ```js
  new Intl.DateTimeFormat('es-MX', { dateStyle: 'medium', timeZone: 'UTC' })
    .format(new Date(user.birth_date)) // '2026-10-11' → '11 oct 2026'
  ```
- **Archivos con `FormData`,** enviados por la instancia `api` (`api.post('/applications', formData)`)
  y sin fijar a mano la cabecera `Content-Type`: el navegador la arma con el separador que
  necesita. La validación en el navegador (`accept`, tamaño) es sólo comodidad para el
  usuario; la que cuenta es la del servidor (§5.4). La URL de un archivo es
  `<PREFIJO>MULTIMEDIA_URL` (§10) + la ruta relativa que devuelve la API.
- **HTML y URLs que vienen de la API** pasan por `src/js/safeHtml.js` (probado con DOMPurify 3
  en jsdom): HTML saneado antes de `v-html`, y sólo URLs `http(s)` en un `:href`. Si un campo
  no necesita HTML, se muestra con `{{ }}` y no hace falta sanearlo.
  ```js
  import DOMPurify from 'dompurify'

  // HTML que viene de la API: se sanea antes de v-html (§4.3).
  export function safeHtml (html) {
    return DOMPurify.sanitize(html)
  }

  // URL que viene de la API: sólo http(s) llega a un :href (§4.3).
  export function safeUrl (url) {
    return /^https?:\/\//i.test(url) ? url : null
  }
  ```
- **Dependencias sólo desde npm,** bajo el lockfile y `npm audit`. Nunca un CDN con `@latest`;
  si un CDN es inevitable, versión exacta con `integrity` y `crossorigin="anonymous"`.
- **Sin source maps en producción:** permiten reconstruir el código fuente. Vite no los genera
  por defecto (`build.sourcemap`); en Vue CLI, `productionSourceMap: false` en `vue.config.js`.
- **CSP del frontend en dos pasos:** primero `Content-Security-Policy-Report-Only`, se revisan
  los reportes y se ajusta; después la cabecera real. Si las dependencias vienen de npm, la
  política no necesita listar CDNs.
- **Los datos de la API no se renombran.** Un campo se llama igual en la BD, la API, la
  pestaña Network del navegador y Vue, sin capa de conversión:
  - Se leen y se envían en snake_case: `user.created_at`, `{ created_at: date }`.
  - Al desestructurar se renombra a camelCase: `const { created_at: createdAt } = user`.
    Standard marca `const { created_at } = user` porque crea una variable en snake_case.
  - El estado propio de la interfaz (`isSelected`, `isExpanded`) no se agrega al objeto de
    la API: va en un estado aparte. Así un objeto nunca mezcla los dos estilos y no se
    reenvían a la API campos que no espera.

### 4.4 Diferencias por stack

| Tema | Vue 3 (proyectos nuevos) | Vue 2 (proyectos existentes) |
|---|---|---|
| Build | Vite | Vue CLI |
| Guía de estilo | `vuejs.org/style-guide/` | `v2.vuejs.org/v2/style-guide/` |
| Estilo de componente | Composition API con `<script setup>` | Options API |
| Orden de bloques | `<script setup>`, `<template>`, `<style>` | `<template>`, `<script>`, `<style>` (el que genera Vue CLI) |
| Orden interno | 1. `import` · 2. `defineProps`, `defineEmits` · 3. stores y composables (`useNotificationsStore()`, `useRoute()`) · 4. estado (`ref`, `reactive`) · 5. `computed` · 6. `watch` · 7. ciclo de vida (`onMounted`…) · 8. funciones | `name`, `components`, `props`, `data`, `computed`, `watch`, ciclo de vida, `methods` (guía de Vue, prioridad C) |
| Estado | Pinia: `stores/notifications.js` exporta `useNotificationsStore` | Vuex: `store/notifications.js`; mutations en UPPER_SNAKE_CASE (`PUSH`), actions y getters en camelCase (`notify()`) |
| Lógica compartida | Composables con prefijo `use`: `composables/useDebounceSearch.js` | Mixins con sufijo `Mixin`: `mixins/debounceSearchMixin.js` |
| Variables de entorno | Prefijo `VITE_`, leídas con `import.meta.env.VITE_API_SERVER` | Prefijo `VUE_APP_`, leídas con `process.env.VUE_APP_API_SERVER` |
| Emitir eventos | camelCase: `emit('itemSelected')`; Vue lo convierte y el padre escucha `@item-selected` | kebab-case: `$emit('item-selected')`; Vue 2 no convierte el nombre |
| ESLint (`eslint-plugin-vue`) | Ver §14 | Ver §14 |

El prefijo `use` de Pinia y de los composables es la convención oficial de Vue 3, y cumple
el principio 4 del §3: `useNotificationsStore` no se confunde con el componente ni con el
servicio del mismo dominio.

Composition API es lo que la guía oficial recomienda para aplicaciones completas con build,
y es la que usan los composables y Pinia. La guía no fija un orden dentro de `<script setup>`,
así que el de la tabla es propio: sigue el mismo orden que la guía fija para Options API
(props → data → computed → watch → ciclo de vida → methods), para que un componente
Vue 2 y uno Vue 3 se lean igual de arriba abajo.

### 4.5 Tests

Vitest + `@vue/test-utils` + happy-dom: la combinación que recomienda la guía de Vue para
proyectos con Vite. Aplica a Vue 3. Un proyecto Vue 2 (Vue CLI, sin Vite) queda fuera de esta
configuración: si tiene tests (por ejemplo, Jest con `@vue/cli-plugin-unit-jest`), su script se
llama `test`, para que la CI los corra; si no tiene, lo declara en su `README.md` y quita el paso
`npm test` de su CI (§14).

- **Ubicación:** `tests/` en la raíz, con la misma estructura que `src/`. El archivo se llama
  como lo que prueba + `.test.js`: `src/components/UserCard.vue` →
  `tests/components/UserCard.test.js`. Nada de pruebas dentro de `src/` (§4.3).
- **Configuración**, en `vite.config.js` (probada con Vitest 5), junto al alias `@` → `src/`
  que ya trae la plantilla de `create-vue` (§16):
  ```js
  test: {
    environment: 'happy-dom',
    include: ['tests/**/*.test.js']
  }
  ```
  Y el script `"test": "vitest run"` en `package.json`: sin `run`, Vitest se queda vigilando
  cambios y no termina.
- **Imports explícitos** (`import { describe, it, expect } from 'vitest'`), sin variables
  globales: así Standard no necesita configuración extra.
- **Tests de saneado de HTML en jsdom,** no en happy-dom: con happy-dom, DOMPurify devuelve un
  resultado incorrecto (probado: dejaba pasar un `<script>`). El archivo de test empieza con
  `// @vitest-environment jsdom`, y el resto de los tests sigue en happy-dom. jsdom se instala
  en una versión compatible con el Node del proyecto (probado con jsdom 26).
- **Componentes:** se prueba su interfaz pública —props, eventos y slots—, no su estado
  interno, y sin snapshots (guía de Vue). **Services:** con la instancia `api` simulada,
  `vi.mock('@/plugins/axios', () => ({ api: { get: vi.fn() } }))`. **El plugin de axios:** el
  servidor se simula con `api.defaults.adapter`, una función que recibe la petición y devuelve
  o lanza la respuesta, sin red.
  ```js
  import { describe, it, expect } from 'vitest'
  import { mount } from '@vue/test-utils'
  import UserCard from '@/components/UserCard.vue'

  describe('UserCard', () => {
    it('muestra el nombre y emite itemSelected con el id al hacer clic', async () => {
      const wrapper = mount(UserCard, { props: { user: { id: 7, full_name: 'Ana López' } } })

      expect(wrapper.text()).toContain('Ana López')
      await wrapper.trigger('click')
      expect(wrapper.emitted('itemSelected')).toEqual([[7]])
    })
  })
  ```

---

## 5. Backend (PHP)

### 5.1 Nomenclatura

| Elemento | Convención | Ejemplo | Fuente |
|---|---|---|---|
| Clases | PascalCase (*StudlyCaps*), una por archivo homónimo | `VisitsController.php` | PSR-1 |
| Métodos | camelCase | `upsertPage()` | PSR-1 |
| Constantes de clase | UPPER_SNAKE_CASE | `MAX_RETRIES` | PSR-1 |
| Constantes globales | UPPER_SNAKE_CASE | `BASE_PATH` | De facto (PSR-1 sólo regula las de clase) |
| Variables | camelCase | `$pageId` | De facto (PSR-1 no lo fija) |
| Controladores | Segmento de URL en PascalCase + `Controller` | `UsersController` (`/users`) | Propio |
| Servicios | Segmento de URL en PascalCase + `Service` | `VisitsService` (`/visits`) | Propio |
| Modelos | PascalCase + `Model` | `BaseModel` | Propio |
| Middleware | PascalCase + `Middleware` | `AuthMiddleware` | Propio |
| Infraestructura (`Core/`) | PascalCase, sin sufijo, una clase por archivo | `Router.php`, `Response.php` | PSR-1 |
| Namespaces | `App\` + carpeta en PascalCase | `App\Controllers` | PSR-4 |
| Archivos de rutas | Segmento de URL (kebab-case) + `.routes.php` | `user-profiles.routes.php` | Propio |
| Indentación | 2 espacios | — | **Desviación de PER-CS** (§13) |
| Etiqueta de cierre `?>` | Se omite en archivos sólo-PHP | — | PER-CS |
| Variables de entorno | UPPER_SNAKE_CASE | `API_BASE_PATH`, `DB_HOST` | POSIX |

La etiqueta `?>` se omite por una razón práctica, no estética: un espacio o salto de línea
sobrante después de ella se envía al cliente antes de los headers y produce
*"headers already sent"*. En una API que devuelve JSON, corrompe la respuesta.

### 5.2 Estructura

```
api/
  Core/             Router, Request, Response, Cors, BaseController, Env, FileUpload, SqlIn
  Core/Exceptions/  ApiException, ValidationException, DatabaseException, NotFoundException
  Controllers/      entrada HTTP: valida el 'type' y delega; sin lógica de negocio
  Services/         lógica de negocio y SQL (AuthService: sesiones, §6.2)
  Models/           BaseModel: conexión PDO y ejecución de consultas preparadas, sin SQL de negocio
  Middleware/       AuthMiddleware (§6.2), etc.
  routes/           un archivo por recurso, <segmento-url>.routes.php
public/             raíz web: la única carpeta que sirve el servidor
  index.php         bootstrap: carga ../vendor/autoload.php, el entorno (§10) y las rutas
  .htaccess         redirige a index.php toda petición que no sea un archivo existente, y
                    lleva las cabeceras de seguridad de la API (§5.5)
  multimedia/       archivos públicos subidos; su .htaccess impide ejecutar scripts (§5.4)
tests/              bootstrap.php, Unit/ (sin BD) y Database/ (BD _test), con la estructura de api/ (§5.3)
phpunit.xml         grupos unit y database (§5.3)
database/
  migrations/       migraciones de Phinx (§7.2)
  seeds/            datos iniciales
phinx.php           configuración de migraciones; lee la conexión del entorno (§7.2)
storage/            archivos privados subidos, fuera de la raíz web (§5.4)
composer.json       autoload PSR-4 ("App\\" → "api/") y versión de PHP (require.php, §10)
vendor/             dependencias de composer, junto a composer.json — nunca se edita
.env                configuración local, siempre fuera de git
.env.example        las variables que necesita el proyecto, sin valores reales (§10)
.php-cs-fixer.dist.php  reglas de formato (§14)
.php-version        versión de PHP que corre: local, CI y producción (§10)
.githooks/          hooks de git (§14)
.github/workflows/  ci.yml (§14)
```

Las carpetas que contienen clases van en PascalCase porque PSR-4 las traduce del namespace
tal cual (`App\Controllers\UsersController` → `api/Controllers/UsersController.php`).
Las que no contienen clases (`routes/`, `public/`, `multimedia/`) siguen en minúsculas
(principio 5 del §3).

**Raíz web:** el servidor (`DocumentRoot` en Apache) apunta a `public/`, nunca a la raíz del
repo. Así `.env`, `api/`, `vendor/` y `composer.json` quedan fuera del alcance de una URL
por construcción, sin depender de que el servidor respete un `.htaccess`. El `.git` tampoco se
despliega dentro de la raíz web.

**Backend existente que sirve desde la raíz del repo** (excepción del §13): este `.htaccess` en
la raíz (probado en Apache 2.4: `.git/HEAD` en minúsculas y mayúsculas, `.env`, cualquier
carpeta oculta, `vendor/`, `composer.*` y respaldos `.sql` responden 403; `.well-known/`, las
páginas, la API y las imágenes, 200):
```apache
# Backend existente que sirve desde la raíz del repo (§13). Con public/ como raíz web no hace falta.
Options -Indexes

<IfModule mod_rewrite.c>
  RewriteEngine On
  # Cualquier segmento que empiece con punto (.git/, .env, .claude/…), salvo .well-known/.
  RewriteRule "(^|/)\.(?!well-known/)" - [NC,F,L]
  # Dependencias de Composer, sus metadatos y respaldos.
  RewriteRule "(^|/)vendor/" - [NC,F,L]
  RewriteRule "(^|/)composer\.(json|lock)$" - [NC,F,L]
  RewriteRule "\.(sql|gz|zip|bak|log|ini)$" - [NC,F,L]
</IfModule>
```
- `<FilesMatch "^\.">` **no basta:** sólo evalúa el último segmento de la ruta. Bloquea
  `/.env`, pero deja pasar `/.git/HEAD`.
- `.well-known/` queda abierto porque lo usa Let's Encrypt para emitir los certificados HTTPS.
- `RewriteRule` en un `.htaccess` exige que la carpeta tenga `FollowSymLinks` o
  `SymLinksIfOwnerMatch` (XAMPP lo trae). Sin eso, Apache responde 403 a todo el sitio.

**Archivos subidos:** los públicos en `public/multimedia/`, que nunca ejecuta scripts; los
privados en `storage/`, fuera de la raíz web. Las reglas completas están en §5.4.

**Regla de capas:** `routes → Controller → Service → BaseModel → BD`.

- El **Controller** recibe HTTP, valida el `type` y delega. Nunca escribe SQL.
- El **Service** tiene las reglas de negocio y el SQL del recurso. Nunca imprime HTTP.
- El **BaseModel** sólo conecta y ejecuta lo que el Service le pasa. No conoce ningún recurso.

### 5.3 Tests

PHPUnit, en la versión más reciente compatible con el `.php-version` del proyecto (§10).
Fuente: `phpunit.de/supported-versions.html`. La tabla es la de octubre de 2026; si ya no
coincide con la fuente, manda la fuente.

| PHP | PHPUnit |
|---|---|
| 8.4 o superior | 13 |
| 8.3 | 12 |
| 8.2 | 11 (ya sin correcciones) |
| 8.1 | 10 (ya sin correcciones) |

- **Ubicación:** `tests/` en la raíz, en dos grupos con la estructura de `api/`:
  `tests/Unit/` (sin BD) y `tests/Database/` (con BD). El archivo se llama como la clase +
  `Test.php`, sufijo que exige PHPUnit: `api/Services/UsersService.php` →
  `tests/Unit/Services/UsersServiceTest.php`. Namespace `App\Tests\…`, declarado en
  `composer.json`: `"autoload-dev": { "psr-4": { "App\\Tests\\": "tests/" } }`.
- **Métodos en inglés, descripción en español** con `#[TestDox]` (principio 8 del §3):
  ```php
  #[TestDox('devuelve ok para el código 200')]
  public function testGetStatusReturnsOkFor200(): void
  ```
- **`phpunit.xml`** en la raíz (probado con PHPUnit 10.5 y PHP 8.1):
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <phpunit bootstrap="tests/bootstrap.php" colors="true">
    <testsuites>
      <testsuite name="unit">
        <directory>tests/Unit</directory>
      </testsuite>
      <testsuite name="database">
        <directory>tests/Database</directory>
      </testsuite>
    </testsuites>
    <php>
      <env name="DB_NAME" value="app_test" force="true"/>
    </php>
  </phpunit>
  ```
  `app_test` se cambia por `<proyecto>_test`, el mismo nombre que usa la CI (§14).
  `force="true"` hace que valga aunque el entorno del equipo apunte a otra BD.
- **`tests/bootstrap.php`** carga el `.env` local (§10), sin sobrescribir el `DB_NAME` que fija
  `phpunit.xml` (probado: los tests usan la BD `_test` aunque el `.env` apunte a la de desarrollo,
  y en la CI, sin `.env`, toman las variables del entorno):
  ```php
  <?php

  declare(strict_types=1);

  use App\Core\Env;

  // Carga el .env local sin sobrescribir lo que fija phpunit.xml (DB_NAME de pruebas, §5.3).
  require __DIR__ . '/../vendor/autoload.php';

  Env::load(__DIR__ . '/..');
  ```
- **La BD de pruebas termina en `_test`** y es distinta de la de desarrollo, porque los tests
  borran y crean datos. Los tests de `tests/Database/` extienden esta clase, que se detiene
  si el nombre no termina en `_test`:
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Tests\Database;

  use App\Core\Env;
  use PHPUnit\Framework\TestCase;
  use RuntimeException;

  // Base de los tests que tocan la BD: borran y crean datos, así que sólo corren
  // contra una base de datos de pruebas, cuyo nombre termina en _test.
  abstract class DatabaseTestCase extends TestCase
  {
    public static function setUpBeforeClass(): void
    {
      $database = Env::get('DB_NAME');

      if (!str_ends_with($database, '_test')) {
        throw new RuntimeException("DB_NAME debe terminar en _test; es '{$database}'.");
      }
    }
  }
  ```
  Para que el freno sirva, el BaseModel lee la BD con `Env::get('DB_NAME')` (§7.1), y
  `Env::load()` no sobrescribe una variable que ya existe (§10). La BD de pruebas se migra con
  `DB_NAME=<proyecto>_test vendor/bin/phinx migrate` (probado: la variable de la terminal gana
  sobre el `.env`).
- **SQLite no sirve como sustituto:** el SQL del §7 (`ON DUPLICATE KEY UPDATE`) es de
  MySQL/MariaDB.
- **En la CI corren los dos grupos** (§14), contra un contenedor con el motor de producción
  (§7.1) y después de aplicar las migraciones (§7.2).
- **Matriz de autorización:** cada endpoint protegido tiene tests de las tres respuestas, 401
  sin sesión, 403 con sesión pero sin permiso, y 200 con permiso (§5.5).
- **Sesiones:** tests de cada caso del §6.2 que corta una sesión (cierre, cambio de contraseña,
  usuario desactivado, permiso retirado, inactividad, límite absoluto y reúso del token de
  renovación), con duraciones cortas en el entorno de pruebas para no esperar minutos reales.

### 5.4 Archivos subidos

Un archivo que sube un usuario es entrada no confiable: el servidor decide qué es, cómo se
llama y dónde vive.

- **`multipart/form-data`** (`FormData` en el frontend, `$_FILES` en PHP). Base64 dentro del
  JSON aumenta el tamaño un tercio y carga el archivo entero en memoria: sólo se acepta en
  proyectos existentes que ya lo usan, declarado en su `README.md`.
- **El tipo se verifica sobre el contenido** con `finfo`, contra una lista de tipos permitidos
  por recurso. **La extensión sale del tipo**, nunca del nombre que envía el usuario.
- **Nombre aleatorio:** `bin2hex(random_bytes(16))` + la extensión. Si hace falta el nombre
  original, se guarda en una columna de la BD, nunca en la ruta.
- **Nunca SVG ni HTML** subidos por usuarios: pueden llevar código que corre al abrirlos.
- **Dónde se guardan:**
  - Públicos (los puede ver cualquiera): `public/multimedia/<recurso>/`, con el `.htaccess` de
    abajo.
  - Privados (requieren sesión): `storage/<recurso>/`, fuera de la raíz web, entregados por un
    endpoint que verifica el permiso (`authorize()`, §5.5). Esa respuesta es el archivo con su
    `Content-Type`, no la envoltura JSON del §6. En un backend existente sin `public/`, `storage/`
    lleva un `.htaccess` de una sola línea, `Require all denied`, que se versiona (§11).
- **La BD guarda sólo la ruta que genera el servidor,** relativa a la carpeta base
  (`applications/7bf34498f38250c2aa37dc6663765354.pdf`). Si una edición no trae archivo
  nuevo, se conserva la ruta que ya está en la BD: nunca se acepta una ruta enviada por el
  cliente.
- **Tamaño:** límite por recurso en el Service, menor que `upload_max_filesize` y
  `post_max_size` de PHP. Si la petición supera `post_max_size`, PHP descarta el archivo sin
  avisar: el Core lo detecta y responde 413 (abajo).
- **Los archivos subidos no se versionan** (§11): son datos, como los registros de la BD, y se
  respaldan junto con ella.
- **`Core/FileUpload.php`** (probado con PHP 8.1: archivo válido, tipo no permitido, imagen con
  nombre `.pdf` guardada como `.png`, tamaño excedido, petición sin archivo, y borrado que no
  sale de la carpeta). La firma de `ValidationException` es la del Core del proyecto.
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Core;

  use App\Core\Exceptions\ValidationException;
  use finfo;
  use RuntimeException;

  // Guarda archivos subidos con multipart/form-data. El servidor decide el tipo (por el
  // contenido), la extensión y el nombre; devuelve la ruta relativa, lo único que se guarda
  // en la BD (§5.4).
  final class FileUpload
  {
    private const EXTENSIONS = [
      'application/pdf' => 'pdf',
      'image/jpeg' => 'jpg',
      'image/png' => 'png',
      'image/webp' => 'webp',
    ];

    /** @param list<string> $allowedTypes tipos MIME permitidos para este recurso */
    public static function save(string $field, string $baseDir, string $subdir, array $allowedTypes, int $maxBytes): string
    {
      $file = $_FILES[$field] ?? null;

      if ($file === null || $file['error'] !== UPLOAD_ERR_OK || !is_uploaded_file($file['tmp_name'])) {
        throw new ValidationException([$field => 'Adjunta un archivo.']);
      }
      if ($file['size'] > $maxBytes) {
        throw new ValidationException([$field => 'El archivo supera el tamaño máximo.']);
      }

      $type = (new finfo(FILEINFO_MIME_TYPE))->file($file['tmp_name']);
      if (!in_array($type, $allowedTypes, true) || !isset(self::EXTENSIONS[$type])) {
        throw new ValidationException([$field => 'Tipo de archivo no permitido.']);
      }

      $dir = $baseDir . '/' . $subdir;
      if (!is_dir($dir) && !mkdir($dir, 0775, true) && !is_dir($dir)) {
        throw new RuntimeException("No se pudo crear {$dir}.");
      }

      $name = bin2hex(random_bytes(16)) . '.' . self::EXTENSIONS[$type];
      if (!move_uploaded_file($file['tmp_name'], $dir . '/' . $name)) {
        throw new RuntimeException('No se pudo guardar el archivo.');
      }

      return $subdir . '/' . $name;
    }

    // Borra un archivo por su ruta relativa sólo si, resuelta, queda dentro de $baseDir.
    public static function delete(?string $relativePath, string $baseDir): void
    {
      if ($relativePath === null || trim($relativePath) === '') {
        return;
      }

      $dir = realpath($baseDir);
      $full = $dir !== false ? realpath($dir . DIRECTORY_SEPARATOR . ltrim($relativePath, '/\\')) : false;
      if ($dir === false || $full === false || strncmp($full, $dir . DIRECTORY_SEPARATOR, strlen($dir) + 1) !== 0) {
        return;
      }

      if (is_file($full)) {
        unlink($full);
      }
    }
  }
  ```
  Uso en un Service, con la carpeta y los tipos fijos en el código (`$subdir` nunca sale de la
  petición). `MULTIMEDIA_DIR` es una constante del bootstrap con la ruta absoluta de
  `public/multimedia`:
  ```php
  $path = FileUpload::save('pdf', MULTIMEDIA_DIR, 'applications', ['application/pdf'], 10 * 1024 * 1024);
  ```
- **Petición mayor que `post_max_size`**, en el Core antes del router (probado; sólo aplica a
  `multipart`, porque PHP tampoco llena `$_POST` con una petición JSON normal):
  ```php
  $isMultipart = str_starts_with($_SERVER['CONTENT_TYPE'] ?? '', 'multipart/form-data');
  if ($isMultipart && $_POST === [] && $_FILES === [] && (int) ($_SERVER['CONTENT_LENGTH'] ?? 0) > 0) {
    // → 413 'El archivo es demasiado grande.'
  }
  ```
- **`.htaccess` de `multimedia/`** (probado en Apache 2.4 con PHP 8.1: `.php`, `.phtml` y `.PHP`
  responden 403, el listado de la carpeta 403 y una imagen 200; y con sólo `engine off`, un
  `.php` no se ejecuta):
  ```apache
  # Archivos subidos: nunca se ejecutan ni se listan (§5.4).
  Options -Indexes

  <FilesMatch "(?i)\.(php[0-9]?|phtml|phar|pl|py|cgi|sh|asp|aspx|exe)$">
    Require all denied
  </FilesMatch>

  <IfModule mod_php.c>
    php_flag engine off
  </IfModule>
  ```
  Son dos capas: `FilesMatch` niega las extensiones ejecutables, y `php_flag engine off` apaga
  PHP en la carpeta aunque alguna extensión se escapara de la lista.
- **`multimedia/` hereda las cabeceras de `public/.htaccess`** (§5.5; probado en Apache 2.4).
  `nosniff` y la CSP protegen los archivos subidos; `Cache-Control: no-store` impide guardarlos
  en caché, y `X-Frame-Options: DENY` impide mostrar un PDF dentro de un `<iframe>`. No se
  relajan por defecto: un proyecto que necesite caché o el `<iframe>` lo cambia en el
  `.htaccess` de `multimedia/` y lo declara en su `README.md`.

### 5.5 Seguridad del backend

Cada regla corresponde a un fallo que se ha encontrado en auditorías de seguridad reales.

- **Cada endpoint declara su acceso, de forma explícita,** con los métodos del `AuthMiddleware`
  (§6.2):
  - Público a propósito, con un comentario que lo diga.
  - `authenticate()` sólo para acciones sobre la propia cuenta (perfil, cambio de contraseña).
  - `authorize('<permiso>')` para todo lo demás. Un endpoint que sólo verifica que haya
    sesión deja pasar a cualquier usuario autenticado, tenga o no permiso. Los permisos se leen
    de la BD en cada petición, nunca del token: retirar uno surte efecto de inmediato.

  La matriz de autorización se prueba (401, 403 y 200; §5.3).
- **La estructura del SQL nunca sale de la petición:** ni nombres de columna, ni de
  placeholders, ni el `ORDER BY`. Sólo valores, siempre como parámetros. `json_decode()` puede
  entregar un objeto donde se esperaba una lista, y sus claves las elige el cliente: se valida
  la forma antes de usarla. Para `IN (…)`, `Core/SqlIn.php` (probado con PHP 8.1: acepta
  listas de números o textos; rechaza objetos, listas anidadas, listas vacías y valores sueltos):
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Core;

  use App\Core\Exceptions\ValidationException;

  // "IN (?, ?, ?)" para valores que llegan en la petición. Sólo entran valores escalares, como
  // parámetros; la petición nunca aporta texto al SQL (§5.5).
  final class SqlIn
  {
    /** @return array{0: string, 1: list<int|string>} */
    public static function build(mixed $values, string $field): array
    {
      if (!is_array($values) || $values === [] || !array_is_list($values)) {
        throw new ValidationException([$field => 'Debe ser una lista con al menos un valor.']);
      }
      foreach ($values as $value) {
        if (!is_int($value) && !is_string($value)) {
          throw new ValidationException([$field => 'Sólo se aceptan números o textos.']);
        }
      }

      return ['IN (' . implode(', ', array_fill(0, count($values), '?')) . ')', $values];
    }
  }
  ```
  Uso: `[$in, $params] = SqlIn::build($body['state_ids'] ?? null, 'state_ids');` y luego
  `"… WHERE state_id {$in}"` con `$params`: `$in` sólo contiene `IN (?, ?, ?)`.
- **Si el orden lo elige el usuario,** se traduce desde una lista fija. Un valor que no está en
  la lista responde 400, igual que un `type` desconocido (§6); sin el `is_string()`,
  `?sort[]=x` provoca un `TypeError` y un 500 (probado):
  ```php
  $columns = ['name' => 'full_name', 'date' => 'created_at'];
  $sort = $_GET['sort'] ?? 'date';
  if (!is_string($sort) || !isset($columns[$sort])) {
    // → 400 'Orden no válido.'
  }
  $orderBy = $columns[$sort];
  ```
- **Un solo helper por control de seguridad.** Una función de seguridad copiada en dos
  archivos termina corregida en uno y vulnerable en el otro.
- **HTML y URLs que se guardan:** si un campo admite HTML, se sanea al guardar con una lista
  de etiquetas permitidas (por ejemplo, HTML Purifier); si no, se guarda como texto. Un campo
  de URL sólo acepta `http(s)`: `preg_match('#^https?://#i', $url)`. El frontend vuelve a
  sanear al mostrar (§4.3).
- **Contraseñas** (NIST SP 800-63B-4; probado con PHP 8.1):
  - Hash con bcrypt de coste 12, y re-hash transparente al iniciar sesión:
    ```php
    $hash = password_hash($password, PASSWORD_BCRYPT, ['cost' => 12]);

    // Al iniciar sesión, después de un password_verify() exitoso:
    if (password_needs_rehash($hash, PASSWORD_BCRYPT, ['cost' => 12])) {
      // guardar password_hash($password, PASSWORD_BCRYPT, ['cost' => 12])
    }
    ```
  - Mínimo 15 caracteres (NIST, cuando la contraseña es el único factor). Máximo 72 **bytes**:
    bcrypt ignora lo que pasa de ahí, y dos contraseñas que difieren después del byte 72 se
    aceptan como la misma. Con acentos caben menos caracteres (cada uno ocupa 2 bytes). NIST
    recomienda permitir al menos 64; `PASSWORD_ARGON2ID` no tiene ese límite, si el servidor
    lo ofrece.
  - Sin reglas de composición (mayúsculas, símbolos…) ni cambios periódicos obligatorios. El
    cambio se fuerza sólo si hay evidencia de que la contraseña se filtró.
  - Una contraseña temporal generada por el sistema es aleatoria y larga:
    `bin2hex(random_bytes(12))` da 24 caracteres (96 bits).
- **Los secretos fallan cerrados:** sin valor de respaldo (`getenv('JWT_SECRET') ?: 'algo'`
  deja en producción un secreto conocido); se leen con `Env::get()`, y si falta uno la API no
  arranca (§10). Los secretos se comparan con `hash_equals()`, nunca con `===` o `!==`.
- **Logs sin datos sensibles:** nunca contraseñas, tokens ni datos personales. Los saltos de
  línea de un mensaje se reemplazan antes de escribirlo (`str_replace(["\r", "\n"], ' ', $msg)`),
  para que nadie pueda fabricar líneas falsas en el log.
- **Sin endpoints de prueba ni código muerto alcanzable:** lo que no se usa se borra (git lo
  conserva; principio 3).
- **Cabeceras de seguridad de la API** (OWASP REST Security Cheat Sheet), en `public/.htaccess`
  (probado en Apache 2.4: salen también en las respuestas de error, y las hereda `multimedia/`,
  §5.4). En un backend existente sin `public/`, van en el `.htaccess` de la carpeta de la API,
  nunca en el de la raíz si ésta también sirve páginas: la CSP las dejaría en blanco.
  ```apache
  # Cabeceras de seguridad de la API (§5.5, OWASP REST Security Cheat Sheet).
  <IfModule mod_headers.c>
    Header always set Cache-Control "no-store"
    Header always set Content-Security-Policy "default-src 'none'; frame-ancestors 'none'"
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "DENY"
    Header always set Referrer-Policy "no-referrer"
  </IfModule>
  ```
  En producción, servida por HTTPS, se agrega `Strict-Transport-Security "max-age=31536000"`
  (no probado aquí: requiere HTTPS).

---

## 6. Contrato de API

Todas las APIs comparten el mismo contrato. No se inventa uno nuevo por proyecto. Una API
existente que no cumpla alguna regla de esta sección (por ejemplo, `?id=7` en lugar de
`/users/7`) lo declara en su `README.md`.

**Envoltura de respuesta** (`Core/Response.php`): toda respuesta lleva `status`, `success` y
`message`. El éxito agrega `result`; una lista paginada agrega `total`, `page` y `per_page`
(§6.3); un error de validación (422) o un conflicto (409) agrega `errors` con el detalle por
campo, para que el formulario marque el campo:

```json
{ "status": 200, "success": true,  "message": "OK", "total": 25, "page": 1, "per_page": 20, "result": [] }
{ "status": 422, "success": false, "message": "Revisa los datos marcados.", "errors": { "email": "Escribe un correo válido." } }
{ "status": 409, "success": false, "message": "Ya existe una cuenta con este correo.", "errors": { "email": "Ya existe una cuenta con este correo." } }
```

- **`status` es siempre el código HTTP de la respuesta,** y `success` es `true` sólo con un
  código 2xx. Nunca un 200 con `"success": false`: el navegador, axios y la CI leen el código
  HTTP, no el cuerpo.
- **`message` y los valores de `errors` van en español,** redactados para mostrarse al
  usuario tal cual. Las claves de `errors` son los nombres de campo de la petición.
- Campos JSON en **snake_case**. (Decisión propia: no hay estándar único — la guía de Google
  recomienda camelCase, mientras que Stripe y la API v3 de GitHub usan snake_case.
  Se elige snake_case por coherencia con las columnas de la BD.) El frontend los usa tal
  cual (§4.3).
- **Un recurso = una URL base = un archivo de rutas = un controlador = un servicio.**
- **Sub-operaciones por query param `type`**, no por endpoints nuevos:
  `GET /visits?type=summary`. Un `type` no reconocido responde `400`.
- **El nombre del recurso es su segmento de URL.** Entre capas sólo cambia el casing, nunca
  el número ni el idioma:
  `/user-profiles` → `user-profiles.routes.php` → `UserProfilesController` → `UserProfilesService`
  → tabla `user_profiles` (con prefijo si la BD es compartida, §7). Un recurso que no es colección copia igual su segmento:
  `/auth` → `AuthController`; `/curp` → `CURPService` (siglas, principio 7 del §3).
- **URLs de la API en inglés y kebab-case.** La API la consume código, así que sus URLs siguen
  a los identificadores. El español queda para las URLs del frontend, que ve el usuario.
- **Errores** siempre vía excepciones tipadas capturadas en `BaseController::handle()`.
  `ValidationException` y `ApiException` se muestran; `DatabaseException` y `Throwable`
  se registran en log y devuelven mensaje genérico. **Nunca** exponer SQL ni stack traces.
- **Una clave `UNIQUE` repetida responde 409, no 500.** El Service captura el error 1062 del
  motor; así funciona también si dos peticiones llegan a la vez, cosa que una consulta previa
  no cubre. Probado con MariaDB (también con el correo en mayúsculas: la collation del §7.1 no
  las distingue):
  ```php
  try {
    BaseModel::run(
      'INSERT INTO users (email, password_hash) VALUES (?, ?)',
      [$email, password_hash($password, PASSWORD_BCRYPT, ['cost' => 12])],
    );
  } catch (PDOException $e) {
    // 1062: clave UNIQUE repetida. Es un conflicto con un dato que ya existe: 409, no 500.
    if (($e->errorInfo[1] ?? null) === 1062) {
      throw new ApiException('Ya existe una cuenta con este correo.', 409, ['email' => 'Ya existe una cuenta con este correo.']);
    }
    throw $e;
  }
  ```
  `ApiException` lleva el mensaje, el código HTTP y, opcionalmente, `errors`:
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Core\Exceptions;

  use RuntimeException;

  class ApiException extends RuntimeException
  {
    /** @param array<string, string> $errors detalle por campo, como en el 422 (§6) */
    public function __construct(string $message, public readonly int $status, public readonly array $errors = [])
    {
      parent::__construct($message);
    }
  }
  ```
- **SQL siempre preparado** (`?` + arreglo). Nunca concatenar entrada del usuario.
- **`declare(strict_types=1);`** al inicio de cada archivo PHP nuevo.

### 6.1 Recursos, métodos y códigos

- **Colección y elemento:** `/users` es la colección y `/users/{id}`, un elemento. Las dos
  rutas viven en el mismo `users.routes.php`.
- **Métodos:**

  | Método | Uso | Respuesta | Controlador |
  |---|---|---|---|
  | `GET` | Leer la colección o un elemento | 200 | `get()` |
  | `POST` | Crear | 201, con el elemento creado en `result` | `post()` |
  | `PATCH` | Actualizar sólo los campos enviados (el caso normal) | 200 | `patch()` |
  | `PUT` | Reemplazar el elemento completo | 200 | `put()` |
  | `DELETE` | Borrar | 200 con la envoltura (no 204: toda respuesta lleva cuerpo) | `delete()` |

- **Códigos:** `200` OK · `201` creado · `204` sólo en el preflight de CORS (§6.5) ·
  `400` petición mal formada (parámetros inválidos, `type` desconocido) · `401` sin sesión o
  con la sesión vencida · `403` con sesión pero sin permiso · `404` no encontrado ·
  `405` método no permitido · `409` conflicto (una clave `UNIQUE` repetida, como un correo ya
  registrado) · `413` archivo demasiado grande (§5.4) · `422` validación de campos ·
  `429` demasiados intentos (§6.2) · `500` error interno.
- **Un 405 lleva la cabecera `Allow`** con los métodos que sí acepta el recurso
  (`Allow: GET, POST`): la exige la norma HTTP (RFC 9110).
- **Con Bearer (§6.2), el 401 lleva `WWW-Authenticate: Bearer`.** Con cookie no hay un esquema
  registrado para esa cabecera y no se envía (desviación declarada en el §13).

### 6.2 Autenticación

Dos tokens, cada uno en su cookie `httpOnly`:

| Cookie | Qué es | Duración | Viaja a |
|---|---|---|---|
| `access_token` | JWT firmado que identifica al usuario y su sesión | `JWT_TTL`, máximo 15 minutos | Toda la API (`path` `/`) |
| `refresh_token` | Valor aleatorio de un solo uso; la BD guarda sólo su hash | Hasta el límite absoluto de la sesión | Sólo `/auth` (`path` `<API_BASE_PATH>/auth`) |

- **Cookies `httpOnly`** cuando el frontend y la API comparten sitio (el mismo dominio, aunque
  cambien el subdominio o el puerto). JavaScript no puede leerlas, así que un script inyectado
  (XSS) no puede robar la sesión; OWASP desaconseja guardar tokens en `localStorage`. Una API que
  consumen clientes que no son navegador (apps, scripts) usa `Authorization: Bearer <token>` y
  lo declara en su `README.md`.
- **En local, el frontend y la API usan el mismo host:** `localhost` en los dos, no `localhost`
  y `127.0.0.1`. Para el navegador son sitios distintos y no guarda las cookies `SameSite=Strict`
  (probado: el inicio de sesión responde 200, pero la petición siguiente recibe 401).
  `'secure' => true` funciona en `http://localhost`, que los navegadores tratan como contexto
  seguro (probado).
- **Librería:** `firebase/php-jwt` (`composer require firebase/php-jwt`), algoritmo HS256.
  `JWT_SECRET` de al menos 256 bits (§10); la librería rechaza claves más cortas. Se genera con
  `php -r "echo bin2hex(random_bytes(32));"`.
- **Claims:** `sub` (id del usuario), `sid` (id de la sesión), `iss` (`JWT_ISSUER`), `iat` y
  `exp`. Cualquiera puede decodificar un JWT y leer su contenido: nunca lleva datos sensibles, ni
  los permisos, que se leen de la BD (§5.5).
- **Sin `aud`, a propósito:** una API con un solo emisor y un solo consumidor (su frontend) no
  tiene otra audiencia que distinguir, y un valor artificial no cierra ninguna brecha. Es una
  exclusión deliberada, no un pendiente.
- **Duraciones** (§10):
  - `JWT_TTL`: vida del token de acceso, máximo 900 segundos (15 minutos).
  - `SESSION_IDLE_TTL`: inactividad permitida. Sin peticiones en ese tiempo, la sesión se
    cierra (por ejemplo, 420: 7 minutos).
  - `SESSION_MAX_TTL`: límite absoluto desde el inicio de sesión, máximo 28800 (8 horas, una
    jornada). Renovar no lo extiende.
- **El estado de cada sesión vive en `user_sessions`** (migración probada con Phinx). `users`
  lleva además `is_active` y `password_changed_at` (`DATETIME`, con `CURRENT_TIMESTAMP` por
  defecto):
  ```php
  <?php

  declare(strict_types=1);

  use Phinx\Migration\AbstractMigration;

  final class CreateUserSessions extends AbstractMigration
  {
    public function change(): void
    {
      $this->table('user_sessions')
        ->addColumn('user_id', 'integer', ['signed' => false, 'null' => false])
        ->addColumn('refresh_token_hash', 'char', ['limit' => 64, 'null' => false])
        ->addColumn('previous_refresh_token_hash', 'char', ['limit' => 64, 'null' => true])
        ->addColumn('password_changed_at', 'datetime', ['null' => false])
        ->addColumn('created_at', 'datetime', ['default' => 'CURRENT_TIMESTAMP', 'null' => false])
        ->addColumn('refreshed_at', 'datetime', ['default' => 'CURRENT_TIMESTAMP', 'null' => false])
        ->addColumn('last_activity_at', 'datetime', ['default' => 'CURRENT_TIMESTAMP', 'null' => false])
        ->addColumn('expires_at', 'datetime', ['null' => false])
        ->addColumn('revoked_at', 'datetime', ['null' => true])
        ->addIndex(['refresh_token_hash'], ['unique' => true, 'name' => 'uq_user_sessions_refresh_token_hash'])
        ->addIndex(['previous_refresh_token_hash'], ['name' => 'idx_user_sessions_previous_refresh_token_hash'])
        ->addForeignKey('user_id', 'users', 'id', ['delete' => 'CASCADE', 'constraint' => 'fk_user_sessions_users'])
        ->create();
    }
  }
  ```
- **En cada petición protegida,** el `AuthMiddleware` verifica el token y luego la sesión, con
  `AuthService::checkSession()`. Así, cerrar sesión, cambiar la contraseña, desactivar al usuario
  o retirarle un permiso surte efecto en la petición siguiente, no cuando vence el token:
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Middleware;

  use App\Core\Env;
  use App\Core\Exceptions\ApiException;
  use App\Services\AuthService;
  use DomainException;
  use Firebase\JWT\JWT;
  use Firebase\JWT\Key;
  use UnexpectedValueException;

  final class AuthMiddleware
  {
    /** @return array{user_id: int, session_id: int} */
    public static function authenticate(): array
    {
      try {
        $claims = JWT::decode($_COOKIE['access_token'] ?? '', new Key(Env::get('JWT_SECRET'), 'HS256'));
      } catch (UnexpectedValueException|DomainException $e) {
        throw new ApiException('Inicia sesión para continuar.', 401);
      }
      // Sin aud a propósito: un solo emisor y un solo consumidor (§6.2).
      if (!isset($claims->exp, $claims->sub, $claims->sid) || ($claims->iss ?? null) !== Env::get('JWT_ISSUER')) {
        throw new ApiException('Inicia sesión para continuar.', 401);
      }

      AuthService::checkSession((int) $claims->sub, (int) $claims->sid);

      return ['user_id' => (int) $claims->sub, 'session_id' => (int) $claims->sid];
    }

    // Los permisos se leen de la BD en cada petición: retirar uno surte efecto de inmediato.
    // Sesión válida y, además, el permiso que exige el endpoint (§5.5).
    public static function authorize(string $permission): array
    {
      $session = self::authenticate();

      AuthService::checkPermission($session['user_id'], $permission);

      return $session;
    }
  }
  ```
- **`Services/AuthService.php`** concentra el resto:
  - **Inicio de sesión:** crea la sesión y entrega las dos cookies. El límite de intentos (abajo)
    va antes de verificar la contraseña.
  - **Revisión de cada petición:** una sola consulta comprueba que la sesión no esté cerrada,
    vencida ni inactiva, que el usuario siga activo y que su contraseña no haya cambiado desde
    que la abrió; si todo está bien, registra la actividad.
  - **Renovación:** el token de renovación es de un solo uso; cada renovación lo cambia. El
    anterior se acepta sólo durante 10 segundos, para que dos pestañas que renuevan a la vez no
    se cierren la sesión una a la otra. Pasado ese margen, si el anterior vuelve a aparecer es
    que alguien más lo tiene, y la sesión se cierra.
  - **Cierre:** revoca la sesión y borra las dos cookies.
  - **Cambio de contraseña:** actualiza `password_changed_at`; las demás sesiones dejan de
    coincidir y terminan, y la actual toma la nueva fecha. Todo cambio de contraseña (el propio
    y el que hace un administrador) pasa por ese único lugar.

  Probado contra MariaDB con 35 casos: cookies y su `path`; dos peticiones en el mismo segundo;
  permiso retirado (403 inmediato); vencimiento del token de acceso y renovación; rotación; dos
  pestañas dentro del margen; reúso detectado; cierre de sesión con tokens copiados antes del
  cierre; cambio de contraseña con otra sesión abierta; usuario desactivado; inactividad; uso
  continuo más allá del límite de inactividad, y límite absoluto. Cada prueba falla si se quita
  la regla que cubre. En el navegador: cookies que JavaScript no ve, renovación y reintento ante
  un 401, cierre por inactividad, y cookies borradas al cerrar sesión.
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Services;

  use App\Core\Env;
  use App\Core\Exceptions\ApiException;
  use App\Models\BaseModel;
  use Firebase\JWT\JWT;

  // Sesiones (§6.2): token de acceso corto (JWT) + token de renovación de un solo uso. El estado
  // de la sesión vive en user_sessions y se revisa en cada petición.
  final class AuthService
  {
    // Margen en el que un token de renovación recién rotado todavía se acepta: dos pestañas
    // que renuevan a la vez no deben cerrarse la sesión una a la otra.
    private const REFRESH_GRACE_SECONDS = 10;

    /** @return array{id: int, email: string} */
    public static function login(string $email, string $password): array
    {
      $user = BaseModel::one('SELECT id, email, password_hash, is_active, password_changed_at FROM users WHERE email = ?', [$email]);

      // Mismo 401 para usuario inexistente, inactivo o contraseña incorrecta (§6.2).
      if ($user === null || !$user['is_active'] || !password_verify($password, $user['password_hash'])) {
        throw new ApiException('Correo o contraseña incorrectos.', 401);
      }

      $refreshToken = bin2hex(random_bytes(32));
      BaseModel::run(
        'INSERT INTO user_sessions (user_id, refresh_token_hash, password_changed_at, expires_at)
         VALUES (?, ?, ?, NOW() + INTERVAL ? SECOND)',
        [$user['id'], hash('sha256', $refreshToken), $user['password_changed_at'], (int) Env::get('SESSION_MAX_TTL')],
      );
      $sessionId = (int) BaseModel::pdo()->lastInsertId();

      self::sendCookies((int) $user['id'], $sessionId, $refreshToken);

      return ['id' => (int) $user['id'], 'email' => $user['email']];
    }

    // Revisión de cada petición protegida: la sesión sigue abierta, no está inactiva ni
    // vencida, el usuario sigue activo y su contraseña no cambió desde que se abrió.
    public static function checkSession(int $userId, int $sessionId): void
    {
      $session = BaseModel::one(
        'SELECT s.id FROM user_sessions s JOIN users u ON u.id = s.user_id
         WHERE s.id = ? AND s.user_id = ? AND s.revoked_at IS NULL AND s.expires_at > NOW()
           AND s.last_activity_at > NOW() - INTERVAL ? SECOND
           AND u.is_active = 1 AND u.password_changed_at = s.password_changed_at',
        [$sessionId, $userId, (int) Env::get('SESSION_IDLE_TTL')],
      );
      if ($session === null) {
        throw new ApiException('Tu sesión terminó. Inicia sesión de nuevo.', 401);
      }

      BaseModel::run('UPDATE user_sessions SET last_activity_at = NOW() WHERE id = ?', [$sessionId]);
    }

    // Los permisos se leen de la BD en cada petición: retirar uno surte efecto de inmediato.
    public static function checkPermission(int $userId, string $permission): void
    {
      $granted = BaseModel::one('SELECT 1 FROM user_permissions WHERE user_id = ? AND permission = ?', [$userId, $permission]);
      if ($granted === null) {
        throw new ApiException('No tienes permiso para esta acción.', 403);
      }
    }

    public static function refresh(string $refreshToken): void
    {
      $hash = hash('sha256', $refreshToken);
      $session = BaseModel::one(
        'SELECT s.id, s.user_id, s.refresh_token_hash, s.refreshed_at > NOW() - INTERVAL ? SECOND AS in_grace
         FROM user_sessions s WHERE s.refresh_token_hash = ? OR s.previous_refresh_token_hash = ?',
        [self::REFRESH_GRACE_SECONDS, $hash, $hash],
      );
      if ($session === null) {
        throw new ApiException('Tu sesión terminó. Inicia sesión de nuevo.', 401);
      }

      $userId = (int) $session['user_id'];
      $sessionId = (int) $session['id'];

      if (!hash_equals($session['refresh_token_hash'], $hash)) {
        if (!$session['in_grace']) {
          // Un token ya usado vuelve a aparecer: alguien más lo tiene. Se cierra la sesión.
          BaseModel::run('UPDATE user_sessions SET revoked_at = NOW() WHERE id = ?', [$sessionId]);
          throw new ApiException('Tu sesión terminó. Inicia sesión de nuevo.', 401);
        }
        // Otra pestaña acaba de rotarlo: sólo se entrega un token de acceso nuevo.
        self::checkSession($userId, $sessionId);
        self::sendCookies($userId, $sessionId, null);

        return;
      }

      self::checkSession($userId, $sessionId);

      $newToken = bin2hex(random_bytes(32));
      BaseModel::run(
        'UPDATE user_sessions SET previous_refresh_token_hash = refresh_token_hash, refresh_token_hash = ?, refreshed_at = NOW()
         WHERE id = ?',
        [hash('sha256', $newToken), $sessionId],
      );
      self::sendCookies($userId, $sessionId, $newToken);
    }

    public static function logout(?string $refreshToken): void
    {
      if ($refreshToken !== null && $refreshToken !== '') {
        $hash = hash('sha256', $refreshToken);
        BaseModel::run(
          'UPDATE user_sessions SET revoked_at = NOW() WHERE (refresh_token_hash = ? OR previous_refresh_token_hash = ?) AND revoked_at IS NULL',
          [$hash, $hash],
        );
      }
      self::clearCookies();
    }

    // Cierra las demás sesiones del usuario: dejan de coincidir con password_changed_at.
    public static function changePassword(int $userId, int $sessionId, string $current, string $new): void
    {
      $user = BaseModel::one('SELECT password_hash FROM users WHERE id = ?', [$userId]);
      if ($user === null || !password_verify($current, $user['password_hash'])) {
        throw new ApiException('La contraseña actual no es correcta.', 422);
      }

      BaseModel::run(
        'UPDATE users SET password_hash = ?, password_changed_at = NOW() WHERE id = ?',
        [password_hash($new, PASSWORD_BCRYPT, ['cost' => 12]), $userId],
      );
      // La sesión actual sigue abierta: toma la nueva fecha.
      BaseModel::run(
        'UPDATE user_sessions s JOIN users u ON u.id = s.user_id SET s.password_changed_at = u.password_changed_at WHERE s.id = ?',
        [$sessionId],
      );
    }

    private static function sendCookies(int $userId, int $sessionId, ?string $refreshToken): void
    {
      $now = time();
      $ttl = (int) Env::get('JWT_TTL');
      $accessToken = JWT::encode([
        'sub' => $userId,
        'sid' => $sessionId,
        'iss' => Env::get('JWT_ISSUER'),
        'iat' => $now,
        'exp' => $now + $ttl,
      ], Env::get('JWT_SECRET'), 'HS256');

      setcookie('access_token', $accessToken, self::cookieOptions($now + $ttl, '/'));
      if ($refreshToken !== null) {
        setcookie('refresh_token', $refreshToken, self::cookieOptions($now + (int) Env::get('SESSION_MAX_TTL'), self::refreshPath()));
      }
    }

    private static function clearCookies(): void
    {
      setcookie('access_token', '', self::cookieOptions(1, '/'));
      setcookie('refresh_token', '', self::cookieOptions(1, self::refreshPath()));
    }

    // El token de renovación sólo viaja a /auth: ningún otro endpoint lo recibe.
    private static function refreshPath(): string
    {
      return rtrim(Env::get('API_BASE_PATH'), '/') . '/auth';
    }

    /** @return array<string, mixed> */
    private static function cookieOptions(int $expires, string $path): array
    {
      return ['expires' => $expires, 'path' => $path, 'secure' => true, 'httponly' => true, 'samesite' => 'Strict'];
    }
  }
  ```
- **Límite de intentos de inicio de sesión,** por IP **y** por usuario (por ejemplo, 5 en 15
  minutos), con 429 al superarlo.
- **La misma respuesta para cualquier fallo de inicio de sesión:** usuario inexistente, cuenta
  inactiva o contraseña incorrecta dan el mismo 401 y el mismo mensaje, y todos cuentan como
  intento. Un mensaje distinto revela qué cuentas existen.
- **En el frontend,** la renovación ante un 401 va en `src/plugins/axios.js` y el cierre por
  inactividad en `useIdleSession` (§4.3).

### 6.3 Paginación

- **`?page=1&per_page=20`:** `page` desde 1, y `per_page` entre 1 y 100 (20 si no se envía). Un
  valor fuera de rango responde 400:
  ```php
  $page = filter_var($_GET['page'] ?? 1, FILTER_VALIDATE_INT, ['options' => ['min_range' => 1]]);
  $perPage = filter_var($_GET['per_page'] ?? 20, FILTER_VALIDATE_INT, ['options' => ['min_range' => 1, 'max_range' => 100]]);
  // false en cualquiera de los dos → 400
  ```
- **`total` es el número de registros que cumplen el filtro en todas las páginas,** no los de
  la página actual. El frontend calcula las páginas con `Math.ceil(total / per_page)`.
- **SQL** (probado con MariaDB y `EMULATE_PREPARES` en `false`): un `COUNT(*)` con el mismo
  filtro, y la consulta con `LIMIT ? OFFSET ?` y `($page - 1) * $perPage` como desplazamiento.
- **Los catálogos chicos** (estados, tipos) no se paginan: devuelven todo, con `total` igual a
  su conteo y sin `page` ni `per_page`.

### 6.4 Fechas

- **Fecha y hora: ISO 8601 en UTC, con `Z`** (`2026-10-11T22:03:00Z`). La BD las guarda en UTC
  (§7.1) y el Service las formatea en el propio SQL, igual en MySQL que en MariaDB (probado):
  `DATE_FORMAT(created_at, '%Y-%m-%dT%TZ') AS created_at`.
- **Sólo fecha** (nacimiento, vigencia): `YYYY-MM-DD`, sin zona. El frontend la formatea en UTC
  para no mostrar el día anterior (§4.3).
- **Sin la `Z`, la hora se desplaza:** el navegador interpreta `2026-10-11 22:03:00` como hora
  local del equipo.
- **Al recibir,** el backend acepta ISO 8601 con zona (`date.toISOString()` del frontend da
  `…T22:03:00.000Z`) y la convierte a UTC antes de guardarla (probado):
  `(new DateTimeImmutable($input))->setTimezone(new DateTimeZone('UTC'))->format('Y-m-d H:i:s')`.
- **El frontend la muestra en hora de México** con `Intl` (§4.3).

### 6.5 CORS

- **Orígenes permitidos en `CORS_ALLOWED_ORIGINS`** (§10), separados por comas. En la API,
  nunca `*`, que además el navegador rechaza cuando la petición lleva credenciales.
- **`Core/Cors`** (probado: un origen permitido recibe las cabeceras; uno no permitido y una
  petición sin `Origin`, no). `array_filter` quita las entradas vacías: sin él, una coma al final
  de la lista acepta las peticiones sin `Origin` (probado).
  ```php
  $allowed = array_filter(array_map('trim', explode(',', Env::get('CORS_ALLOWED_ORIGINS'))));
  $origin = $_SERVER['HTTP_ORIGIN'] ?? '';

  if (in_array($origin, $allowed, true)) {
    header("Access-Control-Allow-Origin: {$origin}");
    header('Access-Control-Allow-Credentials: true');
    header('Vary: Origin');
  }

  if ($_SERVER['REQUEST_METHOD'] === 'OPTIONS') {
    header('Access-Control-Allow-Methods: GET, POST, PUT, PATCH, DELETE');
    header('Access-Control-Allow-Headers: Content-Type');
    http_response_code(204);
    exit;
  }
  ```
- **El preflight `OPTIONS` responde 204 sin cuerpo.** Con la entrega de un archivo privado
  (§5.4), son las únicas respuestas sin la envoltura del §6.
- **CORS no es control de acceso:** a un origen no permitido el servidor igual responde, sólo
  que sin esas cabeceras, y es el navegador el que bloquea la lectura. Lo que protege los
  datos es la autenticación (§6.2).
- **Archivos públicos** (`public/multimedia/`, §5.4): pueden llevar `*`, porque son de sólo
  lectura y no usan credenciales. Sólo hace falta si el frontend los lee desde otro origen
  con `fetch`, un `canvas` o un visor de PDF; para `<img>` o `<a>` no se necesita. Nunca en
  `storage/` ni en nada que requiera sesión. Se agrega al `.htaccess` de `multimedia/`
  (probado en Apache 2.4: la cabecera sale en los archivos públicos que se entregan, no en
  los bloqueados ni en los privados):
  ```apache
  <IfModule mod_headers.c>
    Header set Access-Control-Allow-Origin "*"
  </IfModule>
  ```

## 7. Base de datos

- Tablas en snake_case plural. Si la base de datos la comparte más de una aplicación, cada
  tabla lleva el prefijo de la suya (`blog_posts`, `shop_orders`); si no, sin prefijo
  (`user_profiles`): un prefijo que llevan todas las tablas no distingue nada (principio 4 del §3).
- PK `id` autoincremental; FK = singular de la tabla, sin prefijo, + `_id`
  (`blog_posts` → `post_id`); `UNIQUE` en las claves naturales.
- Timestamps `created_at` / `updated_at`.
- Catálogo + hechos separados (esquema estrella) cuando haya conteos o series.
- Upserts idempotentes (`INSERT … ON DUPLICATE KEY UPDATE`, con la forma de cada motor,
  §7.1) en lugar de consultar-y-luego-insertar.
- **Nombres de tabla siempre en minúsculas**: MySQL en Windows es insensible a mayúsculas
  y en Linux no. Una tabla `Users` consultada como `users` funciona en Windows y falla en producción.
- **Columnas `NOT NULL` por defecto;** `NULL` sólo cuando la ausencia del dato significa algo.
- **Tipos:** fechas en `DATETIME` y en UTC (§7.1), no `TIMESTAMP`, que convierte según la
  zona de la sesión y en MySQL termina en 2038; dinero en `DECIMAL`, nunca `FLOAT`;
  booleanos `TINYINT(1)` con prefijo `is_` o `has_` (`is_public`); `deleted_at` sólo si la
  tabla necesita borrado lógico.
- **Tablas InnoDB** (claves foráneas y transacciones).
- **Índices y restricciones:** `uq_<tabla>_<columnas>`, `idx_<tabla>_<columnas>` y
  `fk_<tabla>_<tabla_referida>` (`uq_user_profiles_user_id`, `fk_user_profiles_users`).

### 7.1 Motor, charset y conexión

- **Motor:** el de producción (MySQL o MariaDB), en una versión LTS con soporte
  (`endoflife.date/mariadb`, `endoflife.date/mysql`). El mismo motor y la misma serie en
  local, en la CI y en producción (por ejemplo, MariaDB 11.4 o MySQL 8.4: 11.4 y 11.8 son
  series distintas aunque las dos sean "11"); se declara en el `README.md` y en la imagen de la
  CI (§14). Como en el §10, este documento no fija el número.
- **`utf8mb4` + `utf8mb4_unicode_ci`** en la base de datos, las tablas y la conexión. Es la
  collation común a los dos motores: la de MySQL 8 (`utf8mb4_0900_ai_ci`) no existe en
  MariaDB, y un respaldo que la use no se puede importar ahí. Además no distingue acentos
  ni mayúsculas: buscar `jose perez` encuentra "José Pérez".
- **Upsert según el motor.** MariaDB no acepta el alias de MySQL, y MySQL marcó `VALUES()`
  como obsoleto:
  ```sql
  -- MariaDB (probado)
  INSERT INTO users (email, full_name) VALUES (?, ?)
    ON DUPLICATE KEY UPDATE full_name = VALUES(full_name);

  -- MySQL 8.0.19 o posterior (según su manual)
  INSERT INTO users (email, full_name) VALUES (?, ?) AS new
    ON DUPLICATE KEY UPDATE full_name = new.full_name;
  ```
- **`Models/BaseModel.php`:** la conexión y la ejecución de consultas preparadas (probado con
  MariaDB 10.4 y PHP 8.1):
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Models;

  use App\Core\Env;
  use PDO;

  // Conexión y ejecución de consultas preparadas; no conoce ningún recurso (§5.2).
  class BaseModel
  {
    private static ?PDO $pdo = null;

    public static function pdo(): PDO
    {
      if (self::$pdo === null) {
        self::$pdo = new PDO(
          sprintf('mysql:host=%s;port=%s;dbname=%s;charset=utf8mb4', Env::get('DB_HOST'), Env::get('DB_PORT'), Env::get('DB_NAME')),
          Env::get('DB_USER'),
          Env::get('DB_PASS'),
          [
            PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
            PDO::ATTR_EMULATE_PREPARES => false,
            PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
          ],
        );
        self::$pdo->exec("SET time_zone = '+00:00'");
      }

      return self::$pdo;
    }

    /** @param list<mixed> $params */
    public static function one(string $sql, array $params = []): ?array
    {
      $statement = self::pdo()->prepare($sql);
      $statement->execute($params);
      $row = $statement->fetch();

      return $row === false ? null : $row;
    }

    /** @param list<mixed> $params */
    public static function run(string $sql, array $params = []): int
    {
      $statement = self::pdo()->prepare($sql);
      $statement->execute($params);

      return $statement->rowCount();
    }
  }
  ```
  - `charset=utf8mb4` en el DSN: sin él, la conexión usa el charset por defecto del
    servidor, que puede no ser `utf8mb4`.
  - Errores como excepciones: así llegan a `BaseController::handle()` (§6).
  - `EMULATE_PREPARES` en `false`: las consultas preparadas las resuelve el motor, no PHP.
  - Sesión en UTC: un `DATETIME` guarda la hora tal cual, así que la zona de la sesión que
    escribe define qué significa. Fijada en UTC, siempre significa lo mismo.
- **La app nunca se conecta como `root`.** Tiene su propio usuario (`DB_USER`), sólo con
  permisos sobre los datos de su base; las migraciones usan otro (`DB_MIGRATIONS_USER`) que
  además puede cambiar el esquema (probado con MariaDB 10.4: el usuario de la app lee y
  escribe, pero no puede crear ni borrar tablas ni leer otras bases):
  ```sql
  CREATE USER 'app'@'127.0.0.1' IDENTIFIED BY '<contraseña larga>';
  GRANT SELECT, INSERT, UPDATE, DELETE ON app.* TO 'app'@'127.0.0.1';

  CREATE USER 'app_migrations'@'127.0.0.1' IDENTIFIED BY '<otra contraseña larga>';
  GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, ALTER, DROP, INDEX, REFERENCES ON app.* TO 'app_migrations'@'127.0.0.1';
  ```
  - En local, los dos usuarios reciben además los mismos permisos sobre `app_test.*`, la BD de
    los tests (§5.3).
  - El host de la cuenta es el de `DB_HOST`. Con `DB_HOST=127.0.0.1`, una cuenta
    `'app'@'localhost'` falla si el motor tiene `skip-name-resolve` (probado), algo común en
    servidores de producción; `'app'@'127.0.0.1'` funciona con y sin esa opción (probado).
  - `root` tiene contraseña, también en local.
- **El motor sólo escucha en local:** `bind-address=127.0.0.1` en `my.ini` (o `my.cnf`), salvo
  que la BD viva en otro servidor, y entonces sólo para la IP de la app.

### 7.2 Migraciones

- **Phinx**, en versión exacta por ser 0.x (§11): `composer require --dev robmorgan/phinx:0.16.12`
  (la vigente al escribir esto). Las migraciones van en `database/migrations/` y los datos
  iniciales en `database/seeds/`.
- **Una migración aplicada nunca se edita:** todo cambio al esquema es una migración nueva.
  Phinx registra en cada BD cuáles ya se aplicaron (tabla `phinxlog`).
- **Nombres:** `vendor/bin/phinx create CreateUserProfiles` crea
  `20261012044204_create_user_profiles.php`, con la fecha en UTC. El archivo en snake_case
  con una clase en PascalCase es una excepción impuesta por la herramienta (principio 5 del
  §3). La fecha es la identidad y el orden de la migración, no una versión: nunca se
  renombra (principio 3).
- **Cada columna lleva `'null' => false`** salvo que deba aceptar `NULL`: si no se indica,
  Phinx la crea opcional.
- **`phinx.php`** en la raíz (probado con Phinx 0.16: con el `.env` en local, sin él en la CI,
  y deteniéndose con un mensaje claro si falta una variable). Carga el `.env` igual que la API y
  usa el usuario de migraciones, el único que puede cambiar el esquema (§7.1):
  ```php
  <?php

  declare(strict_types=1);

  use App\Core\Env;

  // Configuración de migraciones: lee la conexión del entorno (§10), nunca del repo. Usa el
  // usuario de migraciones, el único que puede cambiar el esquema (§7.1).
  Env::load(__DIR__);

  return [
    'paths' => [
      'migrations' => __DIR__ . '/database/migrations',
      'seeds' => __DIR__ . '/database/seeds',
    ],
    'environments' => [
      'default_migration_table' => 'phinxlog',
      'default_environment' => 'local',
      'local' => [
        'adapter' => 'mysql',
        'host' => Env::get('DB_HOST'),
        'port' => (int) Env::get('DB_PORT'),
        'name' => Env::get('DB_NAME'),
        'user' => Env::get('DB_MIGRATIONS_USER'),
        'pass' => Env::get('DB_MIGRATIONS_PASS'),
        'charset' => 'utf8mb4',
        'collation' => 'utf8mb4_unicode_ci',
      ],
    ],
  ];
  ```
- **Migración de ejemplo** (probada):
  ```php
  <?php

  declare(strict_types=1);

  use Phinx\Migration\AbstractMigration;

  final class CreateUserProfiles extends AbstractMigration
  {
    public function change(): void
    {
      $this->table('user_profiles')
        ->addColumn('user_id', 'integer', ['signed' => false, 'null' => false])
        ->addColumn('display_name', 'string', ['limit' => 120, 'null' => false])
        ->addColumn('is_public', 'boolean', ['default' => false, 'null' => false])
        ->addColumn('created_at', 'datetime', ['default' => 'CURRENT_TIMESTAMP', 'null' => false])
        ->addColumn('updated_at', 'datetime', ['default' => 'CURRENT_TIMESTAMP', 'update' => 'CURRENT_TIMESTAMP', 'null' => false])
        ->addIndex(['user_id'], ['unique' => true, 'name' => 'uq_user_profiles_user_id'])
        ->addForeignKey('user_id', 'users', 'id', ['delete' => 'RESTRICT', 'constraint' => 'fk_user_profiles_users'])
        ->create();
    }
  }
  ```
- `vendor/bin/phinx migrate` aplica las pendientes; `vendor/bin/phinx rollback` revierte la
  última.
- **Proyectos existentes:** al adoptar Phinx, la primera migración reproduce el esquema
  actual (lo que había en archivos `.sql` sueltos pasa ahí) y se marca como aplicada, sin
  ejecutarla, en las BD que ya lo tienen: `vendor/bin/phinx migrate --fake -t <fecha>`.
  Desde ese punto, todo cambio es una migración.

## 8. Estilos (CSS)

- Clases en kebab-case; **BEM** cuando el componente crece: `bloque__elemento--modificador`
  (`.card__title--active`).
- Estilos del componente en su `<style scoped>`; globales sólo en `src/assets/css/`.
- Fuentes autohospedadas en `src/assets/fonts/<familia>/` (`montserrat/`) con archivos kebab-case
  (`montserrat-v31-latin-700.woff2`) y su `@font-face` en un CSS dedicado.
- Sin colores ni tamaños "mágicos" repetidos: variables CSS o el tema de la librería de UI
  (por ejemplo, Vuetify). Los breakpoints son la excepción: son valores fijos del §8.1 y se
  escriben tal cual.

### 8.1 Responsividad

Breakpoints de Bootstrap 5, sin `xxl`. Los nombres son de tamaño y el dispositivo es sólo una
referencia aproximada: un iPad en horizontal (1024px) cae en `lg`, y muchos teléfonos en
horizontal, en `md`.

| Nombre | Referencia | Ancho | Media query (mobile first) |
|---|---|---|---|
| `xs` | Mobile | < 576px | Ninguna: son los estilos base |
| `sm` | Mobile landscape | 576 – 767.98px | `@media (min-width: 576px)` |
| `md` | Tablet | 768 – 991.98px | `@media (min-width: 768px)` |
| `lg` | Desktop | 992 – 1199.98px | `@media (min-width: 992px)` |
| `xl` | Extra Large | ≥ 1200px | `@media (min-width: 1200px)` |

- **Mobile first.** Los estilos sin media query son los de `xs`; cada breakpoint sólo agrega
  cambios hacia arriba con `min-width`, en orden ascendente. Un estilo que aplica de `md` en
  adelante se escribe una vez:
  ```css
  .grid { grid-template-columns: 1fr; }                                      /* xs */
  @media (min-width: 768px)  { .grid { grid-template-columns: 1fr 1fr; } }    /* md y más */
  @media (min-width: 1200px) { .grid { grid-template-columns: repeat(4, 1fr); } }
  ```
- **Rango cerrado sólo para un estilo de un único breakpoint,** con el `.98` de Bootstrap en
  el máximo, que evita huecos con anchos fraccionarios en pantallas de alta densidad:
  `@media (min-width: 768px) and (max-width: 991.98px)` aplica sólo en `md`.
- **Sintaxis clásica** (`min-width` / `max-width`), no la de rangos (`768px <= width`): tiene
  soporte en todos los navegadores y es la que usa Bootstrap.
- **Los valores se escriben tal cual.** Las variables CSS no funcionan dentro de `@media`;
  como los cinco valores están fijados aquí, no cuentan como tamaños mágicos.
- **Etiqueta viewport obligatoria** en `index.html`:
  `<meta name="viewport" content="width=device-width, initial-scale=1">`. Sin ella, el móvil
  simula una pantalla de unos 980px y ningún breakpoint de móvil se activa. Vite y Vue CLI la
  incluyen en su plantilla: no se borra.
- **Vuetify se alinea con estos breakpoints.** Sus valores por defecto son otros (Vuetify 4:
  600, 840, 1145, 1545 y 2138 px; Vuetify 3: 600, 960, 1280, 1920 y 2560 px); sin ajustarlos,
  sus columnas (`cols`, `md`…) y `useDisplay()` cambian en anchos distintos que el CSS.
  - **Vue 3** (Vuetify 3 y 4). Cada valor es donde **empieza** ese tamaño:
    ```js
    createVuetify({ display: { thresholds: { sm: 576, md: 768, lg: 992, xl: 1200 } } })
    ```
    Vuetify tiene además `xxl` (desde 2560 px en Vuetify 3, desde 2138 px en Vuetify 4), que
    esta convención no usa: queda dentro de `xl`, y las columnas `xl` se aplican igual.
  - **Vue 2** (Vuetify 2). Cada valor es donde **termina** ese tamaño, y hace falta
    `scrollBarWidth: 0`:
    ```js
    new Vuetify({ breakpoint: { thresholds: { xs: 576, sm: 768, md: 992, lg: 1200 }, scrollBarWidth: 0 } })
    ```
    Sin `scrollBarWidth: 0`, Vuetify 2 resta 16 px a los límites de `md` y `lg`, y entre 976 y
    991 px, y entre 1184 y 1199 px, dice un tamaño distinto que el CSS.
  - Probado ejecutando el cálculo de breakpoints de Vuetify 2.7, 3.13 y 4.2 en los anchos de
    frontera (575, 576, 767, 768, 975, 976, 991, 992, 1183, 1184, 1199, 1200 px), no en un
    navegador.

## 9. Assets estáticos

- kebab-case, minúsculas, sin espacios ni acentos; `.` sólo para la extensión.
- Si el asset pertenece a un periodo o tiene variantes: `<slug>-<año>-<variante>.<ext>`
  (`annual-report-2026-1.webp`). Si no, sólo el slug (`logo.svg`).
- Imágenes en `.webp` optimizado; versión reducida con sufijo `-min`.
- Nada de carpetas `_sin-usar/`: lo que no se usa se borra (git lo conserva).

## 10. Entorno y configuración

- Frontend: prefijo obligatorio `VITE_` (Vue 3) o `VUE_APP_` (Vue 2). El build sólo expone al
  bundle las variables con ese prefijo; por lo mismo, **ningún secreto** lleva ese prefijo:
  todo lo que entra al bundle lo puede leer cualquier visitante. Con el prefijo del stack:
  - `API_SERVER`: URL base de la API (§4.3).
  - `MULTIMEDIA_URL`: URL base de los archivos públicos subidos (§5.4).
  - `SESSION_IDLE_TTL`: la misma inactividad permitida que en el backend, en segundos (§4.3).
- Backend: `.env` con estas variables, todas obligatorias (`.env.example` las lista sin valores
  reales):
  - `API_BASE_PATH`: ruta de la API en el servidor (`/api`). El token de renovación viaja sólo a
    `<API_BASE_PATH>/auth` (§6.2).
  - `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER` y `DB_PASS`: la conexión de la app (§7.1).
  - `DB_MIGRATIONS_USER` y `DB_MIGRATIONS_PASS`: el usuario que puede cambiar el esquema (§7.2).
  - `JWT_SECRET`: al menos 256 bits, generado con `php -r "echo bin2hex(random_bytes(32));"`
    y distinto en cada entorno (§6.2).
  - `JWT_ISSUER`: el emisor de los tokens, por ejemplo la URL de la API (§6.2).
  - `JWT_TTL`: vida del token de acceso en segundos, máximo 900 (15 minutos).
  - `SESSION_IDLE_TTL`: inactividad permitida en segundos (por ejemplo, 420: 7 minutos).
  - `SESSION_MAX_TTL`: límite absoluto de la sesión en segundos, máximo 28800 (8 horas).
  - `CORS_ALLOWED_ORIGINS`: orígenes del frontend separados por comas (§6.5).
  - `MAIL_*`, si el proyecto envía correo.
- **Backend:** `.env` y sus respaldos (`.env.*.bak`) **fuera de git**, porque tienen secretos;
  se versiona sólo `.env.example`.
- **Frontend:** los archivos de cada modo (`.env.development`, `.env.production`) **se
  versionan**: por la regla de arriba sólo contienen valores públicos, y la CI los necesita
  para compilar. Cualquier otro (`.env`, `.env.local`, `.env.staging`) queda fuera de git
  (§11). Un valor que no se puede publicar no va en el frontend. Un escáner de secretos que
  busca archivos por nombre los reporta; la prueba de que sólo tienen valores públicos es el
  paso de la CI del §14.
- `.env` **nunca dentro de la raíz web** (§5.2): lo que está ahí se puede pedir por URL.
- Ningún secreto ni cadena de conexión en el repositorio.
- **Ninguna credencial de producción en el `.env` local,** ni siquiera comentada: cada entorno
  tiene las suyas.
- **Los secretos fallan cerrados** (§5.5): si falta uno, la API no arranca; nunca usa un valor
  por defecto.
- **PHP y Apache en producción** no revelan errores ni versiones:
  - `php.ini`: `display_errors=Off`, `display_startup_errors=Off`, `log_errors=On`,
    `expose_php=Off`.
  - `httpd.conf`: `ServerTokens Prod` y `ServerSignature Off`.

### Carga del entorno

Toda la configuración del backend se lee con `Core/Env.php`, nunca con `getenv()` o `$_ENV`
directo:

- **Funciona igual en todos los contextos** (probado): la API, `phinx`, PHPUnit con el `DB_NAME`
  forzado y la CI sin `.env`. Con `vlucas/phpdotenv`, `createImmutable()` llena `$_ENV` y
  `$_SERVER`, pero no `getenv()` (probado: devuelve `false`); y en la CI, con el `php.ini` de
  producción, las variables del entorno llegan a `$_SERVER` y a `getenv()`, pero no a `$_ENV`
  (probado). `Env::get()` revisa los tres.
- **Falla cerrado en un solo lugar:** una variable que falta o está vacía detiene la ejecución
  con un mensaje que dice cuál, nunca su valor (probado).
- **El `.env` no sobrescribe lo que ya trae el entorno:** así la CI y `phpunit.xml` mandan sobre
  el archivo (§5.3).
- `composer require vlucas/phpdotenv`.

```php
<?php

declare(strict_types=1);

namespace App\Core;

use Dotenv\Dotenv;
use RuntimeException;

// Único punto de lectura de la configuración (§10). Falla cerrado: una variable que falta o
// está vacía detiene la ejecución; nunca hay valor por defecto.
final class Env
{
  // Carga el .env si existe, sin sobrescribir lo que ya trae el entorno (la CI, phpunit.xml).
  public static function load(string $dir): void
  {
    Dotenv::createImmutable($dir)->safeLoad();
  }

  public static function get(string $name): string
  {
    $value = $_ENV[$name] ?? $_SERVER[$name] ?? getenv($name);

    if (!is_string($value) || $value === '') {
      throw new RuntimeException("Falta la variable de entorno {$name}.");
    }

    return $value;
  }

  // Detiene el arranque si falta alguna: así un error de configuración aparece al desplegar,
  // no en la primera petición que la usa.
  /** @param list<string> $names */
  public static function require(array $names): void
  {
    foreach ($names as $name) {
      self::get($name);
    }
  }
}
```

El bootstrap (`public/index.php`) comprueba al arrancar todas las variables que usa, para que un
error de configuración aparezca al desplegar y no en la primera petición que la necesita:

```php
require __DIR__ . '/../vendor/autoload.php';

Env::load(__DIR__ . '/..');
Env::require([
  'API_BASE_PATH', 'DB_HOST', 'DB_PORT', 'DB_NAME', 'DB_USER', 'DB_PASS',
  'JWT_SECRET', 'JWT_ISSUER', 'JWT_TTL', 'SESSION_IDLE_TTL', 'SESSION_MAX_TTL', 'CORS_ALLOWED_ORIGINS',
]);
```

`phinx.php` (§7.2) y `tests/bootstrap.php` (§5.3) cargan el `.env` con el mismo `Env::load()`.

### Versiones de runtime

Este documento no fija números de versión: caducarían. Fija cómo se eligen.

- **Un proyecto nuevo arranca en la versión de PHP más reciente con soporte activo** que
  esté disponible en producción. Fuente: `php.net/supported-versions.php`. Si producción no
  ofrece ninguna con soporte activo, se usa la más reciente que aún reciba parches de
  seguridad y se declara como riesgo en el `README.md`. **Nunca** se arranca en una versión
  sin soporte.
- **Node: la versión LTS activa** (`nodejs.org/en/about/previous-releases`), y nunca menor
  que la que exijan las herramientas del §14 (`neostandard`: `^22.13.0 || >=24`).
- **La versión se declara en el proyecto,** no aquí, en dos niveles:
  - **La versión que corre** (en local, en la CI y en producción): `.nvmrc` (por ejemplo,
    `24`) y `.php-version` (por ejemplo, `8.4`) en la raíz. La CI la lee de ahí (§14).
  - **El rango de compatibilidad:** `"require": { "php": "^8.4" }` en `composer.json` y
    `"engines": { "node": "^22.13.0 || >=24" }` en `package.json` (el rango de `neostandard`).
- **En local se usa la misma versión menor que en producción.** Desarrollar con una versión
  más nueva permite usar sintaxis que el servidor no entiende.
- **Un proyecto existente en una versión sin parches de seguridad** lo declara en su
  `README.md` como riesgo conocido, y su migración se trata como tarea propia.

## 11. Git — Conventional Commits 1.0.0

Formato: `<tipo>(<ámbito>): <descripción en imperativo>`

Tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`,
`revert`. El hook `commit-msg` lo valida (§14).

- Sí: `feat(auth): agrega recuperación de contraseña` · `fix(map): corrige carga de capas`
- No: `2026-08-17-6PM` · `cambios` · `update`

La fecha ya la guarda git; el mensaje debe decir **qué** cambió. Además, al ser un formato
con especificación pública, permite generar changelogs y versionado semántico automáticos.

- `.gitignore` obligatorio: `node_modules/`, `vendor/`, `dist/`, `.php-cs-fixer.cache`,
  `.phpunit.cache/`, `.phpunit.result.cache`, logs y respaldos. Además:
  - Backend: `.env*` con la excepción `!.env.example`, y los archivos subidos (§5.4):
    `public/multimedia/**` con la excepción `!public/multimedia/.htaccess`, y `storage/**`
    con la excepción `!storage/.htaccess`. Con `storage/` a secas, git tampoco puede versionar
    el `.htaccess` que protege la carpeta en un backend existente (probado).
  - Frontend: rechazo por defecto, con excepciones explícitas (probado: `.env`, `.env.local`,
    `.env.production.local` y `.env.staging` quedan fuera; `.env.development` y
    `.env.production`, dentro):
    ```
    .env
    .env.*
    !.env.development
    !.env.production
    ```
  - Ambos, por patrón y no por nombre (probado: ignora respaldos con cualquier nombre, en
    mayúsculas o comprimidos): `*.sql`, `*.sql.gz`, `*.dump`, `*.pem`, `*.key`, `id_rsa*`. Un
    proyecto existente con migraciones SQL numeradas (excepción al §7.2, declarada en su
    `README.md`) las conserva con una excepción al patrón: `!<carpeta>/[0-9][0-9][0-9]_*.sql`.
- **Si un secreto o un respaldo llegó a git:** se cambian todas las credenciales expuestas,
  se purga del historial (`git filter-repo`) y se verifica con un clon limpio desde GitHub,
  no con la copia local. Borrarlo en un commit nuevo no basta: sigue en el historial.
- **`package-lock.json` y `composer.lock` se versionan:** `npm ci` y `composer install`
  instalan exactamente esas versiones, en local y en la CI.
- **Versiones en `package.json` y `composer.json`:** las bibliotecas con `^`, para recibir
  parches de seguridad al actualizar (el lockfile fija la versión exacta instalada). Toda
  dependencia 0.x va en versión exacta (`neostandard`, §14; Phinx, §7.2): en 0.x, semver no
  promete compatibilidad ni entre parches. `^0.14.0` ya impide pasar a 0.15, pero no de 0.14.0
  a 0.14.1, y los auditores de dependencias marcan `^0.x` como rango inestable.
- **Sin archivos-respaldo en el repo:** `index 2024-03-15.js`, `main v1.js`, `api_legacy/`.
  Un respaldo es código que ya no se ejecuta; para conservarlo se usa un tag o una rama.
  Si las dos versiones se ejecutan durante una migración, ver `legacy/` en §4.3.
- Un renombre actualiza todas sus referencias en el mismo commit.
- Ramas: `main` estable; trabajo en `feat/<tema>`, `fix/<tema>`, `refactor/<tema>`.

## 12. Comentarios y documentación

- Comentarios en español, explicando el **por qué**, no el qué.
- Cada servicio PHP abre con un bloque que documenta su contrato y el archivo del frontend
  que lo consume. Es lo primero que lleva la plantilla `ExamplesService` (§16):
  ```php
  /**
   * /examples — plantilla del contrato de un recurso (§6).
   *
   * GET    /examples                 lista paginada (?page, ?per_page)
   * GET    /examples?type=summary    resumen por estado
   * GET    /examples/{id}            un elemento
   * POST   /examples                 crea (201)
   * PATCH  /examples/{id}            actualiza los campos enviados
   * DELETE /examples/{id}            borra
   *
   * Acceso: authorize('examples.read') para leer y authorize('examples.write') para escribir.
   * Lo consume: src/services/examplesService.js
   */
  ```
- Cada endpoint lleva un comentario con su firma sobre la línea del `$router->add(...)`.
- `README.md` por repo: instalación, ejecución, variables de entorno necesarias, las
  excepciones a esta convención con su motivo ("Cómo se usa este documento", §13) y los
  riesgos conocidos, como una versión de runtime sin soporte activo (§10).

---

## 13. Desviaciones declaradas respecto al estándar

Un estándar sólo sirve si las excepciones son explícitas. Éstas son las nuestras:

| Estándar | Desviación | Motivo |
|---|---|---|
| PER Coding Style | Indentación de **2 espacios** en PHP, no 4 | Coherencia con el frontend (`.editorconfig` usa 2). Una sola configuración de editor para todo el stack. |
| PSR-1 y PSR-4 | Un backend **existente sin namespaces** puede seguir con `require_once` y carpetas en minúsculas hasta migrar; lo declara en su `README.md`. **Todo backend nuevo usa namespaces.** | Migrar a namespaces toca cada archivo del backend: se hace como tarea propia, no de pasada en otro cambio. |
| Google JSON Style Guide | Campos JSON en snake_case, no camelCase | Coherencia con las columnas de la BD. Evita mapeo en el backend y en el frontend: un campo se llama igual en la BD, la API y Vue. |
| RFC 9110 | Un 401 de la sesión por cookie no lleva `WWW-Authenticate` (con Bearer sí, §6.1) | La norma exige un esquema en esa cabecera, y no hay uno registrado para cookies. |
| NIST SP 800-63B-4 | Contraseñas de máximo 72 bytes (§5.5): con acentos, menos de los 64 caracteres que recomienda | Es el límite de bcrypt, disponible en todo PHP. `PASSWORD_ARGON2ID` no lo tiene, pero depende de cómo se compiló PHP en el servidor. |
| Raíz web en `public/` (§5.2) | Un backend **existente** que sirve desde la raíz del repo puede seguir así si usa el `.htaccess` raíz del §5.2 (bloquea cualquier segmento oculto, como `.git/` y `.env`, además de `vendor/`, `composer.*` y respaldos) y pasa la comprobación del §14; lo declara en su `README.md`. **Todo backend nuevo usa `public/`.** | Mover la raíz web cambia la configuración del servidor en cada entorno. Mientras tanto la protección depende del `.htaccess`, y por eso se comprueba. |

## 14. Herramientas que hacen cumplir esto

Un documento no se cumple solo. Lo que un revisor externo comprueba es que el repo
**valide automáticamente** su convención, en dos niveles:

- **Hooks de git** (en el equipo, antes de cada commit): aviso inmediato, el error no llega
  a entrar al historial. Se pueden saltar con `--no-verify`.
- **CI** (en GitHub, después de cada push): la referencia. Corre en Linux, igual para todos,
  y no se puede saltar. En repositorios privados de GitHub Free no hay ramas protegidas: la
  CI marca el commit con ✗ y avisa por correo, pero no impide el merge. Por eso los hooks
  son el primer filtro.

Primero se describen las herramientas de cada lado; los hooks y la CI que las ejecutan van
al final de esta sección.

### Frontend — ESLint

ESLint aplica la guía de Vue completa (prioridades A + B + C) y JavaScript Standard Style.
Los niveles de `eslint-plugin-vue` son `essential` (A), `strongly-recommended` (A+B) y
`recommended` (A+B+C). Las reglas de nombres están en B y C, así que `essential` no basta.
Con `recommended`, ESLint marca por sí solo los nombres de una sola palabra, el casing de
props, el orden de opciones y el casing de los nombres de componente.

El nombre exacto de la configuración depende del stack y de la versión del plugin:

| Stack | Configuración | Dónde |
|---|---|---|
| Vue 3 (plugin actual) | `pluginVue.configs['flat/recommended']` + `neostandard` (ver abajo) | `eslint.config.js` |
| Vue 2 con plugin v8 (Vue CLI) | `'plugin:vue/recommended'` + `'@vue/standard'` | `.eslintrc.js` |
| Vue 2 con plugin actual | `pluginVue.configs['flat/vue2-recommended']` + `neostandard`, igual que Vue 3 (combinación no probada) | `eslint.config.js` |

Cuidado: en el plugin actual, `plugin:vue/recommended` significa **Vue 3**. Un proyecto
Vue 2 que actualice el plugin sin cambiar el nombre empieza a validar reglas de Vue 3.

Un proyecto que esté en un nivel menor sube por pasos: primero `strongly-recommended`,
corrige, y luego `recommended`. Se verifica con `npm run lint`.

En Vue 3 se activan además a mano dos reglas que `recommended` no cubre:
`vue/define-macros-order` obliga a que `defineProps` y `defineEmits` vayan justo después
de los `import`, y `vue/block-order` con `<script>` primero aplica el orden del §4.4
(`recommended` sólo exige `<style>` al final). En Vue 2 la misma regla se configura con
`['template', 'script', 'style']`; con el plugin v8 se llama `vue/component-tags-order`
(probado con eslint-plugin-vue 8.7: marca un `<script>` antes del `<template>`):
`'vue/component-tags-order': ['error', { order: ['template', 'script', 'style'] }]`.
Los pasos 3 a 8 del orden interno (§4.4) **no los valida ninguna regla de ESLint**: se
revisan a mano. Lo mismo ocurre con las constantes (§4.3): Standard acepta tanto
`MAX_LENGTH` como `maxLength` en cualquier lugar y sólo rechaza mezclas como `Max_Value`;
que la mayúscula se use sólo a nivel de módulo y para valores inmutables se revisa a mano.

**Configuración de Vue 3** (probada con ESLint 10, `neostandard` 0.14 y `eslint-plugin-vue` 10):

```js
import { defineConfig } from 'eslint/config'
import { neostandard, resolveIgnoresFromGitignore } from 'neostandard'
import pluginVue from 'eslint-plugin-vue'

export default defineConfig([
  ...neostandard({ files: ['**/*.vue'], ignores: resolveIgnoresFromGitignore() }),
  ...pluginVue.configs['flat/recommended'],
  {
    files: ['**/*.vue'],
    rules: {
      'vue/define-macros-order': 'error',
      'vue/block-order': ['error', { order: ['script', 'template', 'style'] }]
    }
  }
])
```

`package.json` define el script: `"lint": "eslint ."`.

- **`files: ['**/*.vue']` es obligatorio.** Por defecto `neostandard` sólo revisa `.js`/`.ts`:
  sin esa línea, Standard no se aplica a ningún componente y ESLint no avisa.
- **`ignores: resolveIgnoresFromGitignore()`** hace que ESLint ignore lo que ya ignora git
  (`dist/`, `node_modules/`). Sin esto, después de un build `eslint .` revisa el código
  compilado y falla con errores en archivos que nadie escribió.
- **Por qué `neostandard`:** es el sucesor de Standard con flat config y el único compatible
  con ESLint 10. `eslint-config-standard` sólo admite ESLint 8 y `@vue/eslint-config-standard`
  sólo ESLint 9.
- **Versión exacta en `package.json`** (`"neostandard": "0.14.0"`, sin `^`): es 0.x (§11), y
  además decide qué código pasa la revisión. Una actualización silenciosa haría fallar la CI
  sin que el código cambie. Se actualiza a propósito, no de pasada.
- **Requiere Node `^22.13.0 || >=24`.**
- **Difiere de Standard en tres reglas:** `comma-dangle` es advertencia, `dot-notation` está
  desactivada y `no-multi-spaces` tolera comentarios al final de la línea.

### Backend — PHP-CS-Fixer

Todo backend tiene PHP-CS-Fixer como dependencia de desarrollo, con PER Coding Style:

```bash
composer require --dev friendsofphp/php-cs-fixer
```

`.php-cs-fixer.dist.php` en la raíz (probado con PHP-CS-Fixer 3.95 y PHP 8.1):

```php
<?php

$finder = (new PhpCsFixer\Finder())
  ->in(__DIR__);

return (new PhpCsFixer\Config())
  ->setRules(['@PER-CS3x0' => true])
  ->setIndent('  ')
  ->setLineEnding("\n")
  ->setFinder($finder);
```

```bash
vendor/bin/php-cs-fixer check --diff   # sólo revisa: muestra las diferencias, no toca archivos
vendor/bin/php-cs-fixer fix            # corrige los archivos
```

- **`in(__DIR__)` revisa todo el proyecto** y excluye por sí solo `vendor/` y los archivos
  ocultos. Sirve igual con `public/` o sin ella (backends existentes, §13); listar carpetas
  concretas falla si alguna no existe.
- **`check` termina con un código distinto de 0** si algún archivo no cumple: sirve tal cual
  para un hook o una CI.
- **`setIndent('  ')` es la desviación del §13** declarada en la herramienta: los archivos con
  4 espacios se marcan y `fix` los convierte a 2.
- **Reglas fijadas a una versión** (`@PER-CS3x0`, no `@PER-CS`): `@PER-CS` apunta siempre a
  la última versión y una actualización cambiaría las reglas sin aviso. Se cambia a propósito.
- PHP_CodeSniffer no incluye PER Coding Style; por eso la herramienta es PHP-CS-Fixer.

### Backend — archivos sensibles fuera de alcance

En cada entorno (local y producción), pedir por URL los archivos que nunca deben servirse
responde `403` o `404`, nunca `200`:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://<servidor>/<proyecto>/.env
curl -s -o /dev/null -w "%{http_code}\n" https://<servidor>/<proyecto>/composer.json
curl -s -o /dev/null -w "%{http_code}\n" https://<servidor>/<proyecto>/.git/HEAD
curl -s -o /dev/null -w "%{http_code}\n" https://<servidor>/<proyecto>/.GIT/HEAD
```

Se usa `-o /dev/null` para ver sólo el código, sin descargar el contenido. Un `200` en
`.env` significa que las credenciales son públicas: se corrige la raíz web y se cambian
todas las contraseñas del archivo. Un `200` en `.git/HEAD` significa que se puede descargar
el repositorio completo, con su historial: se corrige igual, y se trata todo lo que haya
pasado por git (respaldos, credenciales) como expuesto. La variante en mayúsculas importa en
servidores Linux, que distinguen `.GIT` de `.git`.

### Ambos

`.editorconfig` en la raíz de cada repo, cubriendo también PHP:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
indent_style = space
indent_size = 2
trim_trailing_whitespace = true
insert_final_newline = true
```

`end_of_line = lf` importa: evita que los archivos se marquen como modificados al
alternar entre Windows y el servidor.

`.gitattributes` en la raíz de cada repo:

```ini
* text=auto eol=lf
```

`.editorconfig` controla cómo guarda el editor; `.gitattributes` controla cómo guarda
git. Sin él, git en Windows puede convertir a CRLF aunque el editor use LF.

### Hooks de git

Dos hooks nativos en `.githooks/`, en la raíz del repo, idénticos en todos los repos y sin
dependencias (probados con Git para Windows). Se versionan y se revisan como cualquier otro
código: un escáner de seguridad que reporte `core.hooksPath` se responde mostrando que apunta a
esa carpeta versionada.

`.githooks/pre-commit` ejecuta la revisión de las herramientas que tenga el proyecto, ya sea
que viva en la raíz del repo o en una carpeta de primer nivel (`vue2/`, `api/`):

```sh
#!/bin/sh
# Revisa el estilo del código antes de cada commit (§14). La CI repite la revisión.
# Busca el proyecto en la raíz del repo y en sus carpetas de primer nivel (vue2/, api/).
set -e
for dir in . */; do
  if [ -f "$dir/package.json" ]; then (cd "$dir" && npm run lint --silent); fi
  if [ -f "$dir/composer.json" ]; then (cd "$dir" && vendor/bin/php-cs-fixer check); fi
done
```

Probado con el proyecto en la raíz, en `vue2/` y en `api/`, con el lint pasando y fallando:
bloquea el commit cuando falla. Un hook que sólo buscara en la raíz aceptaría todo en
silencio en un proyecto dentro de una subcarpeta.

`.githooks/commit-msg` valida Conventional Commits (§11) y acepta los mensajes que genera
git (`Merge …`, `Revert "…"`, `fixup!`, `squash!`):

```sh
#!/bin/sh
# Valida el mensaje con Conventional Commits 1.0.0 (§11).
first_line=$(head -n 1 "$1")
case "$first_line" in
  "Merge "*|"Revert \""*|"fixup! "*|"squash! "*) exit 0 ;;
esac
pattern='^(feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert)(\([a-z0-9-]+\))?!?: .+'
if ! printf '%s\n' "$first_line" | grep -Eq "$pattern"; then
  echo "Mensaje de commit inválido (§11): $first_line" >&2
  echo "Formato: <tipo>(<ámbito>): <descripción>  ·  Ejemplo: feat(auth): agrega recuperación de contraseña" >&2
  exit 1
fi
```

**Activación automática.** Git no activa hooks al clonar; lo hace la instalación de
dependencias:

- Frontend, en `package.json`: `"prepare": "git config core.hooksPath .githooks || exit 0"`.
  `npm install` lo ejecuta.
- Backend, en `composer.json`:
  `"scripts": { "post-install-cmd": "git config core.hooksPath .githooks || exit 0" }`.
  `composer install` lo ejecuta.

El `|| exit 0` evita que la instalación falle en una copia sin git, por ejemplo un ZIP: sin
repositorio no hay commits que revisar. Como sí podría ocultar una activación fallida (por
ejemplo, sin `git` en el PATH), se comprueba una vez después de instalar, como pide el
principio 9: `git config core.hooksPath` debe responder `.githooks`.

- **Permiso de ejecución.** Desde Windows los hooks se guardan sin él, y Linux y macOS los
  ignoran. Al crearlos: `git update-index --chmod=+x .githooks/pre-commit .githooks/commit-msg`.
- **`--no-verify` sólo en emergencias.** Salta los hooks, no la CI.
- **Vue 2 (Vue CLI):** `vue-cli-service lint` corrige archivos por defecto y modificaría el
  código a mitad del commit. El script `lint` es `vue-cli-service lint --no-fix`.
- **Backend:** el hook necesita `php` en el PATH del sistema.

### CI (GitHub Actions)

`.github/workflows/ci.yml` en cada repo. Corre en cada push a `main` y en cada pull request,
en Linux: ahí falla, por ejemplo, un import con el casing mal escrito que en Windows
funciona (§2). Los workflows siguen el formato documentado de cada acción, pero todavía no
se han ejecutado en GitHub: se confirman en la primera ejecución.

Frontend:

```yaml
name: ci
on:
  push:
    branches: [main]
  pull_request:
permissions:
  contents: read
jobs:
  frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1   # v7
      - uses: actions/setup-node@949feb2413d6458794dcd2491c4babbbce0c15c1   # v7
        with:
          node-version-file: .nvmrc
          cache: npm
      - run: npm ci
      - run: npm run lint
      - name: .env versionados sólo con variables públicas (§10)
        run: |
          files=$(git ls-files '.env*')
          [ -z "$files" ] && exit 0
          status=0
          grep -HnvE '^[[:space:]]*(#|$)|^(VITE_|VUE_APP_)[A-Z0-9_]+=|^(NODE_ENV|BASE_URL)=' $files || status=$?
          if [ "$status" -eq 0 ]; then
            echo "Un .env versionado define variables que no son públicas (§10)." >&2
            exit 1
          elif [ "$status" -gt 1 ]; then
            echo "No se pudieron revisar los .env versionados (grep terminó con $status)." >&2
            exit 1
          fi
      - run: npm audit --omit=dev --audit-level=high
      - run: npm test
      - run: npm run build
```

Backend:

```yaml
name: ci
on:
  push:
    branches: [main]
  pull_request:
permissions:
  contents: read
jobs:
  backend:
    runs-on: ubuntu-latest
    services:
      db:
        image: mariadb:11.4   # el motor y la serie de producción (§7.1)
        env:
          MARIADB_ROOT_PASSWORD: ci
          MARIADB_DATABASE: app_test
        ports:
          - 3306:3306
        options: >-
          --health-cmd="healthcheck.sh --connect --innodb_initialized"
          --health-interval=5s --health-timeout=5s --health-retries=10
    env:
      API_BASE_PATH: /api
      DB_HOST: 127.0.0.1
      DB_PORT: 3306
      DB_NAME: app_test
      DB_USER: root
      DB_PASS: ci
      DB_MIGRATIONS_USER: root
      DB_MIGRATIONS_PASS: ci
      JWT_ISSUER: http://localhost/api
      JWT_TTL: 5
      SESSION_IDLE_TTL: 8
      SESSION_MAX_TTL: 20
      CORS_ALLOWED_ORIGINS: http://localhost:5173
    steps:
      - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1   # v7
      - uses: shivammathur/setup-php@eb7c497e18156a6bbabfef1d3a82760b9eda3962   # v2; lee la versión de .php-version
      - name: Secreto de JWT sólo para esta ejecución
        run: echo "JWT_SECRET=$(openssl rand -hex 32)" >> "$GITHUB_ENV"
      - run: composer install --no-interaction --no-progress
      - run: composer audit
      - run: vendor/bin/php-cs-fixer check --diff
      - run: vendor/bin/phinx migrate
      - run: vendor/bin/phpunit
```

- **Acciones fijadas por SHA,** con la versión en un comentario. Una etiqueta (`@v7`) la puede
  mover quien controla la acción, y un SHA no. Para actualizar, se cambian juntos el SHA y el
  comentario, con el SHA del commit de la etiqueta:
  `git ls-remote https://github.com/actions/checkout 'refs/tags/v7*'`. En una etiqueta anotada
  se usa la línea que termina en `^{}`.
- **`permissions: contents: read`:** el token del workflow sólo puede leer el repo.
- **Tests:** el frontend corre todos (`npm test`). El backend levanta un contenedor con el
  motor de producción, aplica las migraciones y corre los grupos `unit` y `database` (§5.3).
  `app_test` es el mismo `<proyecto>_test` del `phpunit.xml`.
- **Auditoría de dependencias:** `npm audit --omit=dev --audit-level=high` y `composer audit`
  hacen fallar la CI ante una vulnerabilidad conocida (en npm, sólo las altas de producción;
  probados en local, terminan en 0 sin hallazgos).
- **`.env` del frontend:** el paso revisa sólo los archivos versionados y falla si alguno
  define algo distinto de `VITE_*`, `VUE_APP_*`, `NODE_ENV` o `BASE_URL`. Si `grep` no puede
  revisarlos, también falla (principio 9). Probado: pasa sin `.env` versionados y con variables
  públicas; detiene un `DEPLOY_PUBLIC_PATH`, un nombre con espacios y un archivo versionado que
  falta en disco. La versión anterior de este paso daba "limpio" en los dos últimos casos. Un
  proyecto que necesite otra variable de build la agrega a la expresión y la declara en su
  `README.md`.
- **La BD de la CI** sólo existe mientras corre el workflow: su contraseña (`ci`) no es un
  secreto, y ahí la app y las migraciones usan `root`. Con MySQL, la imagen es
  `mysql:<versión>`, las variables `MYSQL_ROOT_PASSWORD` y `MYSQL_DATABASE`, y la comprobación
  `mysqladmin ping -h 127.0.0.1`.
- **El frontend también compila** (`npm run build`): el lint revisa estilo, el build revisa
  que el proyecto funcione en Linux.
- **La versión sale del proyecto** (`.nvmrc`, `.php-version`; §10), no del workflow.
- **`npm ci` y `composer install` instalan lo que dicen los archivos de bloqueo,** que por
  eso se versionan (§11).
- **Costo:** gratis en repositorios públicos; 2,000 minutos al mes en privados con GitHub
  Free, de sobra para lint y build.
- **No va en la CI** la comprobación del `.env` por URL: necesita el servidor publicado y se
  hace en cada despliegue.

**Proyecto en una subcarpeta del repo** (por ejemplo, el frontend en `vue2/`):

- Los hooks no cambian: `.githooks/` va en la raíz del repo, y el `prepare` del
  `package.json` de `vue2/` los activa igual (probado, también con commits hechos desde la
  subcarpeta).
- En la CI, `defaults.run.working-directory` cubre los pasos `run`, pero no los `uses`: las
  acciones reciben la ruta completa. Los pasos son los mismos del workflow de arriba (no
  probado en GitHub):
  ```yaml
  jobs:
    frontend:
      runs-on: ubuntu-latest
      defaults:
        run:
          working-directory: vue2
      steps:
        - uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1   # v7
        - uses: actions/setup-node@949feb2413d6458794dcd2491c4babbbce0c15c1   # v7
          with:
            node-version-file: vue2/.nvmrc
            cache: npm
            cache-dependency-path: vue2/package-lock.json
        - run: npm ci
        - run: npm run lint
        - name: .env versionados sólo con variables públicas (§10)
          run: |
            files=$(git ls-files '.env*')
            [ -z "$files" ] && exit 0
            status=0
            grep -HnvE '^[[:space:]]*(#|$)|^(VITE_|VUE_APP_)[A-Z0-9_]+=|^(NODE_ENV|BASE_URL)=' $files || status=$?
            if [ "$status" -eq 0 ]; then
              echo "Un .env versionado define variables que no son públicas (§10)." >&2
              exit 1
            elif [ "$status" -gt 1 ]; then
              echo "No se pudieron revisar los .env versionados (grep terminó con $status)." >&2
              exit 1
            fi
        - run: npm audit --omit=dev --audit-level=high
        - run: npm test
        - run: npm run build
  ```
- Para un backend con `composer.json` dentro de `api/`: `working-directory: api`, y
  `setup-php` recibe `php-version-file: api/.php-version`. Sin eso, según su documentación,
  instala la versión estable más reciente y no la del proyecto, sin avisar.

---

## 15. Prohibiciones transversales (resumen)

| No hacer | En su lugar |
|---|---|
| Espacios en nombres de archivo (`main v1.js`) | kebab/camel según el tipo, sin versión |
| Sufijos de versión (`_V3`, `V1`, `v2`) | Una sola versión vigente + git |
| Fecha de edición en el nombre (`index 2024-03-15.js`) | Historial de git |
| Componentes de una sola palabra (`Counter.vue`) | 2+ palabras (Vue-A): `VisitCounter.vue` |
| Sufijo `Comp` en todos los componentes | Esquema `Base` / `The` / prefijo del padre (Vue-B) |
| Prefijo o sufijo `store` dentro de `stores/`, `routes` dentro de `router/` | `notifications.js`, `dashboard.js` |
| `.` como separador de palabras (`user.settings.json`) | `-` o el casing del tipo (`user-settings.json`, `UserSettings.vue`) |
| Doble extensión (`BarChart.js.js`) | Una extensión |
| Acentos, `ñ` o mayúsculas en carpetas, URLs y assets | ASCII minúsculas con guiones (salvo carpetas de clases PSR-4, §5.2) |
| Mezclar idiomas en un identificador | Inglés, salvo nombre oficial del dominio |
| `axios` en una vista o componente | Función en `src/services/` |
| SQL en un controlador | En el `Service` correspondiente |
| Exponer errores internos en la respuesta | Log + mensaje genérico |
| HTTP 200 con `"success": false` | El código HTTP real en `status` (§6) |
| Token de sesión en `localStorage` | Cookie `httpOnly` (§6.2) |
| Token de sesión de horas, sin renovación | Token de acceso de 15 minutos como máximo y renovación de un solo uso (§6.2) |
| Permisos dentro del token | Leídos de la BD en cada petición (§5.5) |
| Cierre de sesión por inactividad sólo en el navegador | También en el servidor, con `SESSION_IDLE_TTL` (§6.2) |
| Leer la configuración con `getenv()` o `$_ENV` directo | `Env::get()` (§10) |
| Una clave `UNIQUE` repetida que termina en 500 | 409 con `errors` (§6) |
| Una acción de la CI fijada por etiqueta (`@v7`) | Fijada por SHA, con la versión en un comentario (§14) |
| Fechas sin zona en la API (`2026-10-11 22:03:00`) | ISO 8601 en UTC con `Z` (§6.4) |
| CORS con `*` en la API | Orígenes en `CORS_ALLOWED_ORIGINS` (§6.5) |
| Proteger con `authenticate()` un endpoint que no es de la propia cuenta | `authorize('<permiso>')` (§5.5) |
| Nombres de columna, placeholders u `ORDER BY` que salen de la petición | Sólo valores como parámetros; `SqlIn` y listas fijas (§5.5) |
| Copiar una función de seguridad en otro archivo | Un solo helper compartido (§5.5) |
| `v-html` o `:href` con datos de la API sin sanear | `safeHtml` y `safeUrl` (§4.3) |
| Un secreto con valor por defecto o comparado con `===` | Falla cerrado; `hash_equals()` (§5.5) |
| La app conectada a la BD como `root` | Usuario propio con privilegios mínimos (§7.1) |
| `<FilesMatch "^\.">` como única protección de `.git` | `.htaccess` raíz del §5.2, o `public/` como raíz web |
| Dependencias por CDN con `@latest`, o source maps en producción | npm con lockfile; builds sin source maps (§4.3) |
| Un control (hook, CI, regla) sin probar con un caso que debe fallar | Principio 9 del §3 |
| Guardar un archivo subido con la extensión o el nombre que envió el usuario | Nombre aleatorio y extensión según el tipo real (§5.4) |
| Guardar en la BD una ruta de archivo enviada por el cliente | Sólo la ruta que genera el servidor (§5.4) |
| Aceptar SVG o HTML subidos por usuarios | PDF o imágenes, según la lista del recurso (§5.4) |
| Versionar archivos subidos | `.gitignore` de §11; se respaldan con la BD |
| `?>` al cierre de un archivo PHP | Omitirlo (PER-CS) |
| `.env` del backend, o sus respaldos, en git | `.env.example` (§10) |
| Un secreto en el `.env` del frontend | Si no se puede publicar, no va en el frontend (§10) |

## 16. Checklist para un proyecto nuevo

1. Estructura de carpetas del §4.2 / §5.2, aunque algunas queden vacías (con un `.gitkeep`:
   git no guarda carpetas vacías).
2. `.gitignore`, `.gitattributes`, `.editorconfig`, `.env.example`, `README.md` y una copia
   idéntica de `CONVENTIONS-GENERAL.md`.
3. Versiones de runtime (§10): `.nvmrc` / `.php-version` con la versión que corre, y
   `engines.node` / `require.php` con el rango de compatibilidad.
4. Hooks y CI del §14 desde el primer commit: `.githooks/` (ejecutables y activados por
   `prepare` o `post-install-cmd`; `git config core.hooksPath` responde `.githooks`) y
   `.github/workflows/ci.yml`.
5. Frontend: se crea con `npm create vue@latest`, la plantilla oficial, que trae Vite y el
   alias `@`; en sus preguntas se eligen Pinia, Vue Router y Vitest. Componentes con
   Composition API y `<script setup>`, y desde
   el día uno el `eslint.config.js` del §14 tal cual, con `neostandard` en versión exacta y el
   script `"lint": "eslint ."`. Si usa Vuetify, sus breakpoints alineados con el §8.1.
6. Backend: PHP-CS-Fixer con `@PER-CS3x0` (§14), **namespaces PSR-4** y **`public/` como raíz web**
   desde el día uno.
7. Backend: `Core/` completo (`Router`, `Request`, `Response`, `Cors`, `BaseController`, `Env`
   y `Exceptions/`) antes del primer endpoint, con el bootstrap que comprueba las variables
   de entorno (§10).
8. Frontend: `src/plugins/axios.js` (la instancia `api`, con la URL base de `VITE_API_SERVER`,
   §4.3) y `src/services/` antes de la primera llamada.
9. Un `examples.routes.php` + `ExamplesController` + `ExamplesService` como plantilla del contrato.
10. Tests desde el día uno (§4.5, §5.3): la configuración y un primer test que pase, para
    que agregar el siguiente sea copiar uno existente.
11. Backend: base de datos desde el día uno (§7): el `BaseModel` con la conexión del §7.1, y
    `phinx.php` con una primera migración (§7.2).
12. Sesión y CORS antes del primer endpoint protegido: `user_sessions`, `AuthService` y
    `AuthMiddleware` del §6.2, y `Core/Cors` del §6.5, en el backend; la renovación de
    `src/plugins/axios.js` y `useIdleSession` en el layout con sesión, en el frontend (§4.3).
13. Antes del primer endpoint que reciba archivos (§5.4): `Core/FileUpload.php`, la detección
    del 413 en el Core y el `.htaccess` de `public/multimedia/`.
14. Seguridad desde el día uno (§5.5, §7.1, §10): `Core/SqlIn.php`, las cabeceras de la API,
    usuarios de BD con privilegios mínimos, la configuración de producción de PHP y Apache, y
    la comprobación por URL del §14 (incluido `.git/HEAD`) en cada entorno.

---

## Historial de versiones

| Versión | Fecha | Cambios |
|---|---|---|
| 3.0.0 | 2026-10-12 | **Mayor:** cambia el modelo de sesión. Token de acceso de 15 minutos como máximo con renovación de un solo uso, ambos en cookies `httpOnly`; estado en `user_sessions`, revisado en cada petición (cierre, cambio de contraseña, usuario desactivado, inactividad del lado del servidor y límite absoluto); permisos leídos de la BD; sin `aud`, a propósito (§6.2). Frontend con renovación ante un 401 y `useIdleSession` (§4.3). `Core/Env.php` para leer la configuración en todos los contextos, con fallo cerrado (§10); `phinx.php` con el usuario de migraciones y sin valores por defecto (§7.2); `tests/bootstrap.php` (§5.3); `BaseModel` completo (§7.1); usuarios de BD por `127.0.0.1`. 409 con `errors` ante una clave `UNIQUE` repetida (§6). `?sort` inválido responde 400 (§5.5). Fecha sola mostrada en UTC (§4.3). Contradicciones corregidas: ejemplo 409/422, respuestas sin envoltura, `authenticate()` en el §15, URL base en el §16, tests de Vue 2 en la CI, prefijo y sufijo `store`, plantilla del §12 y desviaciones faltantes en el §13. Ajustes para las auditorías: acciones de la CI fijadas por SHA y `permissions: contents: read`, `engines` sin rango suelto, hook sin falsos positivos, `.gitignore` del frontend con rechazo por defecto, `storage/**`, CORS con `array_filter`, paso de `.env` de la CI que distingue "sin coincidencias" de "error", y toda dependencia 0.x en versión exacta (§11, §14). Ejemplos genéricos en lugar de nombres de proyectos. |
| 2.8.0 | 2026-10-12 | Lecciones de tres auditorías de seguridad. Principios 9 (un control que no se probó no existe) y 10 (una corrección se cierra en tres pasos) (§3). §5.5 nueva: acceso declarado por endpoint, estructura del SQL fuera de la petición con `SqlIn`, un solo helper por control, HTML y URLs saneados, contraseñas (bcrypt 12, 15 caracteres a 72 bytes), secretos que fallan cerrados, logs sin datos sensibles y cabeceras de la API. `.htaccess` raíz para backends existentes que bloquea `.git/` (§5.2). Endurecimiento de sesiones y código 429 (§6.1, §6.2). Usuarios de BD con privilegios mínimos (§7.1). Configuración de producción (§10). `.gitignore` por patrones y política de versiones (§11). `safeHtml`, sin CDN ni source maps, y CSP (§4.3); tests de saneado en jsdom (§4.5). Comprobación de `.git/HEAD`, auditoría de dependencias y revisión de `.env` en la CI (§14). |
| 2.7.0 | 2026-10-12 | El frontend versiona sus `.env.[modo]` (sólo valores públicos) e ignora `*.local`; el backend sigue igual (§10, §11). Vuetify: valores por defecto de las versiones 3 y 4, ejemplo de Vue 3 probado y ejemplo de Vue 2 con `scrollBarWidth: 0` (§8.1). CORS `*` prohibido en la API y permitido en archivos públicos (§6.5). `pre-commit` que encuentra el proyecto en la raíz o en una carpeta de primer nivel, y CI para proyectos en subcarpeta (§14). |
| 2.6.0 | 2026-10-12 | Archivos subidos (§5.4): `multipart/form-data`; tipo verificado con `finfo` y extensión según el tipo; nombre aleatorio; sin SVG ni HTML; públicos en `public/multimedia/` sin ejecución de scripts y privados en `storage/`; la BD guarda sólo la ruta del servidor; 413 si se supera `post_max_size`; borrado contenido con `realpath()`; los archivos subidos no se versionan (§11). Frontend con `FormData` (§4.3). |
| 2.5.0 | 2026-10-11 | Contrato de API (§6): `status` igual al código HTTP; mensajes en español para el usuario; `/users/{id}`, `PATCH`, 405 con `Allow` y 409 (§6.1); JWT de `firebase/php-jwt` en cookie `httpOnly` (§6.2); paginación con `page`/`per_page` y `total` de todas las páginas (§6.3); fechas ISO 8601 en UTC (§6.4); CORS con orígenes declarados (§6.5). Frontend: instancia `api` en `src/plugins/axios.js` y fechas con `Intl` (§4.3). |
| 2.4.0 | 2026-10-11 | Base de datos (§7): columnas `NOT NULL` por defecto, tipos (`DATETIME` en UTC, `DECIMAL`, booleanos `is_`/`has_`), InnoDB y nombres de índices; motor de producción en versión LTS con soporte, `utf8mb4_unicode_ci`, upsert según el motor y conexión PDO del `BaseModel` (§7.1); migraciones con Phinx (§7.2). La CI corre los tests `database` contra un contenedor del motor de producción (§14). |
| 2.3.0 | 2026-10-11 | Tests: política en el principio 8 del §3 (bug corregido con su test, código nuevo con lógica con tests, sin cobertura obligatoria, no retroactivo); Vitest + `@vue/test-utils` + happy-dom (§4.5); PHPUnit con grupos `unit` y `database` y BD `_test` con freno de seguridad (§5.3); `npm test` y `phpunit --testsuite unit` en la CI (§14). |
| 2.2.0 | 2026-10-11 | Hooks de git (`pre-commit` con lint, `commit-msg` con Conventional Commits) y CI con GitHub Actions (lint, y build en el frontend) (§14). `.nvmrc` y `.php-version` como versión que corre (§10). Tipos de commit `ci` y `revert`; `package-lock.json` y `composer.lock` se versionan (§11). |
| 2.1.0 | 2026-10-11 | Responsividad (§8.1): breakpoints de Bootstrap 5 sin `xxl`, mobile first, etiqueta viewport y alineación de Vuetify. Se precisa qué es una versión mayor y una menor ("Cómo se usa este documento"). |
| 2.0.0 | 2026-10-11 | Documento universal: sin proyectos de origen ni deuda técnica (las excepciones van al `README.md` de cada proyecto). Vue 3 + Vite + Pinia con Composition API y `<script setup>` para proyectos nuevos; Vue 2 queda para existentes (§4.4). Recursos nombrados por su segmento de URL, en plural (§6). Backends nuevos con namespaces PSR-4, carpetas de clases en PascalCase y `public/` como raíz web (§5.2). PER Coding Style con PHP-CS-Fixer en lugar de PSR-12 con PHP_CodeSniffer; `neostandard` para Vue 3 (§14). Datos de la API en snake_case también en el frontend (§4.3). SQL en el Service (§5.2). Versiones de runtime (§10). |
| 1.x | — | Versión sin numerar: incluía la tabla de proyectos y la deuda técnica por proyecto. |
