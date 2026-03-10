# 📊 Bot de Notícias Crypto — Telegram

Automação que coleta notícias e dados de mercado de criptoativos, analisa com IA e envia um relatório matinal detalhado via Telegram todos os dias às 07:00.

---

## 🚀 O que o bot faz

- Coleta preços em tempo real de **BTC, ETH e SOL** via CoinGecko
- Monitora as **top 20 altcoins** por volume de negociação
- Lê o **Fear & Greed Index** do mercado
- Agrega notícias de **4 fontes**: CoinTelegraph, CoinDesk, Decrypt e Investing.com
- Analisa tudo com **Groq (LLaMA 3.3 70B)** e gera um relatório completo
- Envia o relatório em **2-3 mensagens** no Telegram às 07:00 (horário de Brasília)

---

## 📋 Estrutura do Relatório

Cada relatório diário contém:

1. 🌡️ **Sentimento Geral** — Bullish / Bearish / Neutro + Fear & Greed Index
2. 📊 **Análise de BTC, ETH e SOL** — preço, variação, expectativa 24-72h, risco
3. 🚀 **Altcoins com Potencial** — 3 altcoins justificadas com base em volume e notícias
4. 📰 **Principais Notícias** — 5 notícias mais relevantes e seus impactos
5. ⚠️ **Riscos do Dia** — macro, regulatórios e técnicos
6. 🎯 **Resumo Executivo** — 3 pontos-chave para acompanhar

---

## 🏗️ Arquitetura

```
Trigger (07:00) ──→ CoinGecko BTC/ETH/SOL ──┐
                ──→ CoinGecko Top 20         ├──→ Agrega Dados ──→ Groq IA ──→ Telegram
                ──→ Fear & Greed Index       │
                ──→ RSS CoinTelegraph        │
                ──→ RSS CoinDesk             │
                ──→ RSS Decrypt              │
                ──→ RSS Investing ───────────┘
```

### Serviços

| Serviço | Função |
|---|---|
| **n8n** | Motor de automação — interface e execução dos workflows |
| **n8n Worker** | Processamento paralelo dos workflows |
| **PostgreSQL** | Banco de dados — workflows, credenciais e histórico |
| **Redis** | Fila de jobs entre n8n e o worker |

---

## ⚙️ Pré-requisitos

- [Docker](https://www.docker.com/get-started) e Docker Compose instalados
- Conta no [Groq](https://console.groq.com) (gratuito)
- Bot no Telegram (criado via [@BotFather](https://t.me/BotFather))

---

## 🛠️ Instalação

**1. Clone o repositório**
```bash
git clone https://github.com/seu-usuario/bot_noticiascrypto_telegram.git
cd bot_noticiascrypto_telegram
```

**2. Configure as variáveis de ambiente**
```bash
cp env.example .env
```

Edite o `.env` com suas chaves:

```env
POSTGRES_DB=n8n
POSTGRES_USER=n8n
POSTGRES_PASSWORD=senha_forte_aqui

N8N_USER=admin
N8N_PASSWORD=senha_forte_aqui

GROQ_API_KEY=gsk_...
TELEGRAM_BOT_TOKEN=123456789:ABC-...
TELEGRAM_CHAT_ID=123456789
```

**3. Suba os containers**
```bash
docker compose up -d
```

**4. Acesse o n8n**
```
http://localhost:5678
```

**5. Importe o workflow**
- Menu lateral → **Workflows** → **Import from file**
- Selecione o arquivo `noticias_crypto.json`

**6. Configure as credenciais no n8n**
- Groq: `Credentials` → `Header Auth` → `Authorization: Bearer gsk_...`
- Telegram: adicione o token diretamente na URL do nó

**7. Ative o workflow e teste**
- Clique em **Test workflow** para validar
- Ative o toggle para começar a receber às 07:00

---

## 📁 Estrutura do Projeto

```
bot_noticiascrypto_telegram/
├── docker-compose.yml      # Configuração dos containers
├── env.example             # Modelo de variáveis de ambiente
├── noticias_crypto.json    # Workflow do n8n (importar na interface)
└── README.md
```

---

## 🔑 APIs Utilizadas

| API | Uso | Plano |
|---|---|---|
| [CoinGecko](https://www.coingecko.com/en/api) | Preços e dados de mercado | Gratuito |
| [Alternative.me](https://alternative.me/crypto/fear-and-greed-index/) | Fear & Greed Index | Gratuito |
| [Groq](https://console.groq.com) | Análise com LLaMA 3.3 70B | Gratuito (100k tokens/dia) |
| [Telegram Bot API](https://core.telegram.org/bots/api) | Envio do relatório | Gratuito |

---

## 🔧 Comandos Úteis

```bash
# Iniciar
docker compose up -d

# Parar (mantém os dados)
docker compose stop

# Ver logs
docker compose logs -f n8n

# Ver status
docker compose ps

# Parar e remover containers (mantém volumes)
docker compose down
