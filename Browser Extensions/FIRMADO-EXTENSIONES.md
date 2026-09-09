# Firma y distribución de extensiones — Chrome / Brave y Firefox

> Nota práctica general: mientras la extensión sea para uso personal o pruebas,
> **Chrome** se instala como «cargar descomprimida» desde un `.zip`, y
> **Firefox** como complemento temporal en `about:debugging`. La firma solo se
> necesita para instalar de forma permanente y para distribuir públicamente.

---

## 🔵 Chrome / Brave (Chromium)

### A. Desarrollo y pruebas (sin firmar)

- `chrome://extensions` → *Modo desarrollador* → **«Cargar descomprimida»** →
  seleccionar la carpeta del proyecto.
- No hay firma: Chrome ejecuta la extensión tal cual. Es la vía usada para
  probar y para moverla a otro ordenador (`.zip` + cargar descomprimida).

### B. Empaquetado `.crx` firmado localmente

- En el mismo gestor: *«Empaquetar extensión…»* → genera **`.crx` + `.pem`**
  (la clave privada del desarrollador).
- El **ID de la extensión se deriva de la clave pública** de esa `.pem`.
  Reempaquetar con la **misma `.pem`** mantiene el ID estable (imprescindible
  para no romper permisos ni configuraciones de usuarios ya instalados).
  La `.pem` debe guardarse a buen recaudo.
- Limitación: Chrome moderno **bloquea la instalación de `.crx` externos**.
  Un `.crx` autofirmado solo se puede instalar mediante **política de
  empresa** (`ExtensionInstallForcelist` en `chrome://policy` o gestión MDM).

### C. Distribución pública: Chrome Web Store

1. Cuenta de **desarrollador de Chrome Web Store** + tarifa única de registro
   (**~5 USD**).
2. Subir un **`.zip`** (no el `.crx`) al dashboard del desarrollador.
3. Rellenar la ficha (descripción, capturas, iconos, **declaración de
   privacidad**) y elegir visibilidad: **Pública** / **Privada** (enlace
   directo) / **Solo probadores** (grupos).
4. Google **revisa** (manifest, permisos, contenido, privacidad) antes de
   publicar. Las **actualizaciones** se suben como nuevo `.zip` y vuelven a
   pasar revisión; la tienda las distribuye automáticamente con el mismo ID.

### D. Resumen de vías

| Objetivo | Vía |
|---|---|
| Otro ordenador / pruebas | `.zip` + cargar descomprimida |
| Entornos de empresa | `.crx` con `.pem` + política |
| Público general | Chrome Web Store (registro + revisión) |

---

## 🦊 Firefox (Mozilla)

### A. Desarrollo y pruebas (sin firmar)

- `about:debugging#/runtime/this-firefox` → **«Cargar complemento temporal…»**
  → elegir `manifest.json`. Sin firma, pero es **temporal**: desaparece al
  reiniciar el navegador.
- Firefox **release y beta exigen firma obligatoria** para instalaciones
  permanentes. Solo **Developer Edition, Nightly y ESR** permiten desactivar
  la comprobación con `xpinstall.signatures.required = false`.

### B. Firma con Mozilla (obligatoria para instalar/distribuir)

1. Cuenta en **addons.mozilla.org (AMO)** → Developer Hub.
2. Subir el paquete (`.zip` o `.xpi`):
   - **Listed**: público en la tienda → revisión humana (contenido, permisos,
     privacidad); actualizaciones automáticas vía AMO.
   - **Unlisted**: firmado por Mozilla pero **no aparece en la tienda** →
     revisión automatizada más rápida; tú distribuyes el `.xpi` firmado
     (ideal para uso interno/equipos).
3. Mozilla firma y devuelve el **`.xpi`** listo para instalar (arrastrar a
   Firefox o *Ajustes → Instalar complemento desde archivo*).

### C. Firma con `web-ext` (CLI oficial, automatizable/CI)

```bash
web-ext build                                # genera el .zip
web-ext sign --api-key=<clave> --api-secret=<secreto>
```

- Las credenciales (`--api-key`/`--api-secret`) se crean en AMO → Developer
  Hub → *API keys*. Mozilla firma remotamente y `web-ext` descarga el `.xpi`
  firmado, sin abrir el navegador.

### D. Requisitos específicos (aplicados a este proyecto Firefox)

- `browser_specific_settings.gecko.id`: el valor actual
  (`youtube-ai-summarizer-firefox@example.com`) vale para desarrollo/carga
  temporal, pero **antes de subir a AMO hay que usar un id único** (o dejar
  que AMO lo asigne omitiéndolo).
- `strict_min_version` ya fijado en `115.0` (Manifest V3).
- El manifest ya usa la estructura MV3 de Firefox (event page en background,
  `contextMenus`, librerías UMD en content_scripts).

### E. Costes y diferencias con Chrome

- AMO **no cobra tarifa de registro**.
- La firma la hace **siempre Mozilla** (no hay autofirma válida para release).
- Para probar en otro ordenador sin publicar: carga temporal, complemento
  **unlisted** firmado, o Developer Edition con la firma desactivada.

---

## Resumen rápido para estos dos proyectos

- Uso personal/pruebas → Chrome: `.zip` + cargar descomprimida · Firefox:
  carga temporal en `about:debugging`.
- Distribución real → Chrome: Chrome Web Store (tarifa ~5 USD) · Firefox:
  AMO (listed/unlisted) o `web-ext sign`.
