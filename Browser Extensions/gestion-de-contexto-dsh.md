# Gestión de sesiones largas y uso de contexto en DeepSeek Harness (DSH)

> Notas prácticas sobre cuándo conviene seguir con una sesión larga y cuándo dividir
> el trabajo en varias sesiones, sin entrar en temas de precio/tokens.

## Matiz: "cache" ≠ "uso de contexto"

En la GUI, la barra que llega al 99% normalmente es el **uso de contexto**: cuánto de la
ventana del modelo está ocupado por todo el historial acumulado de la sesión (la
"memoria de trabajo"). Eso **no** es la caché del proveedor.

- La **caché** es el prefijo de la conversación que el proveedor reutiliza al continuar
  una misma sesión; no es lo que te debe preocupar.
- El **uso de contexto** es lo que te dice cuánta memoria lleva encima cada petición.

## Qué hace DSH cuando el contexto se acerca al límite

Comportamiento por defecto verificado en `compaction-basic`:

- **Umbral de presión**: la compactación automática se dispara cerca del **80%** de la
  ventana del modelo (`thresholdRatio = 0.8`).
- Al compactar, DSH **resume la parte más antigua** del historial y la sustituye por un
  resumen comprimido, conservando **verbatim** solo la cola reciente (~16% de la ventana,
  `retainRatio = 0.16`).
- Si se excede la ventana real, la petición puede **fallar por overflow** y reintentar.

Consecuencia práctica: una sesión al 99% casi seguro ya pasó por compactaciones y su
memoria más antigua está **degradada a resúmenes** (empieza a olvidar detalles finos del
código viejo).

## ¿Sesión larga o dividir en varias?

### Continuar la misma sesión conviene cuando:
- Sigues en la **misma tarea cohesionada**: iteras sobre un archivo/módulo concreto.
- Todo lo relevante está en la **cola reciente** (la parte que sobrevive a la compactación).
- El modelo ya tiene el contexto en la cabeza; cortar lo obligaría a re-descubrir todo.

### Abrir sesión nueva conviene cuando:
- Estás al 80–99% **y** la siguiente tarea es nueva o de otra zona del código que ya no
  está en la cola reciente (se perdió en resúmenes).
- Gran parte del contexto acumulado es **ruido muerto**: exploraciones descartadas,
  callejones sin salida, archivos que ya no se tocan. Ese ruido viaja en *cada* mensaje.
- El modelo **empieza a re-preguntar o produce código inconsistente** con lo anterior
  (señal de que la compactación ya le comió memoria útil).

### Regla práctica
Dividir en más sesiones que no lleguen al 99% es **acertado**, pero hay que dividir **por
unidad de trabajo cohesionada** (una feature, un bugfix, un archivo), no por tamaño.
Dos sesiones limpias sobre tareas distintas casi siempre salen mejor que una sola sesión
gigante que mezcla todo.

## Cómo evitar llegar al 99% / desperdiciar contexto

1. **Corta por tarea, no por "se acabó la barra".** Termina una feature → nota breve de lo
   decidido → sesión nueva enfocada para la siguiente.
2. **Haz un handoff compacto**: al cerrar, deja en un archivo o en el prompt inicial de la
   nueva sesión un resumen corto de estado y decisiones, para no re-explicar ni re-descubrir.
3. **No le hagas leer repetidamente archivos enormes ni volcar outputs gigantes** en cada
   mensaje (eso infla la barra). Prefiere apuntar a secciones/funciones concretas.
4. **Agrupa cambios pequeños en un solo mensaje** ("cambia A y B") en vez de muchos mensajes
   de una línea, para arrastrar menos historial.

## Conclusión

> **Sesiones largas son ideales para iteración apretada sobre una misma zona de código;
> pero en cuanto superas ~80% o cambias de zona, empieza una sesión nueva enfocada.**
> Ese es el punto de equilibrio, no esperar a tocar el 99%.

## Referencia (código de DSH)

- `packages/compaction/compaction-basic/src/config.ts` — umbrales `thresholdRatio` (0.8)
  y retención `retainRatio` (0.16) por defecto.
- `packages/compaction/compaction-basic/src/index.ts` — política de presión automática.
- `docs/subsystems/compaction.md` — la seam de compactación (eventos, tipos, políticas).
