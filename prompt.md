## Objetivo

Convertir el frame de Figma adjunto (landing page veterinaria) a una aplicación Angular 20 con precisión pixel-perfect.

---

## 1. Análisis del Diseño de Figma

Usar el MCP server `figma-desktop` para extraer:

```bash
get_design_context    # Estructura completa del frame
get_screenshot        # Referencia visual
get_metadata          # Metadatos del diseño
get_variable_defs     # Tokens: colores, tipografía, espaciado
```

**IMPORTANTE**: Figma proporciona nombres de imágenes sin extensión. Cuando extraigas nombres de assets del diseño, debes buscar los archivos reales en las carpetas `public/images/` y `public/svg/` para obtener el nombre completo con extensión.

---

## 2. Aspectos Críticos Pixel-Perfect

### Extraer con Precisión Absoluta

- **Layout**: Dimensiones, padding, margin, grid, breakpoints
- **Tipografía**: Familia (Inter Tight), tamaños, pesos, line-height, letter-spacing
- **Colores**: Hex exactos, gradientes, transparencias, estados hover
- **Imágenes**: Dimensiones, border-radius, box-shadow, aspect-ratio
- **Botones**: Dimensiones, padding, border-radius, estados interactivos

### Elementos Críticos (No Omitir)

1. **Hero Grid**: 4 imágenes con **border-radius diferenciados** (no uniforme)
2. **Badge Hero**: Fondo naranja semi-transparente + borde naranja sólido
3. **Features Strip**: Barra naranja horizontal con iconos de certificado
4. **Ornamentos SVG**: Posicionamiento absoluto con rotaciones específicas. Un mismo SVG puede aparecer múltiples veces con diferentes transformaciones (rotación, escala, color)
5. **Logo**: SVG de patita + texto "Vetcare"

### Assets Disponibles

- Imágenes: `public/images/` → **referenciar EXACTAMENTE como** `images/archivo.jpg`
- SVG: `public/svg/` → **referenciar EXACTAMENTE como** `svg/icono.svg`
- **CRÍTICO - Rutas de Assets:**
  - CORRECTO: `<img src="images/foto.jpg" />` o `<img src="svg/icono.svg" />`
  - INCORRECTO: `<img src="assets/images/foto.jpg" />` o `<img src="assets/svg/icono.svg" />`
  - INCORRECTO: `<img src="/images/foto.jpg" />` o rutas absolutas
  - Las rutas son relativas y NO incluyen "assets" en el path
- **CRÍTICO - NO Inventar Nombres de Archivos:**
  - Figma proporciona nombres de assets sin extensión
  - DEBES buscar el archivo real en las carpetas `public/images/` o `public/svg/`
  - Usar el nombre exacto del archivo que existe en el proyecto
  - Si Figma indica "foto-perro", buscar en `public/images/` el archivo completo (ej: `foto-perro.jpg`, `foto-perro.png`)
  - NO inventar nombres como `image1.jpg`, `placeholder.jpg`, etc.
  - Verificar extensiones reales: `.jpg`, `.png`, `.svg`, etc.
- **Importante SVG dinámicos**:
  - Todos los SVG usan `fill="currentColor"` y `stroke="currentColor"`
  - Esto permite cambiar el color mediante la propiedad CSS `color` del elemento contenedor
  - El mismo archivo SVG puede reutilizarse con diferentes estilos CSS (color, tamaño, rotación, opacidad)
  - Ejemplo: `<img src="svg/icon.svg" class="icon--orange" style="color: #ff6b35; width: 32px; transform: rotate(45deg);" />`

---

## 3. Stack Técnico Angular 20

### Componentes a Crear

7 componentes + app principal:

- `header`, `hero`, `services`, `testimonials`, `pricing`, `team`, `footer`

### Mejores Prácticas

- Usar el MCP server `angular-cli` para aplicar las mejores prácticas de Angular 20
- Para generar componentes usar: `ng g c components/nombre-componente`
- **IMPORTANTE**: Si un componente ya fue creado con `ng g c`, NO cambiar su nombre. Respetar el nombre generado por Angular CLI
- En Angular 20 todos los componentes son standalone por defecto (no agregar `standalone: true`)
- **SCSS Moderno**:
  - Usar `@use` en lugar de `@import` para importar archivos SCSS
  - Crear `src/styles/_variables.scss` con todas las variables extraídas del diseño de Figma (colores, fuentes, tamaños, sombras, espaciados, border-radius, breakpoints)
  - **SIEMPRE usar alias al importar**: `@use 'variables' as v;` y acceder como `v.$variable-name`
  - Aprovechar características de SCSS: variables, mixins, funciones, anidación
  - Metodología BEM para nomenclatura de clases
- Mobile-first
- **PROHIBIDO usar Tailwind u otros frameworks CSS**

---

## 4. Estructura de Archivos SCSS

```
src/
├── styles/
│   └── _variables.scss    ← Variables globales extraídas de Figma
├── styles.scss            ← Archivo principal de estilos globales
└── app/
    └── components/
        └── header/
            ├── header.ts
            ├── header.html
            └── header.scss  ← Importa variables con: @use 'variables' as v;
```

