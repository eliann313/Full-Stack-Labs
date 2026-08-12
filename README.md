# Full-Stack Engineering Labs

Colección de 10 laboratorios full-stack pequeños e independientes. Cada uno resuelve **un problema técnico concreto** — no son 10 CRUD con distinta piel.

## Filosofía

> Primero identificamos el problema → después elegimos la tecnología que tenga sentido para resolverlo.

La variedad de stack es consecuencia de las necesidades de cada problema, no el objetivo. Cada lab documenta por qué se eligió su tecnología, no solo cuál.

## Restricciones

- **$0 de costo.** Todo corre sobre free tiers permanentes (sin créditos promocionales que vencen). Ver la sección de servicios gratuitos de cada lab.
- **2-5 días por lab.** Sin paneles de administración gigantes, sin sistemas de permisos complejos, sin 30 entidades. El foco está en un problema, no en una aplicación completa.
- **Sin duplicar proyectos grandes existentes.** Estos labs no son un WMS, un ATS, ni un e-commerce — para eso ya hay otros proyectos en el portfolio.

## Laboratorios

| # | Lab | Problema | Concepto técnico principal | Stack | Estado |
|---|-----|----------|------------------------------|-------|--------|
| 01 | [Wallet](labs/01-wallet) | Transferencias entre usuarios sin duplicados ni inconsistencias | Idempotencia · transacciones ACID · ledger contable | FastAPI · PostgreSQL (Neon) · React | 🔵 En diseño |
| 02 | Workflows | Motor evento → condición → acción, con reintentos | Arquitectura orientada a eventos · colas · backoff | NestJS · BullMQ · Redis (Upstash) · PostgreSQL (Neon) · React | ⚪ Pendiente |
| 03 | Data Platform | CSV/dataset → estadísticas y gráficos | Procesamiento y agregación de datos | FastAPI · Pandas/Polars · PostgreSQL (Neon) · Next.js | ⚪ Pendiente |
| 04 | Chat | Mensajería con presencia en tiempo real | WebSockets · esquema no relacional de alta escritura | NestJS Gateway · Socket.io · MongoDB (Atlas) · Redis (Upstash) · Next.js | ⚪ Pendiente |
| 05 | Scraper | Recolectar y normalizar precios de distintas fuentes | Automatización · jobs programados · SSG | Python · Playwright · PostgreSQL (Neon) · Astro | ⚪ Pendiente |
| 06 | Reservas | Evitar doble reserva de un turno | Concurrencia optimista · scheduling | C# ASP.NET Core · Angular · PostgreSQL (Neon) | ⚪ Pendiente |
| 07 | Subastas | Pujas simultáneas sobre un mismo producto | Concurrencia · race conditions · WebSockets | NestJS Gateway · Redis (Upstash) · PostgreSQL (Neon) · React | ⚪ Pendiente |
| 08 | Delivery | Asignar y trackear el repartidor más cercano | Consultas geoespaciales · tiempo real | NestJS · PostGIS (Neon) · Next.js · Leaflet | ⚪ Pendiente |
| 09 | Procesamiento de Archivos | Subir un archivo y procesarlo en background | Workers asíncronos · colas · progreso | FastAPI · Celery · Redis (Upstash) · Cloudflare R2 · Next.js | ⚪ Pendiente |
| 10 | Monitoreo | Ingesta y visualización de métricas de varios servicios | Observabilidad · time-series | FastAPI/NestJS · Postgres + TimescaleDB (Neon) · Next.js | ⚪ Pendiente |

## Cómo está armado cada lab

Cada carpeta bajo `labs/` tiene su propio `DESIGN.md` (arquitectura, modelo de datos, API, trade-offs, deploy) siguiendo la plantilla de [`_template/DESIGN.md`](_template/DESIGN.md), más su propio backend, frontend y `docker-compose.yml` para correrlo local.

## Deployment

Cada lab se despliega de forma independiente desde este mismo repo:

- Backends → [Render](https://render.com) (free web service, root directory apuntando a la carpeta del lab)
- Frontends → [Vercel](https://vercel.com) o [Netlify](https://netlify.com)
- Bases de datos → [Neon](https://neon.tech) (Postgres, un proyecto por lab), [MongoDB Atlas](https://mongodb.com/atlas) (M0), [Upstash](https://upstash.com) (Redis)

## Orden de ejecución

Se construyen uno a la vez, en este orden — cada lab introduce como máximo una tecnología nueva respecto al anterior:

1. Wallet — stack conocido, consolida el patrón de repo/deploy
2. Workflows — primer framework nuevo (NestJS) y primera cola de jobs
3. Data Platform — conecta con la Licenciatura en Ciencia de Datos
4. Chat — primer WebSocket y primera base no relacional
5. Scraper — primer meta-framework de frontend (Astro)
6. Reservas — primer lenguaje nuevo (C#) y primer framework de frontend enterprise (Angular)
7. Subastas — concurrencia dura, ya con el patrón de WS afianzado
8. Delivery — geoconsultas
9. Procesamiento de Archivos — workers en background
10. Monitoreo — observabilidad y time-series
