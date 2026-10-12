# Convenciones generales de desarrollo

**Versión 2.4.0** · 2026-10-11 · historial al final del documento

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
| JavaScript | **JavaScript Standard Style** (Vue 3: vía `neostandard`; Vue 2: vía `@vue/standard`) | De facto | `standardjs.com` · `github.com/neostandard/neostandard` |
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
   y `UsersService` conviven en el mismo código. Si no hay con qué confundirlo, el sufijo es ruido:
   `Comp` (casi todo en Vue es un componente) o `store` dentro de `stores/`.
5. **ASCII, minúsculas y guiones** en carpetas, URLs y assets. Sin espacios, acentos ni `ñ`.
   Los archivos de código siguen el casing de lo que definen (§4.1, §5.1): `UserCard.vue`,
   `UsersController.php`, `visitTracker.js`. También en ASCII, sin espacios ni acentos.
   Única excepción en carpetas: las que contienen clases PHP con namespace van en
   PascalCase (`Controllers/`), porque PSR-4 las exige así (§5.2). Única excepción en
   archivos: las migraciones siguen el formato que impone Phinx (§7.2).
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
| Módulos de estado | camelCase por dominio, sin prefijo `store` | `notifications.js` | Propio |
| Archivos de rutas | camelCase, sin prefijo `routes` | `router/dashboard.js` | Propio |
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
  composables/   lógica compartida entre componentes · en Vue 2: mixins/
  plugins/       inicialización de librerías (vuetify.js, leaflet.js)
  js/            utilidades puras sin dependencia de Vue
  data/          catálogos y constantes estáticas
public/          img/, json/, pdf/   (kebab-case)
tests/           misma estructura que src/; <Nombre>.test.js (§4.5)
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
  nombrada con verbo (`getUsers`, `sendMessage`), con la URL base desde la variable
  de entorno `<PREFIJO>API_SERVER` (§4.4).
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
proyectos con Vite. Aplica a Vue 3; los proyectos Vue 2 (Vue CLI, sin Vite) quedan fuera de
la regla de tests y lo declaran en su `README.md`.

- **Ubicación:** `tests/` en la raíz, con la misma estructura que `src/`. El archivo se llama
  como lo que prueba + `.test.js`: `src/components/UserCard.vue` →
  `tests/components/UserCard.test.js`. Nada de pruebas dentro de `src/` (§4.3).
- **Configuración**, en `vite.config.js` (probada con Vitest 5), junto al alias `@` → `src/`
  que ya trae Vite:
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
- **Componentes:** se prueba su interfaz pública —props, eventos y slots—, no su estado
  interno, y sin snapshots (guía de Vue). **Services:** con `axios` simulado
  (`vi.mock('axios')`).
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
  Core/             Router, Request, Response, Cors, BaseController
  Core/Exceptions/  ApiException, ValidationException, DatabaseException, NotFoundException
  Controllers/      entrada HTTP: valida el 'type' y delega; sin lógica de negocio
  Services/         lógica de negocio y SQL
  Models/           BaseModel: conexión PDO y ejecución de consultas preparadas, sin SQL de negocio
  Middleware/       AuthMiddleware, etc.
  routes/           un archivo por recurso, <segmento-url>.routes.php
public/             raíz web: la única carpeta que sirve el servidor
  index.php         bootstrap: carga ../.env, ../vendor/autoload.php y las rutas
  .htaccess         redirige a index.php toda petición que no sea un archivo existente
  multimedia/       archivos subidos; su .htaccess impide ejecutar PHP
tests/              Unit/ (sin BD) y Database/ (BD _test), con la estructura de api/ (§5.3)
phpunit.xml         grupos unit y database (§5.3)
database/
  migrations/       migraciones de Phinx (§7.2)
  seeds/            datos iniciales
phinx.php           configuración de migraciones; lee la conexión del entorno (§7.2)
composer.json       autoload PSR-4 ("App\\" → "api/") y versión de PHP (require.php, §10)
vendor/             dependencias de composer, junto a composer.json — nunca se edita
.env                configuración local, siempre fuera de git
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
por construcción, sin depender de que el servidor respete un `.htaccess`.