**Reglas SCSS:**

- Variables globales en `src/styles/_variables.scss` usando sintaxis SCSS (`$variable-name`)
- Gracias a `stylePreprocessorOptions.includePaths: ["src/styles"]` en `angular.json`, se pueden importar sin rutas largas
- **SIEMPRE usar alias al importar**: `@use 'variables' as v;` (NO usar `@use 'variables' as *;`)
- Acceder a variables con el alias: `color: v.$primary-color;`
- NO usar `@import`, solo `@use`
- NO usar rutas relativas como `../../styles/variables`

---

## 5. Formato de Output

Generar cada componente con este formato exacto:

````markdown
## COMPONENT: header

### HTML (header.html)

```html
<nav class="header">
  <!-- código -->
</nav>
```

### TypeScript (header.ts)

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-header',
  imports: [],
  templateUrl: './header.html',
  styleUrl: './header.scss',
})
export class HeaderComponent {}
```

### SCSS (header.scss)

```scss
@use 'variables' as v;

.header {
  // estilos usando v.$variable-name
}
```
````

Repetir para: `hero`, `services`, `testimonials`, `pricing`, `team`, `footer`

### Componente Principal

````markdown
## MAIN APP COMPONENT

### HTML (app.html)

```html
<app-header></app-header>
<app-hero></app-hero>
<!-- ... resto -->
```

### TypeScript (app.ts)

```typescript
// imports de todos los componentes
```
````

### Estilos Globales

````markdown
## STYLES VARIABLES (styles/\_variables.scss)

Extraer TODOS los valores del diseño de Figma y crear variables SCSS organizadas, por ejemplo:

```scss
// Colores (extraer de Figma)
$primary-color: #...;
$secondary-color: #...;
$text-color: #...;
// ... todos los colores del diseño

// Tipografía (extraer de Figma)
$font-primary: '...';
$font-secondary: '...';

// Font Weights (extraer de Figma)
$font-regular: ...;
$font-medium: ...;
$font-bold: ...;
// ... más pesos

// Tamaños de fuente y line-height (extraer de Figma)
$h1-size: ...px;
$h1-line-height: ...px;
// ... todos los tamaños

// Espaciados (extraer del diseño)
$spacing-xs: ...px;
$spacing-sm: ...px;
$spacing-md: ...px;
// ... todos los espaciados

// Sombras (extraer de Figma)
$shadow-sm: ...;
$shadow-md: ...;
// ... todas las sombras

// Border Radius (extraer de Figma)
$radius-sm: ...px;
$radius-md: ...px;
// ... todos los radius

// Breakpoints (analizar diseño responsive en Figma)
$breakpoint-mobile: ...px;
$breakpoint-tablet: ...px;
// ... todos los breakpoints
```

## SHARED STYLES (styles.scss)

```scss
// Importar fuentes (usar las fuentes exactas del diseño de Figma)
@import url('...');

// Importar variables con alias
@use 'variables' as v;

// Reset CSS y estilos globales base
// (Extraer y crear según el diseño de Figma)
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: v.$font-primary;
  color: v.$text-color;
  background-color: v.$white;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

// Crear estilos globales base según diseño de Figma...
```
````

**Uso en componentes:**

```scss
@use 'variables' as v;

.component-name {
  color: v.$primary-color;
  font-family: v.$font-primary;
  padding: v.$spacing-md;
  // Usar v.$variable-name
}
```

**IMPORTANTE**:

- NO usar valores de ejemplo
- Extraer TODOS los valores del diseño de Figma
- Usar alias al importar: `@use 'variables' as v;`
- Acceder con prefijo: `v.$variable-name`

---

## 6. Checklist de Validación

- [ ] Border-radius diferenciados en imágenes hero
- [ ] Badge hero con transparencia correcta
- [ ] Features strip naranja implementada
- [ ] Ornamentos SVG con rotaciones y transformaciones
- [ ] Mismo SVG reutilizado con diferentes estilos (color, tamaño, rotación)
- [ ] Tipografía Inter Tight con pesos correctos
- [ ] **Assets con rutas correctas: `images/archivo.jpg` y `svg/icono.svg` (SIN "assets/")**
- [ ] **Nombres de archivos reales de `public/images/` y `public/svg/` (NO inventados)**
- [ ] **Archivo `src/styles/_variables.scss` creado con todas las variables extraídas de Figma**
- [ ] **Uso de `@use 'variables' as v;` con alias (NUNCA `as *`)**
- [ ] **Acceso a variables con alias: `v.$variable-name`**
- [ ] **Todos los valores de estilos extraídos del diseño de Figma (NO valores de ejemplo)**
- [ ] Nombres de componentes respetan los generados por `ng g c`
- [ ] Colores exactos de Figma
- [ ] Espaciado consistente con diseño
- [ ] Sin uso de Tailwind/frameworks CSS
- [ ] Componentes generados con comando `ng g c`

---

**Resumen Final:** Al finalizar la implementación, proporcionar un resumen **sin código**, solo indicando los componentes creados y aspectos clave implementados.
