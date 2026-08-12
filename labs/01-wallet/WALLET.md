# Wallet

## 1. Problema concreto

Dos usuarios de una billetera virtual necesitan poder transferirse dinero entre sí de forma segura: sin duplicar la transferencia si el cliente reintenta la request (por ejemplo, por un timeout de red), y sin que el balance quede inconsistente cuando llegan transferencias concurrentes sobre la misma wallet.

## 2. Objetivo / MVP exacto

- [ ] Crear una wallet (identificador simple, sin autenticación real)
- [ ] Fondear una wallet (depósito simulado, sin integración de pagos real)
- [ ] Transferir saldo entre dos wallets, exigiendo `Idempotency-Key` en el header
- [ ] Reintentar una transferencia con la misma `Idempotency-Key` → devuelve el mismo resultado, no duplica el movimiento
- [ ] Consultar balance actual de una wallet
- [ ] Consultar historial de movimientos (ledger) de una wallet
- [ ] Dos transferencias simultáneas sobre la misma wallet no deben pisarse ni dejar el balance inconsistente

## 3. Qué NO vamos a construir

- Autenticación real (JWT, OAuth, sesiones) — un identificador de usuario simple alcanza
- Múltiples monedas o conversión de divisas
- Integración con pasarela de pago real (Stripe, MercadoPago)
- Reversión de transferencias o sistema de disputas
- Límites de transferencia, KYC, verificación de identidad

## 4. Concepto técnico principal

Idempotencia (idempotency keys), transacciones ACID, ledger contable (double-entry bookkeeping) y control de concurrencia mediante locking pesimista.

## 5. Stack y justificación

| Capa | Tecnología | Por qué |
|------|-----------|---------|
| Frontend | React (Vite) | App de estado, no de contenido — no necesita SSR/SEO |
| Backend | FastAPI (Python) | Stack ya conocido; el foco de este lab es consolidar el patrón de repo/deploy, no aprender un framework nuevo |
| Base de datos | PostgreSQL (Neon) | Transacciones ACID reales, necesarias para el ledger — no negociable en un dominio financiero |
| Cache / colas | No aplica | Este lab no necesita cache ni colas — mantenerlo simple es parte del punto |

## 6. Arquitectura simplificada

```
React (Vite) ──HTTP──> FastAPI ──transacción──> PostgreSQL (Neon)
                                     │
                                     └─ SELECT ... FOR UPDATE sobre
                                        ambas wallets (ordenadas por id
                                        para evitar deadlock)
```

Cada transferencia corre dentro de una única transacción de base de datos: se bloquean ambas filas de wallet, se valida fondos, se inserta la `Transaction` y las dos `LedgerEntry`, se actualizan los balances, y recién ahí se hace commit.

## 7. Modelo de datos

- **Wallet**: `id, owner_name, balance_cents, created_at`
  (balance en centavos para evitar errores de punto flotante)
- **Transaction**: `id, from_wallet_id, to_wallet_id, amount_cents, idempotency_key (UNIQUE), status, created_at`
- **LedgerEntry**: `id, wallet_id, transaction_id, direction (debit|credit), amount_cents, balance_after_cents, created_at`
  (cada transferencia genera dos entradas — nunca se pisa el balance sin dejar rastro)

## 8. API

| Método | Path | Descripción |
|--------|------|-------------|
| POST | `/wallets` | Crear wallet |
| POST | `/wallets/{id}/deposit` | Fondeo simulado |
| GET | `/wallets/{id}` | Balance actual |
| GET | `/wallets/{id}/ledger` | Historial de movimientos |
| POST | `/transfers` | Transferir (requiere header `Idempotency-Key`) |

## 9. Trade-offs

**Locking pesimista en vez de optimista.** A diferencia del lab de Reservas (donde sí se usa concurrency token / locking optimista), acá se eligió `SELECT ... FOR UPDATE`: en un dominio financiero es preferible que la segunda transferencia espere a que la primera termine, antes que arriesgarse a que el cliente vea un balance momentáneamente incorrecto y tenga que reintentar. El costo es menor throughput bajo alta concurrencia, aceptable para el volumen de este lab.

**Sin autenticación real.** Se sacrifica realismo en favor de mantener el foco exclusivamente en idempotencia y consistencia transaccional, que es el concepto que este lab busca demostrar.

## 10. Servicios gratuitos y límites a vigilar

- **Render** (free web service) — el backend se duerme a los 15 min de inactividad; primera consulta después de eso tarda 30-60s en responder. Aclarado en la demo pública.
- **Neon** (free tier) — 0.5GB de storage y scale-to-zero por proyecto; de sobra para el volumen de este lab.
- **Vercel** (free tier) — frontend estático, sin límites relevantes para este caso de uso.

## 11. Cómo correrlo localmente

```bash
git clone <repo>
cd labs/01-wallet
cp .env.example .env
docker compose up -d          # levanta Postgres local
cd backend && uvicorn main:app --reload
cd frontend && npm run dev
```

## 12. Deployment

- Backend → Render, root directory `labs/01-wallet/backend`
- Frontend → Vercel, root directory `labs/01-wallet/frontend`
- Base de datos → Neon, proyecto `wallet-lab`

## 13. Dificultad real vs. estimada

_A completar al terminar el lab._

## 14. Qué aprendí

_A completar al terminar el lab._
