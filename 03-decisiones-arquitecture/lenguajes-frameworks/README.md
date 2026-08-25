# Lenguajes & Frameworks

Decisiones sobre **con qué lenguaje y framework se construye cada
componente** de Fixia.

> **Nota sobre el estado de estas decisiones:** a diferencia de
> `plataformas-infraestructura/`, esta subcarpeta define un **criterio de
> selección** más que un stack ya cerrado en piedra. A medida que se creen
> microservicios reales, cada uno debe justificar su lenguaje contra este
> criterio y esa justificación queda documentada en el README del propio
> repositorio (ver plantilla en
> [13-plantillas-ejemplos](../../13-plantillas-ejemplos/)).

| Tecnología | Estado en el radar | Uso previsto | Página |
|---|---|---|---|
| Node.js + TypeScript | 🟢 Adoptar | Default para microservicios backend | [nodejs-typescript.md](./nodejs-typescript.md) |
| React | 🟢 Adoptar | Frontend (portal de clientes/proveedores, backoffice) | [react.md](./react.md) |
| Python (FastAPI) | 🟡 Probar | Dominios con carga geoespacial/analítica | [python-fastapi.md](./python-fastapi.md) |
| Java (Spring Boot) / .NET (C#) | 🟣 Retener | No se adoptan por defecto | [poliglotismo-controlado.md](./poliglotismo-controlado.md) |

## Criterio de selección por tipo de carga

En vez de fijar "un lenguaje para todo", Fixia clasifica cada
microservicio según el tipo de trabajo que hace, y ese tipo determina el
lenguaje por defecto:

| Tipo de carga | Ejemplo de dominio | Lenguaje por defecto | Por qué |
|---|---|---|---|
| **I/O-bound / orquestación de APIs** | Solicitudes, Usuarios, Proveedores, Notificaciones, apigateway | Node.js + TypeScript | Alto rendimiento en operaciones concurrentes de red (llamadas entre microservicios, DB, colas), mismo lenguaje que el frontend (menor fricción de contexto para el equipo). |
| **Geoespacial / procesamiento de datos** | Geolocalización, Búsqueda y Matching | Python (FastAPI) | Ecosistema maduro de librerías geoespaciales y de análisis de datos (ver [python-fastapi.md](./python-fastapi.md)), relevante porque estos son los dominios más técnicamente exigentes de Fixia. |
| **Interfaz de usuario** | Web-portal, Web-admin | React | Estándar de facto para SPAs, gran disponibilidad de componentes y talento. |
| **Cualquier otro caso** | — | Requiere página de decisión propia antes de introducirse | Ver [poliglotismo-controlado.md](./poliglotismo-controlado.md) para el proceso. |

## Por qué este criterio y no "un lenguaje único para todo"

Un único lenguaje para todos los microservicios (ej. solo Node.js)
simplificaría la operación, pero el dominio de **Búsqueda y Geolocalización**
—que es el corazón técnico de Fixia (ver
[01-contexto-proyecto/mapa-dominios-negocio.md](../../01-contexto-proyecto/mapa-dominios-negocio.md))—
se beneficia de forma concreta de un ecosistema orientado a datos y cálculo
geoespacial. Forzar todo a un solo lenguaje sacrificaría esa ventaja sin un
beneficio operativo suficiente, dado que Docker (ver
[plataformas-infraestructura/docker.md](../plataformas-infraestructura/docker.md))
ya resuelve la mayor parte del costo de tener más de un lenguaje en el
stack (despliegue uniforme sin importar el lenguaje interno).

Lo que sí se restringe activamente es el **poliglotismo sin criterio**:
sumar un lenguaje nuevo porque sí. Ver
[poliglotismo-controlado.md](./poliglotismo-controlado.md).