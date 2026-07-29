# NS-CareSystem — Documentação Técnica Completa
**Versão:** 1.0.0 | **Data:** Maio 2026 | **Autor:** Tiago Assunção de Miranda

---

## 1. Visão Geral do Projeto

### 1.1 O que é o NS-CareSystem?
O **NS-CareSystem** (Northside Care System) é uma plataforma SaaS (Software as a Service) de gestão clínica multi-tenant desenvolvida para profissionais e clínicas de saúde. O sistema centraliza prontuários eletrônicos, controle de medicamentos, agendamentos, faturamento, captação de pacientes e indicadores gerenciais em um único ambiente digital, com suporte a inteligência artificial clínica.

### 1.2 Origem
O projeto iniciou como **Vynzo Care** e passou por um rebrand completo para **NS-CareSystem**, parte da holding **Northside Company** — que visa desenvolver múltiplos ERPs verticais (saúde, construção, arquitetura).

### 1.3 Modelo de Negócio
- **Multi-tenant:** cada clínica/organização tem seus dados isolados
- **SaaS por assinatura:** planos FREE, BASIC, PREMIUM e BLACK
- **Trial de 7 dias:** acesso completo sem cartão de crédito
- **Limites por plano:** pacientes, usuários e funcionalidades

---

## 2. Arquitetura do Sistema

### 2.1 Visão Geral da Arquitetura

```
┌─────────────────────────────────────────────────────────┐
│                    NORTHSIDE COMPANY                    │
│                                                         │
│  ┌──────────────┐    ┌──────────────┐    ┌───────────┐  │
│  │  Frontend    │    │   Backend    │    │  Mobile   │  │
│  │  Next.js 15  │◄──►│  Fastify 5  │◄──►│(em dev)   │  │
│  │  Vercel      │    │  Render      │    │Expo/RN    │  │
│  └──────────────┘    └──────┬───────┘    └───────────┘  │
│                             │                            │
│                    ┌────────▼────────┐                  │
│                    │   PostgreSQL    │                  │
│                    │   Neon (cloud)  │                  │
│                    └─────────────────┘                  │
└─────────────────────────────────────────────────────────┘
```

### 2.2 Stack Tecnológico

#### Backend
| Tecnologia | Versão | Função |
|------------|--------|--------|
| Node.js | 20+ | Runtime |
| TypeScript | 6.x | Tipagem estática |
| Fastify | 5.x | Framework HTTP de alta performance |
| Prisma | 7.x | ORM (Object-Relational Mapping) |
| PostgreSQL | 16 | Banco de dados relacional |
| JWT | - | Autenticação stateless |
| Zod | 4.x | Validação de schemas |
| Bcryptjs | 3.x | Hash de senhas |
| PDFKit | 0.18 | Geração de PDFs |
| Cloudinary | 2.x | Storage de arquivos |
| Stripe | 22.x | Pagamentos e assinaturas |
| Resend | 6.x | Envio de e-mails transacionais |
| Anthropic SDK | 0.90 | Integração com Claude AI |

#### Frontend
| Tecnologia | Versão | Função |
|------------|--------|--------|
| Next.js | 15.x | Framework React com SSR |
| TypeScript | 5.x | Tipagem estática |
| TailwindCSS | 4.x | Estilização utilitária |
| React Query | 5.x | Gerenciamento de estado server-side |
| React Hook Form | 7.x | Formulários performáticos |
| Zod | 4.x | Validação client-side |
| Sonner | 1.x | Notificações toast |
| Lucide React | - | Ícones |
| Axios | 1.x | Cliente HTTP |

#### Infraestrutura
| Serviço | Uso |
|---------|-----|
| Vercel | Deploy do frontend (CDN global) |
| Render | Deploy do backend (free tier) |
| Neon | PostgreSQL serverless na nuvem |
| Cloudinary | Storage de imagens e documentos |
| UptimeRobot | Keep-alive do servidor (ping a cada 5min) |
| Stripe | Pagamentos e webhooks |
| Resend | E-mails transacionais |

---

## 3. Estrutura de Diretórios