**Archivos subidos:** `public/multimedia/` nunca ejecuta PHP. Si alguien sube `foto.php` y
la carpeta lo ejecuta, puede correr código en el servidor.

**Regla de capas:** `routes → Controller → Service → BaseModel → BD`.

- El **Controller** recibe HTTP, valida el `type` y delega. Nunca escribe SQL.
- El **Service** tiene las reglas de negocio y el SQL del recurso. Nunca imprime HTTP.
- El **BaseModel** sólo conecta y ejecuta lo que el Service le pasa. No conoce ningún recurso.

### 5.3 Tests

PHPUnit, en la versión más reciente compatible con el `.php-version` del proyecto (§10).
Fuente: `phpunit.de/supported-versions.html`.

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
  <phpunit bootstrap="vendor/autoload.php" colors="true">
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
  `app_test` se cambia por `<proyecto>_test`. `force="true"` hace que valga aunque el
  entorno del equipo apunte a otra BD.
- **La BD de pruebas termina en `_test`** y es distinta de la de desarrollo, porque los tests
  borran y crean datos. Los tests de `tests/Database/` extienden esta clase, que se detiene
  si el nombre no termina en `_test`:
  ```php
  <?php

  declare(strict_types=1);

  namespace App\Tests\Database;

  use PHPUnit\Framework\TestCase;
  use RuntimeException;

  // Base de los tests que tocan la BD: borran y crean datos, así que sólo corren
  // contra una base de datos de pruebas, cuyo nombre termina en _test.
  abstract class DatabaseTestCase extends TestCase
  {
    public static function setUpBeforeClass(): void
    {
      $database = (string) getenv('DB_NAME');

      if (!str_ends_with($database, '_test')) {
        throw new RuntimeException("DB_NAME debe terminar en _test; es '{$database}'.");
      }
    }
  }
  ```
  Para que el freno sirva, el BaseModel toma la BD de `DB_NAME` del entorno, y el cargador
  del `.env` no sobrescribe una variable que ya existe.
- **SQLite no sirve como sustituto:** el SQL del §7 (`ON DUPLICATE KEY UPDATE`) es de
  MySQL/MariaDB.
- **En la CI corren los dos grupos** (§14), contra un contenedor con el motor de producción
  (§7.1) y después de aplicar las migraciones (§7.2).

---

## 6. Contrato de API

Todas las APIs comparten el mismo contrato. No se inventa uno nuevo por proyecto.

**Envoltura de respuesta** (`Core/Response.php`): toda respuesta lleva `status`, `success` y
`message`. El éxito agrega `result` (y `total` en listas); un error de validación (422) agrega
`errors` con el detalle por campo:

```json
{ "status": 200, "success": true,  "message": "OK", "total": 3, "result": [] }
{ "status": 422, "success": false, "message": "...", "errors": { "email": "requerido" } }
```

- Campos JSON en **snake_case**. (Decisión propia: no hay estándar único — la guía de Google
  recomienda camelCase, mientras que Stripe y la API v3 de GitHub usan snake_case.
  Se elige snake_case por coherencia con las columnas de la BD.) El frontend los usa tal
  cual (§4.3).
- **Un recurso = un endpoint = un archivo de rutas = un controlador = un servicio.**
- **Sub-operaciones por query param `type`**, no por endpoints nuevos:
  `GET /visits?type=summary`. Un `type` no reconocido responde `400`.
- **Métodos del controlador según verbo HTTP:** `get()`, `post()`, `put()`, `delete()`.
- **El nombre del recurso es su segmento de URL.** Entre capas sólo cambia el casing, nunca
  el número ni el idioma:
  `/user-profiles` → `user-profiles.routes.php` → `UserProfilesController` → `UserProfilesService`
  → tabla `user_profiles` (con prefijo si la BD es compartida, §7). Un recurso que no es colección copia igual su segmento:
  `/auth` → `AuthController`; `/curp` → `CURPService` (siglas, principio 7 del §3).
- **URLs de la API en inglés y kebab-case.** La API la consume código, así que sus URLs siguen
  a los identificadores. El español queda para las URLs del frontend, que ve el usuario.
