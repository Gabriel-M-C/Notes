# Especificación — Apertura, minimización, cierre y cambios de vídeo del panel

> Documento de comportamiento consensuado (no describe necesariamente el estado actual del código).
> Ámbito: panel lateral de **YouTube AI Summarizer** en YouTube.
> Estado: **definido** · Pendiente de portar a Firefox tras verificación manual en Chrome.

---

## Estados

| Estado | Qué se ve |
|---|---|
| Cerrado | Nada (solo el icono de la extensión en la barra) |
| Abierto | Cabecera + estado + [aviso vídeo anterior] + prompt (opcional) + resultado |
| Minimizado | Pestaña lateral fina («Resumen IA») |

Contenido del panel (independiente de abrir/cerrar): `sin vídeo` · `con vídeo, sin resultado` · `resultado del vídeo actual` · `resultado de otro vídeo`.

Regla global: la **generación nunca se cancela** por ocultar/cerrar el panel (la ejecuta el content script). El estado es **por pestaña**.

---

## ABRIR

| Disparador | Comportamiento |
|---|---|
| Llegada a YouTube | Panel **cerrado** hasta que el usuario realiza alguna acción |
| Botón de extensión — panel cerrado + vídeo | Abre mostrando el vídeo actual |
| Botón de extensión — panel cerrado + **sin vídeo** | Abre con mensaje **«No hay vídeo seleccionado»**; se **oculta el prompt** (sin Generar clickeable); el botón ⚙ Opciones pasa a la **cabecera** para seguir accesible |
| Botón de extensión — panel **ya abierto** | **Sincroniza** el panel al vídeo que se está visualizando (nunca cierra; cerrar = ✕ o ▁) |
| Botón de extensión — panel **minimizado** | **Restaura** el panel |
| Menú contextual «¿Vale la pena?» | Abre/restaura y **ejecuta** el prompt escueto sobre el vídeo objetivo |
| Menú contextual «Abrir panel» sobre una miniatura de otro vídeo | Abre el panel **fijado al vídeo de la miniatura** (cabecera con su título, prompt listo para generar) |
| Menú contextual «Abrir panel» sin miniatura | Abre el panel del vídeo de la página |

- Si el panel está en estado «sin vídeo» y el usuario navega a un vídeo → el panel se actualiza y el **prompt reaparece** automáticamente.

---

## MINIMIZAR (▁)

- Conserva **todo** (resultado, estado, prompt). Queda la pestaña lateral.
- Restaurar: clic en la pestaña, o botón de extensión.
- Si hay una generación en curso continúa; al terminar con el panel minimizado o cerrado → **aviso** de que el resultado ya está listo.

---

## CERRAR (✕)

- **Sin resultado**: cierra y limpia (sin aviso).
- **Con resultado**: pregunta **«¿Guardar el resultado antes de cerrar?»** con tres opciones:
  - `Guardar .md y cerrar`
  - `Cerrar sin guardar`
  - `Cancelar` (vuelve al panel)
- Salvo «Cancelar» → el panel **se cierra y se limpia** (el resultado no queda en memoria).

---

## Cambio de URL con texto de otro vídeo

Cuando hay texto generado del vídeo A y la URL pasa a otro contexto:

| Tipo de navegación | Comportamiento |
|---|---|
| **Manual** (panel abierto) | Aviso activo: `Conservar texto` · `Guardar .md` · `Limpiar` |
| **Autoplay** | Sin aviso: el texto se conserva en silencio con **etiqueta pasiva** indicando a qué vídeo pertenece (para leerlo o guardarlo) |
| Hacia zona **sin vídeo** (inicio, búsqueda…) | Sin aviso: se conserva con etiqueta; al llegar a un vídeo distinto se aplica la regla manual/autoplay |
| Panel cerrado | No hay texto que conservar (cerrar limpia) → sin avisos |

**Detección de autoplay**: se considera autoplay si el vídeo anterior llegó a su fin (player `ended`) **y** no hubo interacción del usuario en los segundos previos a la navegación.

Al elegir **Conservar**: se muestra una **etiqueta sobre el resultado** con el **título + enlace del vídeo real** al que pertenece el texto, mientras no coincida con el vídeo actual.

---

## GENERAR / ¿VALE LA PENA? pisando texto de otro vídeo

Si se lanza una generación sobre el vídeo B y existe texto conservado del vídeo A (se va a reemplazar), **se pregunta antes**:

- `Conservar (cancela)` — no se genera.
- `Guardar .md y continuar` — guarda y después genera (reemplaza).
- `Reemplazar` — genera sin guardar (reemplaza).

Aplica a Generar, Regenerar, «¿Vale la pena?» (página y miniatura) y «Abrir panel» sobre miniatura.

---

## Vídeo fijado (menú contextual sobre miniatura)

- El panel se **fija** al vídeo enlazado (cabecera con su título; la transcripción se obtiene por el puente de página, nunca del panel del vídeo abierto).
- El **botón de extensión** (sincronizar) o una **navegación manual** desfijan el panel y lo devuelven al vídeo de la página, aplicando las reglas de aviso si hay texto de otro vídeo.

---

## Recarga de la página (F5)

- El último resultado se guarda (storage de sesión) al generarse y se **limpia al cerrar/limpiar** el panel.
- Al recargar:
  - Si el resultado guardado pertenece al **mismo vídeo** de la URL → se **restaura y el panel se abre** automáticamente.
  - Si pertenece a **otro vídeo** → se conserva y, al abrir el panel, se aplica el **aviso normal** de «texto de otro vídeo».
- Navegar fuera de YouTube descarga el content script; el estado se pierde salvo el guardado de sesión anterior.

---

## Mejoras incluidas en esta especificación

1. ⚙ Opciones accesible desde la cabecera del panel (no solo dentro de la sección del prompt).
2. Estado vacío claro («No hay vídeo seleccionado») en lugar de un panel con controles inutilizables.
3. Botón **Guardar .md** dentro de los avisos (vídeo anterior y cerrar).
4. **Etiqueta** sobre el resultado indicando el vídeo real del texto cuando no coincide con el actual.
5. Aviso antes de que una generación **pise** texto de otro vídeo.
6. Aviso al terminar una generación con el panel no visible (minimizado/cerrado).
