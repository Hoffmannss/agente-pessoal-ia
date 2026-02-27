# 🧠 FASE 1 — O Cérebro: LLM Local

> **Status:** 🔴 Próximo passo | **Fase atual do projeto**

Este guia ensina como instalar, configurar e conectar um modelo de linguagem (LLM) rodando 100% no seu computador, sem depender de APIs externas pagas.

---

## Por que LLM Local?

| Aspecto | LLM Local (Ollama) | API Externa (OpenAI) |
|---|---|---|
| Custo | Gratuito após hardware | Pago por token |
| Privacidade | 100% no seu PC | Dados saem para núvem |
| Velocidade | Depende do hardware | Depende da internet |
| Controle | Total | Limitado |
| Offline | Sim | Não |

---

## Passo 1.1 — Instalar o Ollama

Ollama é o gerenciador de LLMs locais mais fácil de usar. Funciona como um "app store" de modelos de IA.

### Windows
```bash
# Opção 1: Download direto
# Acesse: https://ollama.ai/download e baixe o instalador .exe

# Opção 2: Via winget
winget install Ollama.Ollama
```

### Verificar instalação
```bash
ollama --version
# Deve retornar algo como: ollama version 0.x.x

# Verificar se o serviço está rodando
ollama list
```

---

## Passo 1.2 — Escolher o Modelo Ideal

A escolha do modelo depende do seu hardware. Use esta tabela:

### Por Hardware (RAM)

| RAM Disponível | Modelo Recomendado | Tamanho | Qualidade |
|---|---|---|---|
| 8 GB | `mistral:7b-instruct-q4_0` | ~4 GB | Boa |
| 8 GB | `llama3.2:3b` | ~2 GB | Boa |
| 16 GB | `mistral:7b-instruct` | ~7 GB | Ótima |
| 16 GB | `llama3.1:8b` | ~8 GB | Ótima |
| 32 GB | `mixtral:8x7b` | ~26 GB | Excelente |
| 32 GB+ | `llama3.1:70b-q4` | ~40 GB | Elite |

### Modelos com GPU (NVIDIA)

| VRAM | Modelo | Observação |
|---|---|---|
| 6 GB | `mistral:7b-q4` | Bom custo-benefício |
| 8 GB | `llama3.1:8b` | Recomendado |
| 12 GB | `mistral:7b` + `llama3.1:8b` | Qualidade alta |
| 24 GB | `mixtral:8x7b` | Profissional |

### Recomendação para Agente Pessoal

**Se você tem 16 GB de RAM ou 8 GB VRAM:**
```
Modelo principal: llama3.1:8b
Modelo rápido (tarefas simples): llama3.2:3b
```

**Se você tem 32 GB de RAM ou 16 GB VRAM:**
```
Modelo principal: mixtral:8x7b OU llama3.1:70b-q4
```

---

## Passo 1.2b — Baixar o Modelo

```bash
# Baixar modelo recomendado (substitua pelo modelo escolhido)
ollama pull llama3.1:8b

# OU para PCs menos potentes:
ollama pull mistral:7b-instruct-q4_0

# OU o mais leve para testes:
ollama pull llama3.2:3b

# Ver modelos instalados:
ollama list

# Testar o modelo direto no terminal:
ollama run llama3.1:8b
# Digite sua mensagem e pressione Enter
# Ctrl+D para sair
```

---

## Passo 1.3 — Conectar Ollama ao OpenClaw

### Via Interface do OpenClaw

1. Abra o OpenClaw no navegador (geralmente `http://localhost:3000`)
2. Vá em **Settings** (Configurações)
3. Clique em **Credentials** ou **Connections**
4. Clique em **+ Add Credential**
5. Selecione **Ollama** como provider
6. Preencha:
   - **URL:** `http://localhost:11434`
   - **Nome:** `ollama-local`
7. Clique em **Test Connection** — deve aparecer `Connected`
8. Salve

### Configurar o Agente para usar o Ollama

1. No OpenClaw, vá ao seu agente (ou crie um novo)
2. Em **AI Model**, selecione **Ollama**
3. Escolha o modelo baixado (ex: `llama3.1:8b`)
4. Salve as configurações

### Verificar se está funcionando
```bash
# No terminal, verifique se o Ollama está respondendo:
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.1:8b",
  "prompt": "Olá, tudo bem?",
  "stream": false
}'
```

---

## Passo 1.4 — Testar via Telegram

1. Abra seu Telegram
2. Envie uma mensagem para o seu bot
3. O bot deve responder usando o LLM local
4. Verifique o tempo de resposta — primeiras respostas podem ser lentas (modelo carregando)

**Mensagens de teste sugeridas:**
- "Olá, quem é você?"
- "Qual a capital do Brasil?"
- "Faça um resumo rápido sobre produtividade"

---

## Passo 1.5 — Ajustar Parâmetros

### Parâmetros importantes no OpenClaw

```json
{
  "temperature": 0.7,
  "top_p": 0.9,
  "max_tokens": 2048,
  "context_window": 8192
}
```

| Parâmetro | Valor Baixo | Valor Alto | Recomendado para Agente |
|---|---|---|---|
| `temperature` | Respostas previsíveis | Respostas criativas | 0.6 – 0.8 |
| `top_p` | Mais focado | Mais diverso | 0.9 |
| `max_tokens` | Respostas curtas | Respostas longas | 1024 – 2048 |
| `context_window` | Memória curta | Memória longa | 8192+ |

### System Prompt Inicial (Temporário)

Enquanto não criamos o System Prompt completo na Fase 4, use este como ponto de partida:

```
Você é um assistente pessoal inteligente e proativo. 
Você fala sempre em português do Brasil.
Você é direto, objetivo e útil.
Você se chama [NOME DO AGENTE - definir na Fase 4].
O usuário se chama Hoffmannss.
Ajude sempre com clareza e precisão.
```

---

## Problemas Comuns e Soluções

### Ollama não responde
```bash
# Iniciar o serviço manualmente:
ollama serve

# Verificar se a porta está em uso:
netstat -ano | findstr :11434
```

### Resposta muito lenta
- Verifique se o modelo cabe na sua RAM/VRAM
- Use uma versão quantizada (ex: `q4_0` ao invés de `q8`)
- Feche outros programas pesados

### OpenClaw não conecta ao Ollama
- Confirme que o Ollama está rodando: `ollama list`
- Verifique o firewall do Windows
- URL correta: `http://localhost:11434` (não `https`)

---

## Checklist de Conclusão da Fase 1

- [ ] Ollama instalado e rodando
- [ ] Modelo baixado e testado no terminal
- [ ] Ollama conectado ao OpenClaw
- [ ] Bot do Telegram respondendo com LLM local
- [ ] Parâmetros ajustados
- [ ] System prompt temporário configurado

Quando todos estiverem marcados ✅, você está pronto para a **Fase 2: Memória do Agente**!

---

## Recursos Adicionais

- [Site oficial do Ollama](https://ollama.ai)
- [Lista completa de modelos Ollama](https://ollama.ai/library)
- [Comparativo de modelos - Open LLM Leaderboard](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard)
- [Documentação OpenClaw - Credentials](https://docs.openclaw.io)

---

*Próximo: [Fase 2 — Memória do Agente](02-memoria.md)*
