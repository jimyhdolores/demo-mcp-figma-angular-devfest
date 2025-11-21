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

- Imágenes: `public/images/` → referenciar como `images/archivo.jpg`
- SVG: `public/svg/` → referenciar como `svg/icono.svg`
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
- En Angular 20 todos los componentes son standalone por defecto (no agregar `standalone: true`)
- SCSS con variables + metodología BEM
- Mobile-first + accessibility (ARIA, semantic HTML)
- **PROHIBIDO usar Tailwind u otros frameworks CSS**

---

## 4. Formato de Output

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
.header {
  // estilos
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
## SHARED STYLES (styles.scss)

```scss
:root {
  --primary-color: #...;
  // design tokens
}
```
````

---

## 5. Checklist de Validación

- [ ] Border-radius diferenciados en imágenes hero
- [ ] Badge hero con transparencia correcta
- [ ] Features strip naranja implementada
- [ ] Ornamentos SVG con rotaciones y transformaciones
- [ ] Mismo SVG reutilizado con diferentes estilos (color, tamaño, rotación)
- [ ] Tipografía Inter Tight con pesos correctos
- [ ] Assets desde rutas correctas (`images/`, `svg/`)
- [ ] Colores exactos de Figma
- [ ] Espaciado consistente con diseño
- [ ] Sin uso de Tailwind/frameworks CSS
- [ ] Componentes generados con comando `ng g c`

---

## Notas

**Contexto Disponible en Cursor:**

- Estructura del proyecto: adjuntar carpeta `src/`
- Assets: adjuntar carpetas `public/images/`, `public/svg/`
- Configuración Angular: adjuntar `angular.json`, `tsconfig.json`

**Formato Crítico:** El parser automático buscará los encabezados `## COMPONENT:` y `## MAIN APP COMPONENT` para crear los archivos. No alterar este formato.

**Resumen Final:** Al finalizar la implementación, proporcionar un resumen **sin código**, solo indicando los componentes creados y aspectos clave implementados.