- **Códigos:** `200` OK · `201` creado · `400` inválida · `401` sin autenticar ·
  `403` sin permiso · `404` no encontrado · `422` validación · `500` error interno.
- **Errores** siempre vía excepciones tipadas capturadas en `BaseController::handle()`.
  `ValidationException` y `ApiException` se muestran; `DatabaseException` y `Throwable`
  se registran en log y devuelven mensaje genérico. **Nunca** exponer SQL ni stack traces.
- **SQL siempre preparado** (`?` + arreglo). Nunca concatenar entrada del usuario.
- **`declare(strict_types=1);`** al inicio de cada servicio y modelo nuevo.

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
  (`endoflife.date/mariadb`, `endoflife.date/mysql`). El mismo motor y versión mayor en
  local, en la CI y en producción; se declara en el `README.md` y en la imagen de la CI
  (§14). Como en el §10, este documento no fija el número.
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
- **Conexión en el `BaseModel`** (probada con MariaDB 10.4 y PHP 8.1):
  ```php
  $pdo = new PDO(
    sprintf('mysql:host=%s;port=%s;dbname=%s;charset=utf8mb4', getenv('DB_HOST'), getenv('DB_PORT'), getenv('DB_NAME')),
    (string) getenv('DB_USER'),
    (string) getenv('DB_PASS'),
    [
      PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
      PDO::ATTR_EMULATE_PREPARES => false,
      PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    ],
  );
  $pdo->exec("SET time_zone = '+00:00'");
  ```
  - `charset=utf8mb4` en el DSN: sin él, la conexión usa el charset por defecto del
    servidor, que puede no ser `utf8mb4`.
  - Errores como excepciones: así llegan a `BaseController::handle()` (§6).
  - `EMULATE_PREPARES` en `false`: las consultas preparadas las resuelve el motor, no PHP.
  - Sesión en UTC: un `DATETIME` guarda la hora tal cual, así que la zona de la sesión que
    escribe define qué significa. Fijada en UTC, siempre significa lo mismo.

### 7.2 Migraciones

- **Phinx** (`composer require --dev robmorgan/phinx`), con las migraciones en
  `database/migrations/` y los datos iniciales en `database/seeds/`.
- **Una migración aplicada nunca se edita:** todo cambio al esquema es una migración nueva.
  Phinx registra en cada BD cuáles ya se aplicaron (tabla `phinxlog`).
- **Nombres:** `vendor/bin/phinx create CreateUserProfiles` crea
  `20261012044204_create_user_profiles.php`, con la fecha en UTC. El archivo en snake_case
  con una clase en PascalCase es una excepción impuesta por la herramienta (principio 5 del
  §3). La fecha es la identidad y el orden de la migración, no una versión: nunca se
  renombra (principio 3).
- **Cada columna lleva `'null' => false`** salvo que deba aceptar `NULL`: si no se indica,
  Phinx la crea opcional.