```
NORTHSIDE-COMPANY/
└── NS-CareSystem/
    ├── ns-caresystem-backend/
    │   ├── prisma/
    │   │   └── schema.prisma          # Modelos do banco de dados
    │   ├── src/
    │   │   ├── lib/
    │   │   │   ├── ai.ts              # Integração Claude AI
    │   │   │   ├── email.ts           # Templates de e-mail
    │   │   │   ├── prisma.ts          # Cliente Prisma
    │   │   │   ├── stripe.ts          # Configuração Stripe
    │   │   │   └── validators.ts      # Schemas Zod
    │   │   ├── middlewares/
    │   │   │   ├── planos.ts          # Verificação de limites por plano
    │   │   │   └── superadmin.ts      # Guard SUPER_ADMIN
    │   │   ├── routes/
    │   │   │   ├── agendamentos.ts    # CRUD agendamentos + anexos + avaliação
    │   │   │   ├── ai.ts              # Chat com Claude (SSE streaming)
    │   │   │   ├── alertas.ts         # Sistema de alertas
    │   │   │   ├── auth.ts            # Registro, login, refresh, perfil
    │   │   │   ├── captacao.ts        # Links de captação de pacientes
    │   │   │   ├── faturamento.ts     # Cobranças + KPIs financeiros
    │   │   │   ├── kpis.ts            # KPIs consolidados
    │   │   │   ├── medicamentos.ts    # Gestão de medicamentos
    │   │   │   ├── pacientes.ts       # CRUD pacientes
    │   │   │   ├── planos.ts          # Gestão de planos
    │   │   │   ├── prontuario.ts      # Prontuário + PDF
    │   │   │   ├── stripe.ts          # Checkout e webhooks
    │   │   │   ├── superadmin.ts      # Painel superadmin
    │   │   │   ├── upload.ts          # Upload via Cloudinary
    │   │   │   └── usuarios.ts        # Usuários + branding da org
    │   │   └── server.ts              # Configuração principal do servidor
    │   ├── package.json
    │   └── tsconfig.json
    │
    ├── ns-caresystem-frontend/
    │   ├── src/
    │   │   ├── app/
    │   │   │   ├── (auth)/            # Páginas públicas de autenticação
    │   │   │   │   ├── login/
    │   │   │   │   └── register/
    │   │   │   ├── (dashboard)/       # Área autenticada
    │   │   │   │   ├── agendamentos/
    │   │   │   │   ├── alertas/
    │   │   │   │   ├── captacao/
    │   │   │   │   ├── configuracoes/
    │   │   │   │   ├── dashboard/
    │   │   │   │   ├── faturamento/
    │   │   │   │   ├── medicamentos/
    │   │   │   │   ├── pacientes/
    │   │   │   │   ├── planos/
    │   │   │   │   ├── prontuario/
    │   │   │   │   └── relatorios/
    │   │   │   ├── captacao/[token]/  # Formulário público de captação
    │   │   │   ├── precos/            # Pricing page
    │   │   │   └── page.tsx           # Landing page
    │   │   ├── components/
    │   │   │   ├── layout/
    │   │   │   │   ├── sidebar.tsx
    │   │   │   │   ├── ai-chat.tsx
    │   │   │   │   └── cursor-glow.tsx
    │   │   │   └── ui/
    │   │   │       └── FileUpload.tsx # Componente de upload reutilizável
    │   │   └── lib/
    │   │       ├── api.ts             # Cliente Axios configurado
    │   │       ├── auth.tsx           # Context de autenticação
    │   │       └── theme.tsx          # Sistema de temas
    │   └── package.json
    │
    ├── ns-caresystem-mobile/          # Em desenvolvimento
    ├── documentacao/
    └── backups/
```

---

## 4. Banco de Dados — Modelos Prisma

### 4.1 Diagrama de Entidades

```
Organization (1) ──── (N) User
Organization (1) ──── (N) Patient
Organization (1) ──── (N) ClinicalNote
Organization (1) ──── (N) Cobranca
Organization (1) ──── (N) CaptacaoLink
Patient (1) ────────── (N) Medication
Patient (1) ────────── (N) Appointment
Patient (1) ────────── (N) ClinicalNote
Patient (1) ────────── (N) Cobranca
Medication (1) ──────── (N) DoseLog
Appointment (1) ─────── (1) Cobranca
User (1) ────────────── (N) ClinicalNote
User (0..1) ─────────── (N) Patient [médico responsável]
```

### 4.2 Modelos Principais

**Organization**
- Dados da clínica (nome, plano, status, trial)
- Branding (logoUrl, brandingColor, brandingBg, bgImageUrl)
- Integração Stripe (customerId, subscriptionId)

**User**
- Autenticação (email, passwordHash)
- Roles: SUPER_ADMIN, ADMIN, PROFESSIONAL, CAREGIVER
- Dados profissionais (CRM, especialidade)
- Controle de tentativas de login

**Patient**
- Dados pessoais completos
- Responsável legal
- Médico responsável
- Dados de saúde (tipo sanguíneo, alergias, plano de saúde)

**ClinicalNote** (Prontuário)
- Tipos: EVOLUTION, PRESCRIPTION, EXAM, OTHER
- Anexos (URLs Cloudinary)
- Autor com timestamp

