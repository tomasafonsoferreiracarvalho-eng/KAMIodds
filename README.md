# KAMIodds

<div align="center">

# KamiODDS

**Plataforma SaaS de apostas inteligentes para o mercado de língua portuguesa**
Surebets · Apostas de valor (+EV) · Matched Betting · Escola

[![Website](https://img.shields.io/badge/kamiodds.com-111827?style=for-the-badge&logo=googlechrome&logoColor=white)](https://kamiodds.com)
[![Discord](https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/d2awCsJJwu)

![Python](https://img.shields.io/badge/Python_3.12-3776AB?style=for-the-badge&logo=python&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js_14-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![Fastify](https://img.shields.io/badge/Fastify-000000?style=for-the-badge&logo=fastify&logoColor=white)
![Socket.IO](https://img.shields.io/badge/Socket.IO-010101?style=for-the-badge&logo=socketdotio&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL_16-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis_7-DC382D?style=for-the-badge&logo=redis&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Stripe](https://img.shields.io/badge/Stripe-635BFF?style=for-the-badge&logo=stripe&logoColor=white)
![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)
![Hetzner](https://img.shields.io/badge/Hetzner-D50C2D?style=for-the-badge&logo=hetzner&logoColor=white)

</div>

---

## O que é

O KamiODDS deteta **surebets** (combinações de odds em casas diferentes que garantem lucro independentemente do resultado), **apostas de valor (+EV)** e **arbitragens cruzadas Double Chance**, e distribui-as em tempo real a utilizadores subscritos.

Além da deteção, o produto guia o utilizador na execução (wizards passo a passo), regista o lucro real e ensina os fundamentos na **Escola**. O foco atual é o **matched betting**: transformar os bónus das casas em lucro garantido, como porta de entrada para quem está a começar.

> **Não é uma casa de apostas.** Não aceita apostas nem gere dinheiro de clientes. É um SaaS de análise de dados.

---

## Arquitetura

```
 The Odds API + odds-api.io
            │ HTTP
            ▼
 ┌─────────────────────────────────────────────────────────┐
 │ Python Core (asyncio)                                   │
 │ collector → matching engine → arbitrage engine → scheduler │
 └───────┬──────────────────────────────┬──────────────────┘
         │ INSERT                       │ PUBLISH
         ▼                              ▼
   PostgreSQL ◄───────────────────── Redis (pub/sub + cache)
         │ SELECT                       │ SUBSCRIBE
         ▼                              ▼
 ┌─────────────────────────────────────────────────────────┐
 │ TypeScript API (Fastify + Socket.IO)                    │
 │ REST + WebSocket · JWT · planos · Stripe · jobs         │
 └──────────────────────────┬──────────────────────────────┘
                            ▼
              Next.js 14 (App Router) — Frontend
```

---

## Funcionalidades

**Produto**
- ⚡ Dashboard de surebets em tempo real via WebSocket
- 📈 Apostas de valor (+EV) contra uma casa de referência sharp
- 🔀 Arbitragens cruzadas Double Chance
- 🎁 Matched betting: calculadora, wizard passo a passo e gestor de bónus
- 🎓 Escola com quiz, aula de matched betting e certificado
- 🧮 Calculadora de arbitragem e simulador de banca (públicos)
- 🧭 Wizard de execução com registo de apostas e página `/profits` com P&L real
- 📅 Histórico de surebets detetadas (expiradas e limpas distinguidas)
- 🇵🇹 Badge de licenciamento SRIJ por casa de apostas
- 🔔 Alertas por Discord e Telegram, filtrados por plano
- 📱 Interface responsiva (tabelas viram cartões no telemóvel)

**Motor**
- Matching de eventos entre casas com 500+ aliases de equipas e *confidence scoring*
- Multi-fonte de odds, selecionável no painel de admin
- Deteção de duplicados (`new` / `updated` / `duplicate`)
- Precisão decimal em todos os cálculos monetários

**Sistema**
- Auth com JWT, refresh tokens, verificação de email e *device fingerprinting*
- Proteção contra brute force, bloqueio de conta e anti-abuso de trials
- Planos Free / Plus / Premium com controlo de acesso, Stripe (checkout, portal, webhooks idempotentes) e Free Trial Premium de 3 dias
- Painel de admin com gestão de utilizadores, estatísticas e limpeza de board/histórico
- Integração Discord com atribuição automática de cargos por plano
- Migrations automáticas de base de dados

---

## Stack

| Camada | Tecnologia |
|--------|-----------|
| Deteção | Python 3.12, asyncio, httpx, asyncpg, structlog |
| API | Fastify, TypeScript, Socket.IO, Zod, JWT, Stripe, Resend |
| Frontend | Next.js 14 (App Router), React 18, TypeScript, Tailwind CSS |
| Dados | PostgreSQL 16, Redis 7 |
| Infraestrutura | Docker Compose, Nginx (TLS), Cloudflare (CDN/WAF), UFW, Hetzner VPS |

---

## Estrutura do projeto

```
surebet-platform/
├── services/
│   ├── python-core/
│   │   ├── odds_collector/        # Recolha multi-fonte de odds
│   │   ├── matching_engine/       # Emparelhamento de eventos
│   │   ├── arbitrage_engine/      # Surebets, +EV, Double Chance
│   │   ├── notifications/         # Discord e Telegram
│   │   ├── persistence/           # PostgreSQL, Redis, migrations
│   │   └── scheduler/             # Orquestração dos ciclos
│   └── typescript-api/
│       └── src/
│           ├── api/routes/        # REST: surebets, value-bets, auth, admin, stripe...
│           ├── auth/              # JWT, middleware de planos, device fingerprint
│           ├── websocket/         # Socket.IO + subscriber Redis
│           └── jobs/              # Trials, relatórios semanais
├── apps/
│   └── frontend/src/
│       ├── app/                   # surebets, bonuses, academy, profits, simulator...
│       └── components/            # Dashboard, wizards, calculadoras
└── infra/
    └── docker/                    # docker-compose, nginx, scripts, migrations
```

---

## Quick Start

**Requisitos:** Docker Desktop

```bash
cd infra/docker
cp .env.example .env   # preenche com os teus valores
docker compose up
```

Abre **http://localhost:3000**. Com `MOCK_MODE=true` não precisas de chaves de API.

```bash
docker compose down                  # parar
docker compose up --build            # rebuild após mudar dependências
docker compose up --build frontend   # rebuild só do frontend
```

### Variáveis de ambiente

| Variável | Descrição | Default |
|----------|-----------|---------|
| `MOCK_MODE` | Usa dados fictícios (sem API key) | `true` |
| `ODDS_API_KEY` | Chave da The Odds API | — |
| `COLLECTOR_INTERVAL_SECONDS` | Ciclo de recolha em segundos | `300` |
| `ARBITRAGE_MIN_ROI` | ROI mínimo para detetar surebet | `0.3` |
| `DEFAULT_INVESTMENT` | Stake padrão para cálculos | `1000.00` |
| `DISCORD_WEBHOOK_URL` | Webhook para alertas Discord | — |
| `JWT_SECRET` | Segredo dos tokens JWT | — |
| `RESEND_API_KEY` | Chave Resend para emails | — |

> ⚠️ Nunca faças commit do `.env`.

---

## API

| Endpoint | Auth | Descrição |
|----------|------|-----------|
| `GET /health` | — | Estado dos serviços |
| `GET /surebets` | Plus+ | Surebets ativas |
| `GET /value-bets` | Plus+ | Apostas de valor (+EV) |
| `GET /history` | Plus+ | Histórico de surebets |
| `GET /profits` | Plus+ | Histórico de apostas executadas |
| `POST /executions` | Plus+ | Guardar execução do wizard |
| `* /bonuses` | Premium | Gestor de bónus (matched betting) |
| `* /admin/*` | Admin | Gestão de utilizadores e estatísticas |

**Eventos Socket.IO:** `surebet:new` · `surebets:cleared` · `odds:update` · `connection:ack` · `health:ping`

---

## Precisão financeira

Todos os valores monetários viajam como **strings decimais**, nunca como `number` em JavaScript ou `float` em Python.

```
Python:      Decimal('2.55') → "2.55"
TypeScript:  "2.55" → apresentado diretamente
PostgreSQL:  NUMERIC → devolvido via ::text
```

---

## Casas licenciadas SRIJ (Portugal)

Betclic · Bwin · ESCOnline · PokerStars · Casino Portugal · Solverde · Nossa Aposta · Placard · Luckia · 888 · Betano · Moosh · VS VERSUSBet · Bacanaplay · LeBull · GoldenPark · YOBINGO

> Fonte: [SRIJ — entidades licenciadas](https://www.srij.turismodeportugal.pt/pt/jogos-e-apostas-online/entidades-licenciadas)

---

## Roadmap

- [x] Pipeline core: deteção, matching, arbitragem
- [x] Auth, planos, Stripe e Free Trial
- [x] Dashboard em tempo real, alertas Discord e Telegram
- [x] +EV e Double Chance
- [x] Matched betting (calculadora, wizard, gestor de bónus) e Escola
- [x] Painel de admin, simulador, wizard de execução e `/profits`
- [ ] Banca viva por bookmaker
- [ ] Score de saúde de conta (Bookmaker Health Score)
- [ ] KamiScore + Leaderboard
- [ ] Jogos ao vivo (add-on)
- [ ] Extensão de browser
- [ ] API pública para developers

---

## Contacto

- 💬 [Discord](https://discord.gg/d2awCsJJwu)
- ✉️ kamiodds.infos@gmail.com
- ✉️ tomasafonsoferreiracarvalho@gmail.com
