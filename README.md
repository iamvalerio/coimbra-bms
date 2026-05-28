# Coimbra Editora BMS

Sistema de Gestão de Edifício (BMS) completo para o Edifício Coimbra Editora.

## Sistemas Geridos

| Sistema | Descrição |
|---|---|
| Iluminação | Controlo por zona, dimmer, cenários |
| AVAC | Temperatura por zona, setpoints |
| Fotovoltaico | Produção solar, consumo, métricas |
| Elevadores | Estado, piso atual, direção |
| Elétrico | Painéis, consumo, alarmes |
| SCI | Detetores de fumo, supressão, alarmes |
| Redes Hidráulicas | Consumo de água, pressão, caudal |
| Segurança | Controlo de acessos, câmeras |

## Perfis de Utilizador

| Papel | Permissões |
|---|---|
| ADMIN | Tudo + gestão de utilizadores |
| MANAGER | Todos os sistemas, cenários, relatórios |
| OPERATOR | Controlar dispositivos, ativar cenários |
| VIEWER | Só leitura |
| MAINTENANCE | Acesso a logs e estados técnicos |

## Arranque Rápido

### Pré-requisitos

- [Node.js 20+](https://nodejs.org/) — instalar em https://nodejs.org/en/download
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) — para a base de dados PostgreSQL

### Opção 1 — Desenvolvimento Local

```powershell
# 1. Instalar Node.js (se ainda não instalado)
winget install OpenJS.NodeJS.LTS

# 2. Iniciar a base de dados (requer Docker Desktop)
docker run -d --name coimbra-bms-db `
  -e POSTGRES_USER=bms `
  -e POSTGRES_PASSWORD=bms_secret_2024 `
  -e POSTGRES_DB=coimbra_bms `
  -p 5432:5432 `
  postgres:16-alpine

# 3. Backend
cd C:\Users\mfvalerio\Projects\coimbra-bms\backend
npm install
npx prisma db push
npx ts-node prisma/seed.ts
npm run dev
# Corre em http://localhost:3001

# 4. Frontend (novo terminal)
cd C:\Users\mfvalerio\Projects\coimbra-bms\frontend
npm install
npm run dev
# Abre em http://localhost:5173
```

### Opção 2 — Docker Compose (tudo de uma vez)

```powershell
cd C:\Users\mfvalerio\Projects\coimbra-bms
docker-compose up --build
# Abre http://localhost (frontend) e http://localhost:3001 (API)
```

## Credenciais de Teste

| Email | Password | Papel |
|---|---|---|
| admin@coimbra-bms.pt | Password123! | ADMIN |
| manager@coimbra-bms.pt | Password123! | MANAGER |
| operator@coimbra-bms.pt | Password123! | OPERATOR |
| viewer@coimbra-bms.pt | Password123! | VIEWER |
| maintenance@coimbra-bms.pt | Password123! | MAINTENANCE |

## Estrutura do Projeto

```
coimbra-bms/
├── backend/                 # API Node.js + Express + TypeScript
│   ├── prisma/
│   │   ├── schema.prisma    # Modelos da base de dados
│   │   └── seed.ts          # Dados iniciais
│   └── src/
│       ├── index.ts         # Servidor + Socket.io + Simulador
│       ├── middleware/auth.ts
│       └── routes/          # auth, users, devices, scenarios, alerts, logs, buildings
├── frontend/                # React + TypeScript + Vite + Tailwind
│   └── src/
│       ├── pages/           # Dashboard, Systems, Scenarios, Alerts, Users, Reports
│       ├── components/ui/   # Sidebar, componentes partilhados
│       ├── store/           # Zustand (auth + BMS state)
│       ├── lib/             # API client, Socket.io, mock data
│       └── types/           # TypeScript types
└── docker-compose.yml       # Deploy completo
```

## Funcionalidades

- **Dashboard** em tempo real com KPIs, gráfico de energia, alertas recentes, estado por sistema
- **Controlo de dispositivos** com atualizações via WebSocket (Socket.io)
- **Cenários** manuais, agendados (cron) ou baseados em sensores com condições AND/OR
- **Alertas** com severidade INFO/WARNING/CRITICAL/EMERGENCY e reconhecimento
- **Relatórios** de consumo energético e logs de eventos
- **Simulador** de sensores em tempo real (dados mudam a cada 5s)
- **Responsive** — funciona em desktop e mobile
- **PWA** ready para instalar no telemóvel como app

## Deploy em Cloud

Para deploy em produção (Railway, Render, etc.):
1. Criar uma base de dados PostgreSQL no provider escolhido
2. Atualizar `DATABASE_URL` e `JWT_SECRET` nas variáveis de ambiente
3. Deploy do backend e frontend separadamente ou via Docker Compose