- **`phinx.php`** en la raíz (probado con Phinx 0.16):
  ```php
  <?php

  // Configuración de migraciones: lee la conexión del entorno (§10), nunca del repo.
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
        'host' => getenv('DB_HOST') ?: '127.0.0.1',
        'port' => (int) (getenv('DB_PORT') ?: 3306),
        'name' => getenv('DB_NAME'),
        'user' => getenv('DB_USER'),
        'pass' => getenv('DB_PASS') ?: '',
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
- **Vuetify se alinea con estos breakpoints.** Sus valores por defecto son otros (en la
  versión actual, 600, 840, 1145, 1545 y 2138px); sin ajustarlos, sus columnas (`cols`, `md`…)
  y `useDisplay()` cambian en anchos distintos que el CSS. En Vue 3 (no probado):
  ```js
  createVuetify({ display: { thresholds: { sm: 576, md: 768, lg: 992, xl: 1200 } } })
  ```
  Vuetify 2 se configura de otra forma: se verifica en cada proyecto.

## 9. Assets estáticos

- kebab-case, minúsculas, sin espacios ni acentos; `.` sólo para la extensión.
- Si el asset pertenece a un periodo o tiene variantes: `<slug>-<año>-<variante>.<ext>`
  (`annual-report-2026-1.webp`). Si no, sólo el slug (`logo.svg`).
- Imágenes en `.webp` optimizado; versión reducida con sufijo `-min`.
- Nada de carpetas `_sin-usar/`: lo que no se usa se borra (git lo conserva).

## 10. Entorno y configuración

- Frontend: prefijo obligatorio `VITE_` (Vue 3) o `VUE_APP_` (Vue 2). El build sólo expone al
  bundle las variables con ese prefijo; por lo mismo, **ningún secreto** lleva ese prefijo:
  todo lo que entra al bundle lo puede leer cualquier visitante.
- Backend: `.env` con `API_BASE_PATH`, `DB_*`, `JWT_*`, `MAIL_*`.
- `.env` y sus respaldos (`.env.*.bak`) **fuera de git**; se versiona sólo `.env.example`.
- `.env` **nunca dentro de la raíz web** (§5.2): lo que está ahí se puede pedir por URL.
- Ningún secreto ni cadena de conexión en el repositorio.

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
    `"engines": { "node": ">=22.13" }` en `package.json`.
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

- `.gitignore` obligatorio: `node_modules/`, `vendor/`, `dist/`, `.env*` con la excepción
  `!.env.example`, `.php-cs-fixer.cache`, `.phpunit.cache/`, `.phpunit.result.cache`, logs,
  respaldos.
- **`package-lock.json` y `composer.lock` se versionan:** `npm ci` y `composer install`
  instalan exactamente esas versiones, en local y en la CI.
- **Sin archivos-respaldo en el repo:** `index 2024-03-15.js`, `main v1.js`, `api_legacy/`.
  Un respaldo es código que ya no se ejecuta; para conservarlo se usa un tag o una rama.
  Si las dos versiones se ejecutan durante una migración, ver `legacy/` en §4.3.
- Un renombre actualiza todas sus referencias en el mismo commit.
- Ramas: `main` estable; trabajo en `feat/<tema>`, `fix/<tema>`, `refactor/<tema>`.

## 12. Comentarios y documentación

- Comentarios en español, explicando el **por qué**, no el qué.
- Cada servicio PHP abre con un bloque que documenta su contrato y el archivo del frontend
  que lo consume (ver la plantilla `ExamplesService` del §16).
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
| Raíz web en `public/` (§5.2) | Un backend **existente** que sirve desde la raíz del repo puede seguir así si un `.htaccess` bloquea los archivos ocultos (`.env`), `api/`, `vendor/` y `composer.*`, y pasa la comprobación del §14; lo declara en su `README.md`. **Todo backend nuevo usa `public/`.** | Mover la raíz web cambia la configuración del servidor en cada entorno. Mientras tanto la protección depende del `.htaccess`, y por eso se comprueba. |

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
`['template', 'script', 'style']`; con el plugin v8 se llama `vue/component-tags-order`.
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
- **Versión exacta en `package.json`** (`"neostandard": "0.14.0"`, sin `^`): al ser 0.x, una
  versión menor puede romper la configuración. Se actualiza a propósito, no de pasada.
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
```

Se usa `-o /dev/null` para ver sólo el código, sin descargar el contenido. Un `200` en
`.env` significa que las credenciales son públicas: se corrige la raíz web y se cambian
todas las contraseñas del archivo.

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

Dos hooks nativos en `.githooks/`, idénticos en todos los repos y sin dependencias
(probados con Git para Windows).

`.githooks/pre-commit` ejecuta la revisión de las herramientas que tenga el repo:

```sh
#!/bin/sh
# Revisa la convención antes de cada commit (§14). La CI repite la revisión.
set -e
if [ -f package.json ]; then npm run lint --silent; fi
if [ -f composer.json ]; then vendor/bin/php-cs-fixer check; fi
```

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