**Appointment** (Agendamento)
- Status: SCHEDULED, COMPLETED, CANCELLED, NO_SHOW
- Avaliação NPS (1-5 estrelas + feedback)
- Anexos de laudos/exames

**Cobranca** (Faturamento)
- Status: PENDENTE, PAGO, INADIMPLENTE, CANCELADO
- Anexos de comprovantes

**CaptacaoLink**
- Token único por link
- Contador de acessos
- Data de expiração opcional

---

## 5. API — Endpoints

### 5.1 Autenticação
| Método | Rota | Descrição |
|--------|------|-----------|
| POST | /auth/register | Registrar organização + admin |
| POST | /auth/login | Login (retorna JWT + dados org) |
| POST | /auth/refresh | Renovar token |
| GET | /auth/me | Perfil do usuário logado |
| PATCH | /auth/senha | Alterar senha |

### 5.2 Pacientes
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | /pacientes | Listar pacientes ativos |
| GET | /pacientes/:id | Buscar paciente por ID |
| POST | /pacientes | Cadastrar paciente |
| PUT | /pacientes/:id | Atualizar paciente |
| DELETE | /pacientes/:id | Desativar paciente |

### 5.3 Prontuário
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | /pacientes/:id/prontuario | Listar notas + dados do paciente |
| POST | /pacientes/:id/prontuario | Criar nota clínica |
| DELETE | /prontuario/:noteId | Excluir nota |
| GET | /pacientes/:id/prontuario/pdf | Gerar e baixar PDF |

### 5.4 Upload de Arquivos
| Método | Rota | Descrição |
|--------|------|-----------|
| POST | /upload | Upload para Cloudinary |
| DELETE | /upload/:publicId | Remover arquivo |

### 5.5 KPIs
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | /kpis | KPIs consolidados (comercial, financeiro, NPS, SLA) |
| PATCH | /agendamentos/:id/avaliacao | Registrar avaliação NPS |

### 5.6 Captação
| Método | Rota | Descrição |
|--------|------|-----------|
| GET | /captacao/:token | Dados do link (público) |
| POST | /captacao/:token | Submeter formulário (público) |
| GET | /captacao | Listar links da org |
| POST | /captacao | Criar link |
| PATCH | /captacao/:id | Ativar/desativar |
| DELETE | /captacao/:id | Remover link |

---

## 6. Funcionalidades por Módulo

### 6.1 Dashboard
- Cards de resumo (pacientes, agendamentos, alertas, atendimentos)
- Agenda do dia com próximas consultas
- Alertas recentes
- Indicadores visuais de performance

### 6.2 Prontuário Eletrônico
- Notas clínicas com tipos diferenciados
- Upload de laudos e exames por nota
- Visualização completa dos dados do paciente
- Responsável legal e médico responsável integrados
- **Download de PDF** com histórico completo

### 6.3 KPIs & Indicadores
**Processo Comercial:**
- Total e crescimento de pacientes
- Taxa de presença nas consultas
- Taxa de cancelamento com alertas
- Conversão via links de captação

**Gestão Financeira:**
- Receita mensal, recebido e a receber
- Ticket médio por atendimento
- Taxa de inadimplência
- Cobranças atrasadas com dias de atraso

**Experiência do Cliente (CX):**
- Score NPS (-100 a 100)
- Classificação: promotores, neutros, detratores
- Média de avaliações em estrelas
- Modal de registro de avaliação pós-consulta

**SLA e Tempo de Espera:**
- Alertas visuais: crítico (< 24h), atenção (1-3 dias), ok
- Tempo de espera desde o agendamento
- Cobranças com vencimento ultrapassado

### 6.4 Captação de Clientes
- Geração de links exclusivos por organização
- Página pública com corretor ortográfico nativo
- Cadastro automático no sistema ao submeter
- Controle de acessos e conversão
- Expiração configurável

### 6.5 Identidade Visual
- Upload de logomarca (PNG, JPG, SVG)
- Cor de destaque da marca (picker livre)
- Cor de fundo do sistema (6 opções + picker)
- Imagem de fundo do sistema
- Aplicação em tempo real sem reload

---

## 7. Segurança

### 7.1 Autenticação
- JWT com expiração de 24 horas
- Hash de senhas com bcrypt (salt rounds = 10)
- Bloqueio de conta após tentativas inválidas
- Rate limiting: 100 requisições/minuto por IP

### 7.2 Autorização
- Guards por role em todas as rotas protegidas
- Multi-tenancy: isolamento por `organizationId` em todas as queries
- Verificação de limites por plano antes de criar recursos

