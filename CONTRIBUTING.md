# Cómo Contribuir a esta Wiki

Esta wiki vive en un repositorio propio, separado del código de los
microservicios. Se edita igual que cualquier repo: fork/rama, cambios,
Pull Request, revisión.

## Tipos de contribución

| Tipo | Ejemplo | Proceso |
|---|---|---|
| **Corrección menor** | typo, link roto, actualizar una fecha | PR directo, 1 aprobación |
| **Actualizar una página existente** | agregar un ejemplo, aclarar una política ya vigente | PR directo, 1 aprobación |
| **Nueva tecnología en el Tech Radar** | proponer evaluar una librería, herramienta o patrón nuevo | Ver proceso "RFC de tecnología" abajo |
| **Cambio de política vigente** | cambiar el umbral del Quality Gate, cambiar el modelo de ramas | Ver proceso "RFC de política" abajo |

## RFC de tecnología nueva (mover algo en el Tech Radar)

1. **Crear la página de decisión** en la subcarpeta correspondiente de
   `03-decisiones-arquitectura/`, usando
   [00-inicio/plantilla-decision.md](./00-inicio/plantilla-decision.md).
   La tecnología entra directamente en el anillo **Evaluar**.
2. Abrir un Pull Request con la página nueva y actualizar la imagen/lista
   del [Tech Radar](./02-tech-radar/README.md).
3. El PR debe ser revisado por al menos un Tech Lead. Se espera discusión
   en los comentarios del PR, no solo aprobación silenciosa.
4. Una vez mergeado, la tecnología queda habilitada para usarse en spikes
   o pruebas de concepto, nunca directamente en un flujo crítico de
   producción (ver reglas de cada anillo en
   [02-tech-radar/README.md](./02-tech-radar/README.md)).
5. El movimiento de anillo (Evaluar → Probar → Adoptar) se propone con un
   nuevo PR que actualice la página de decisión existente, incluyendo
   evidencia de uso real (ej. en qué microservicio se probó).

## RFC de cambio de política

Aplica a cambios en capítulos como Git Workflow, CI/CD, Seguridad,
Calidad de Código, etc.

1. Abrir un **Issue** describiendo el problema con la política actual (no
   directamente el PR con el cambio).
2. Dar como mínimo 3 días hábiles para comentarios del equipo.
3. Una vez alineado, abrir el PR con el cambio a la página correspondiente.
4. Requiere aprobación de un Tech Lead o de DevOps, según el capítulo
   afectado (Tech Lead para arquitectura/calidad, DevOps para
   infraestructura/CI-CD/seguridad).
5. Si el cambio afecta el checklist de bootstrap de microservicios (ver
   [08-estructura-microservicios](./08-estructura-microservicios/)),
   debe indicarse explícitamente si aplica también a microservicios ya
   existentes o solo a los nuevos.

## Estilo de escritura

- Español, tono profesional pero directo. Evitar relleno.
- Toda página de decisión sigue la plantilla — no se aceptan páginas
  nuevas en `03-decisiones-arquitectura/` sin las secciones obligatorias.
- Ejemplos de código en TypeScript/Node.js salvo que el contexto de la
  página exija otro lenguaje.
- Preferir tablas y listas sobre párrafos largos cuando el contenido es
  comparativo (alternativas, trade-offs).

## Revisión periódica

Se recomienda revisar esta wiki completa junto con la auditoría interna
semestral (ver
[04-gobierno-ciclo-vida/estandares-internacionales.md](./04-gobierno-ciclo-vida/estandares-internacionales.md)),
para detectar páginas desactualizadas o tecnologías que cambiaron de
anillo sin actualizar su documentación.