# Multiidioma en la extensión (ca / es / en / pt-PT) — guía técnica

## Objetivo

La extensión debe estar disponible en **catalán (ca)**, **español (es)**,
**inglés (en, por defecto)** y **portugués de Portugal (pt-PT)**, con opción
de cambiar de idioma sin tocar el idioma del navegador.

## Dos mecanismos complementarios

### A) `_locales` + `chrome.i18n` (sistema oficial Chrome/Firefox)

- Carpeta `_locales/<idioma>/messages.json`, con códigos BCP-47.
  Ejemplos: `en`, `es`, `ca`, `pt_PT`.
- Formato: `{ "clave": { "message": "texto", "description": "…" } }`.
- Acceso en JS: `chrome.i18n.getMessage('clave')` (content scripts,
  background y páginas). En el **manifest** y HTML estático: `__MSG_clave__`.
- Requiere `"default_locale": "es|en"` en el manifest.
- **Límite**: el idioma lo decide el navegador; no se puede forzar en caliente.

### B) Diccionario propio + función `t()` (cambio en caliente)

- Ficheros `locales/{en,es,ca,pt-PT}.json` con mapas `clave → texto`, y una
  función central:
  ```js
  async function loadI18n(lang) { /* fetch(chrome.runtime.getURL('locales/'+lang+'.json')) */ }
  function t(key, params) { /* lookup con sustitución {param} y fallback a en */ }
  ```
- El idioma activo sale del ajuste `settings.language` (por defecto `'en'`).
- Permite **cambiar el idioma al momento** sin reiniciar (clave para usar
  catalán con el navegador en español).

### Recomendación (híbrido)

- `_locales` para lo que ve la tienda/el sistema: nombre, descripción y título
  del botón de acción del manifest.
- Diccionario propio con `t()` para todo lo dinámico: panel, opciones, menú
  contextual, estados, avisos, modales, toasts y errores del background.

## Qué se traduce

| Dónde | Ejemplos |
|---|---|
| `manifest.json` | `name`, `description`, `default_title` (vía `__MSG_`) |
| `content.js` (panel) | botones, estados, aviso «vídeo anterior», modales, «No hay vídeo seleccionado», toasts, etiqueta «Texto del vídeo:» |
| `background.js` | errores visibles (transcripción, proveedor), títulos del menú contextual, prompts por defecto |
| `options.*` | etiquetas, avisos, modal de prompts, selector de idioma |
| Guardado `.md` | cabeceras «URL:», «Fecha:», «Prompt:» |
| Prompts por defecto | listas completas por idioma |

## Pasos de implementación

1. Inventario de cadenas → claves (`status.transcribing`, `banner.stale`, …).
2. Catálogos `locales/{en,es,ca,pt-PT}.json` + `t()` con *fallback* a `en`.
3. Ajuste **Idioma** en Opciones (Automático según navegador o elección
   explícita); por defecto **inglés**.
4. Sustituir strings en `content.js`, `background.js` y `options.*`.
5. Menú contextual: recrear títulos al cambiar idioma (`removeAll` + `create`).
6. Prompts por defecto por idioma (primera instalación o «Restaurar prompts»).
7. Sincronizar cambios entre las carpetas Chrome y Firefox (código idéntico).

## Puntos delicados

- `chrome.i18n` no permite forzar idioma → diccionario propio para la elección
  manual (p. ej. catalán con navegador en español).
- Cadenas con parámetros (títulos de vídeo, números de fragmento): placeholders
  `{param}`; insertar siempre con `textContent` (o HTML autorizado sin
  parámetros de usuario) para evitar inyecciones.
- Codificación UTF-8; escapar comillas en `messages.json`.
- Código de portugués de Portugal: **pt-PT** (carpetas `_locales/pt_PT`).
- No traducir códigos internos del puente ni claves de storage.
- Test: comprobar que los 4 catálogos existen, son JSON válidos y cubren las
  mismas claves; evitar strings sueltas fuera de los catálogos.