### 7.3 CORS
- Origens permitidas configuráveis via variável de ambiente
- Métodos explícitos: GET, POST, PUT, PATCH, DELETE, OPTIONS
- Headers permitidos: Content-Type, Authorization

### 7.4 Dados
- LGPD compliance declarado
- Dados de pacientes isolados por organização
- Arquivos armazenados no Cloudinary com URLs únicas

---

## 8. Planos e Limites

| Plano | Preço | Pacientes | Usuários | Alertas |
|-------|-------|-----------|----------|---------|
| FREE | Grátis (7 dias trial) | 5 | 1 | 10 |
| BASIC | R$ 59,99/mês | 50 | 1 | 10 |
| PREMIUM | R$ 110/mês | Ilimitados | 3 | Ilimitados |
| BLACK | R$ 199,99/mês | Ilimitados | Ilimitados | Ilimitados |

**Desconto anual:** 20% em todos os planos pagos.

---

## 9. Melhorias e Evoluções Realizadas

### 9.1 Rebrand Completo
- Vynzo Care → NS-CareSystem
- Renomeação em todo o código, GitHub, Render, Vercel, Neon, Docker, Stripe
- Criação da estrutura NORTHSIDE-COMPANY

### 9.2 Landing Page Profissional
- Hero com stats (10k+ profissionais, 500k+ pacientes, 99.9% uptime)
- Seções: problema → features → como funciona → planos → depoimentos → FAQ
- Pricing page separada com comparativo completo
- Deploy automático via Vercel + GitHub

### 9.3 Prontuário Completo
- Responsável legal integrado
- Médico responsável vinculado
- Geração de PDF completo com todos os dados
- Anexo de arquivos por nota clínica

### 9.4 Upload Universal de Arquivos
- Integração com Cloudinary
- Componente `FileUpload` reutilizável com drag & drop
- Integrado em: Prontuário, Agendamentos, Faturamento
- Suporte a PDF, imagem, Word, Excel (máx 10MB)

### 9.5 Captação de Clientes
- Sistema de links exclusivos com token UUID
- Formulário público responsivo com corretor ortográfico nativo
- Dados completos: pessoais, plano de saúde, responsável legal
- Cadastro automático no banco da organização

### 9.6 KPIs Consolidados
- Endpoint `/kpis` com dados em tempo real
- 4 dimensões: Comercial, Financeiro, CX/NPS, SLA
- Avaliação NPS pós-consulta (1-5 estrelas)
- Alertas visuais de SLA com código de cores

### 9.7 Identidade Visual Personalizável
- Upload de logo, cor de marca, cor/imagem de fundo
- Persistência no banco (Organization.logoUrl, brandingColor, etc.)
- Aplicação em tempo real no dashboard

### 9.8 Performance e Disponibilidade
- UptimeRobot: ping a cada 5 minutos no `/health`
- Pre-warm no login: acorda o servidor antes do usuário clicar
- Indicador de status do servidor (verificando/acordando/online)
- CORS configurado com métodos explícitos

---

## 10. Variáveis de Ambiente

### Backend (Render)
```env
DATABASE_URL=postgresql://...
JWT_SECRET=...
PORT=3333
NODE_ENV=production
FRONTEND_URL=https://vynzo-frontend.vercel.app
STRIPE_SECRET_KEY=sk_...
STRIPE_WEBHOOK_SECRET=whsec_...
STRIPE_PRICE_BASIC=price_...
STRIPE_PRICE_PREMIUM=price_...
STRIPE_PRICE_BLACK=price_...
ANTHROPIC_API_KEY=sk-ant-...
RESEND_API_KEY=re_...
CLOUDINARY_CLOUD_NAME=...
CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...
```

### Frontend (Vercel)
```env
NEXT_PUBLIC_API_URL=https://vynzo-backend.onrender.com
```

---

## 11. Repositórios GitHub

| Repositório | Descrição | Visibilidade |
|-------------|-----------|-------------|
| TiagoAM-tech/ns-caresystem-backend | API Fastify | Privado |
| TiagoAM-tech/ns-caresystem-frontend | App Next.js | Privado |

---

## 12. Próximos Passos

### Em Desenvolvimento
- **NS-CareSystem Mobile:** App React Native + Expo para pacientes
  - Login, Dashboard de saúde, Agendamentos, Telemedicina, Perfil

### Planejado
- **NS-Construtech:** ERP para construtoras e imobiliárias
- **NS-ArqSystem:** ERP para escritórios de arquitetura
- Ativação do Stripe em produção (requer CNPJ)
- Domínio personalizado: ns-caresystem.com
- Configuração do Resend com domínio próprio

---

*Documento gerado em Maio de 2026 — Northside Company*
*Desenvolvido por Tiago Assunção de Miranda*