El `|| exit 0` evita que la instalación falle en una copia sin git, por ejemplo un ZIP.

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
jobs:
  frontend:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7
      - uses: actions/setup-node@v7
        with:
          node-version-file: .nvmrc
          cache: npm
      - run: npm ci
      - run: npm run lint
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
jobs:
  backend:
    runs-on: ubuntu-latest
    services:
      db:
        image: mariadb:11.4   # el motor y la versión de producción (§7.1)
        env:
          MARIADB_ROOT_PASSWORD: ci
          MARIADB_DATABASE: app_test
        ports:
          - 3306:3306
        options: >-
          --health-cmd="healthcheck.sh --connect --innodb_initialized"
          --health-interval=5s --health-timeout=5s --health-retries=10
    env:
      DB_HOST: 127.0.0.1
      DB_PORT: 3306
      DB_NAME: app_test
      DB_USER: root
      DB_PASS: ci
    steps:
      - uses: actions/checkout@v7
      - uses: shivammathur/setup-php@v2   # lee la versión de .php-version
      - run: composer install --no-interaction --no-progress
      - run: vendor/bin/php-cs-fixer check --diff
      - run: vendor/bin/phinx migrate
      - run: vendor/bin/phpunit
```

- **Tests:** el frontend corre todos (`npm test`). El backend levanta un contenedor con el
  motor de producción, aplica las migraciones y corre los grupos `unit` y `database` (§5.3).
- **La BD de la CI** sólo existe mientras corre el workflow: su contraseña (`ci`) no es un
  secreto. Con MySQL, la imagen es `mysql:<versión>`, las variables `MYSQL_ROOT_PASSWORD` y
  `MYSQL_DATABASE`, y la comprobación `mysqladmin ping -h 127.0.0.1`.
- **El frontend también compila** (`npm run build`): el lint revisa estilo, el build revisa
  que el proyecto funcione en Linux.
- **La versión sale del proyecto** (`.nvmrc`, `.php-version`; §10), no del workflow.
- **`npm ci` y `composer install` instalan lo que dicen los archivos de bloqueo,** que por
  eso se versionan (§11).
- **Costo:** gratis en repositorios públicos; 2,000 minutos al mes en privados con GitHub
  Free, de sobra para lint y build.
- **No va en la CI** la comprobación del `.env` por URL: necesita el servidor publicado y se
  hace en cada despliegue.

---

## 15. Prohibiciones transversales (resumen)

| No hacer | En su lugar |
|---|---|
| Espacios en nombres de archivo (`main v1.js`) | kebab/camel según el tipo, sin versión |
| Sufijos de versión (`_V3`, `V1`, `v2`) | Una sola versión vigente + git |
| Fecha de edición en el nombre (`index 2024-03-15.js`) | Historial de git |
| Componentes de una sola palabra (`Counter.vue`) | 2+ palabras (Vue-A): `VisitCounter.vue` |
| Sufijo `Comp` en todos los componentes | Esquema `Base` / `The` / prefijo del padre (Vue-B) |
| Prefijo `store` dentro de `stores/`, `routes` dentro de `router/` | `notifications.js`, `dashboard.js` |
| `.` como separador de palabras (`user.settings.json`) | `-` o el casing del tipo (`user-settings.json`, `UserSettings.vue`) |
| Doble extensión (`BarChart.js.js`) | Una extensión |
| Acentos, `ñ` o mayúsculas en carpetas, URLs y assets | ASCII minúsculas con guiones (salvo carpetas de clases PSR-4, §5.2) |
| Mezclar idiomas en un identificador | Inglés, salvo nombre oficial del dominio |
| `axios` en una vista o componente | Función en `src/services/` |
| SQL en un controlador | En el `Service` correspondiente |
| Exponer errores internos en la respuesta | Log + mensaje genérico |
| `?>` al cierre de un archivo PHP | Omitirlo (PER-CS) |
| `.env` o respaldos en git | `.env.example` |

## 16. Checklist para un proyecto nuevo

1. Estructura de carpetas del §4.2 / §5.2, aunque algunas queden vacías (con un `.gitkeep`:
   git no guarda carpetas vacías).
2. `.gitignore`, `.gitattributes`, `.editorconfig`, `.env.example`, `README.md` y una copia
   idéntica de `CONVENTIONS-GENERAL.md`.
3. Versiones de runtime (§10): `.nvmrc` / `.php-version` con la versión que corre, y
   `engines.node` / `require.php` con el rango de compatibilidad.
4. Hooks y CI del §14 desde el primer commit: `.githooks/` (ejecutables y activados por
   `prepare` o `post-install-cmd`) y `.github/workflows/ci.yml`.
5. Frontend: Vue 3 + Vite + Pinia, componentes con Composition API y `<script setup>`, y
   desde el día uno el `eslint.config.js` del §14 tal cual, con `neostandard` en versión
   exacta y el script `"lint": "eslint ."`. Si usa Vuetify, sus breakpoints alineados con
   el §8.1.
6. Backend: PHP-CS-Fixer con `@PER-CS3x0` (§14), **namespaces PSR-4** y **`public/` como raíz web**
   desde el día uno.
7. Backend: `Core/` completo (`Router`, `Request`, `Response`, `Cors`, `BaseController` y
   `Exceptions/`) antes del primer endpoint.
8. Frontend: `src/services/` con la URL base desde `VITE_API_SERVER` antes de la primera llamada.
9. Un `examples.routes.php` + `ExamplesController` + `ExamplesService` como plantilla del contrato.
10. Tests desde el día uno (§4.5, §5.3): la configuración y un primer test que pase, para
    que agregar el siguiente sea copiar uno existente.
11. Backend: base de datos desde el día uno (§7): el `BaseModel` con la conexión del §7.1, y
    `phinx.php` con una primera migración (§7.2).

---

## Historial de versiones

| Versión | Fecha | Cambios |
|---|---|---|
| 2.4.0 | 2026-10-11 | Base de datos (§7): columnas `NOT NULL` por defecto, tipos (`DATETIME` en UTC, `DECIMAL`, booleanos `is_`/`has_`), InnoDB y nombres de índices; motor de producción en versión LTS con soporte, `utf8mb4_unicode_ci`, upsert según el motor y conexión PDO del `BaseModel` (§7.1); migraciones con Phinx (§7.2). La CI corre los tests `database` contra un contenedor del motor de producción (§14). |
| 2.3.0 | 2026-10-11 | Tests: política en el principio 8 del §3 (bug corregido con su test, código nuevo con lógica con tests, sin cobertura obligatoria, no retroactivo); Vitest + `@vue/test-utils` + happy-dom (§4.5); PHPUnit con grupos `unit` y `database` y BD `_test` con freno de seguridad (§5.3); `npm test` y `phpunit --testsuite unit` en la CI (§14). |
| 2.2.0 | 2026-10-11 | Hooks de git (`pre-commit` con lint, `commit-msg` con Conventional Commits) y CI con GitHub Actions (lint, y build en el frontend) (§14). `.nvmrc` y `.php-version` como versión que corre (§10). Tipos de commit `ci` y `revert`; `package-lock.json` y `composer.lock` se versionan (§11). |
| 2.1.0 | 2026-10-11 | Responsividad (§8.1): breakpoints de Bootstrap 5 sin `xxl`, mobile first, etiqueta viewport y alineación de Vuetify. Se precisa qué es una versión mayor y una menor ("Cómo se usa este documento"). |
| 2.0.0 | 2026-10-11 | Documento universal: sin proyectos de origen ni deuda técnica (las excepciones van al `README.md` de cada proyecto). Vue 3 + Vite + Pinia con Composition API y `<script setup>` para proyectos nuevos; Vue 2 queda para existentes (§4.4). Recursos nombrados por su segmento de URL, en plural (§6). Backends nuevos con namespaces PSR-4, carpetas de clases en PascalCase y `public/` como raíz web (§5.2). PER Coding Style con PHP-CS-Fixer en lugar de PSR-12 con PHP_CodeSniffer; `neostandard` para Vue 3 (§14). Datos de la API en snake_case también en el frontend (§4.3). SQL en el Service (§5.2). Versiones de runtime (§10). |
| 1.x | — | Versión sin numerar: incluía la tabla de proyectos y la deuda técnica por proyecto. |
