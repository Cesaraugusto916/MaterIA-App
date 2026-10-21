# Assistente IA Engefast (v3.6.1)
> **Copiloto Desktop Corporativo, Motor RAG Especialista em Normas ABNT/NBR e Telemetria Operacional 360° para Engenharia Predial e Modelagem BIM**

<div align="center">
  <video src="./assets/demo.mp4" width="100%" controls autoplay muted loop></video>
</div>

<p align="center">
  <img src="https://img.shields.io/badge/version-3.6.1-blue.svg?style=for-the-badge" alt="Version 3.6.1">
  <img src="https://img.shields.io/badge/python-3.12-3776AB.svg?style=for-the-badge&logo=python&logoColor=white" alt="Python 3.12">
  <img src="https://img.shields.io/badge/platform-Windows%2010%20%7C%2011%20(x64)-0078D6.svg?style=for-the-badge&logo=windows&logoColor=white" alt="Windows x64">
  <img src="https://img.shields.io/badge/rag-ChromaDB%20%2B%20FlashRank-8A2BE2.svg?style=for-the-badge" alt="RAG ChromaDB FlashRank">
  <img src="https://img.shields.io/badge/storage-SQLite%20WAL%20(Offline--First)-orange.svg?style=for-the-badge" alt="Storage SQLite WAL">
  <img src="https://img.shields.io/badge/compliance-LGPD%20Sanitized-10b981.svg?style=for-the-badge" alt="LGPD Compliance">
</p>

---

## 📋 Resumo Executivo

Na engenharia de instalações prediais (Elétrica, Hidráulica, Climatização/HVAC e Proteção contra Incêndio), engenheiros e projetistas navegam constantemente por dezenas de normas técnicas da ABNT que somam dezenas de milhares de páginas. O dimensionamento incorreto resultante da adoção inadvertida de normas antigas ou revogadas (como a citação da cancelada NBR 6401 em vez da NBR 16401, ou da NBR 7198 unificada na NBR 5626) acarreta não-conformidades legais críticas, reprovações em órgãos reguladores e retrabalho executivo de alto custo financeiro.

O **Assistente IA Engefast** foi concebido para erradicar essa vulnerabilidade. Executando em segundo plano de forma 100% silenciosa na bandeja do sistema (**System Tray**) do Windows, a aplicação opera como um copiloto em tempo de execução para modelagem CAD/BIM e elaboração de memoriais técnicos. O software integra um motor RAG híbrido de alta performance com mais de 215.000 chunks indexados, interface flutuante sob o cursor via atalho duplo universal (`Ctrl+C+C`), persistência transacional com *self-healing* e telemetria passiva de produtividade sob estrita conformidade com a LGPD.

A arquitetura adota uma abordagem *offline-first* com buffer local em SQLite (modo WAL), comunicação TLS Keep-Alive com modelos de ponta do Google Gemini e Groq, re-ranqueamento neural com FlashRank e sincronização em lotes hierárquicos para o Data Lake da rede corporativa a cada 15 minutos, assegurando disponibilidade ininterrupta mesmo sob oscilações de conectividade de rede ou VPN.

---

## 🏛️ Decisões Arquiteturais Chave

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                                 ESTAÇÃO DE TRABALHO WINDOWS                             │
│                                                                                        │
│   [AutoCAD / Revit / Word] ──(Duplo Ctrl+C+C)──► [Hook Global Win32 (pystray/keyboard)]│
│                                                          │                             │
│                                                          ▼                             │
│                                                [Menu Popup Flutuante]                  │
│                                                          │                             │
│                   ┌──────────────────────────────────────┴──────────────────────┐      │
│                   ▼                                                             ▼      │
│        [Corretor / Redator IA]                                        [Motor RAG NBR]  │
│        (Gemini 3.5 Flash Lite)                                        (HTTP Port 8090) │
│                   │                                                             │      │
│                   │                                      ┌──────────────────────┤      │
│                   │                                      ▼                      ▼      │
│                   │                             [ChromaDB HNSW]        [SQLite Filter] │
│                   │                             (215k Vetores - 15ms)  (Vigência ABNT) │
│                   │                                      └──────────┬───────────┘      │
│                   │                                                 ▼                  │
│                   │                                        [FlashRank Re-Rank]         │
│                   │                                                 ▼                  │
│                   │                                       [Streaming SSE + Alerta]     │
│                   ▼                                                 │                  │
│    [Injeção Atômica no Cursor] ◄────────────────────────────────────┘                  │
│    (SetForegroundWindow + Ctrl+V)                                                      │
│                                                                                        │
│ ────────────────────────────────────────────────────────────────────────────────────── │
│                                  TELEMETRIA & RESILIÊNCIA                              │
│                                                                                        │
│   [Monitor 360° CAD/BIM] ──(Sanitização LGPD)──► [SQLite WAL Local]                   │
│                                                         │                              │
│                                      (Sync Lote 15 min tolerante a falhas)             │
│                                                         ▼                              │
│                                            [Data Lake Corporativo UNC]                 │
│                                            (ANO=YYYY/MES=MM/DIA=DD/*.jsonl)            │
│                                                         ▼                              │
│                                            [Telemetry Dashboard Chart.js]              │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

### 1. Motor RAG Híbrido com Controle Regulatório de Vigência & Injeção Tabular
* **Base Vetorial Massiva:** 215.024 chunks indexados com `models/gemini-embedding-001` (768 dimensões) em ChromaDB com índice denso HNSW.
* **Busca Híbrida em Tempo Recorde (14-20 ms):** O ChromaDB recupera 45 candidatos iniciais em ~15 milissegundos, enquanto filtros no SQLite validam a existência do código da norma citada diretamente sem overhead de varredura `LIKE` em tabelas não indexadas.
* **Re-ranqueamento Neural Cross-Encoder:** A biblioteca FlashRank pondera a similaridade contextual entre a pergunta e os chunks recuperados, isolando o Top-4 ótimo para síntese.
* **Camada Mandatória de Conformidade Normativa:** Quando uma consulta envolve normas revogadas ou canceladas (ex.: NBR 6401 substituída pela NBR 16401; NBR 7198 unificada na NBR 5626; NBR 13932 substituída pela NBR 15526), o motor injeta compulsoriamente um banner de alerta regulatório e fundamenta a resposta estritamente na norma vigente.
* **Preservação de Tabelas de Dimensionamento:** Tabelas complexas de ampacidade de cabos, vazões de ar exterior e unidades de Hunter (UHC) são preservadas em Markdown íntegro, acompanhadas de cabeçalho semântico com a página exata do PDF oficial para auditoria do projetista.

### 2. Copiloto Desktop Universal (`Ctrl+C+C`)
* **Ativação Instantânea Sem Troca de Contexto:** Ao selecionar qualquer texto em softwares técnicos (AutoCAD, Revit, Excel, Word, navegadores ou Teams) e pressionar `Ctrl+C` duas vezes rapidamente, um menu suspenso nativo surge sob o cursor do mouse.
* **10 Comandos Especializados:**
  1. *Corrigir Ortografia & Gramática* (preservação estrita de vocabulário técnico)
  2. *Reescrever Parecer* (aprimoramento de clareza e sintaxe)
  3. *Tornar mais Curto* (concisão objetiva)
  4. *Tornar mais Longo* (expansão fundamentada)
  5. *Tom Profissional / Formal* (estilo corporativo para laudos)
  6. *Tom Amigável / Casual* (comunicação de equipe)
  7. *Tom Persuasivo* (propostas comerciais de engenharia)
  8. *Resumir* (síntese executiva)
  9. *Traduzir para Inglês* (especificações internacionais)
  10. *Traduzir para Português* (catálogos de fabricantes estrangeiros)
* **Injeção Atômica via Win32:** Após o processamento via TLS Keep-Alive (`gemini-3.5-flash-lite`), o sistema restaura o foco da janela de origem (`win32gui.SetForegroundWindow`) e injeta o texto gerado diretamente no documento sem necessidade de copiar e colar manual.

### 3. Telemetria de Produtividade 360° (Offline-First & LGPD Compliant)
* **Monitoramento Passivo de Projetos:** Reconhecimento heurístico de arquivos de engenharia em primeiro plano (`.dwg`, `.dxf`, projetos `.rvt` com vistas/pranchas ativas no Revit).
* **Detecção Confiável de Inatividade:** Registro de pausas após 180 segundos sem eventos de teclado/mouse via `GetLastInputInfo` e detecção de tela bloqueada (`SwitchDesktop`).
* **Privacidade Absoluta (LGPD):** Não realiza capturas de tela (*No Screenshots*) e não grava digitações (*No Keylogging*). URLs confidenciais, termos bancários e sessões anônimas (*InPrivate/Incognito*) são mascarados na origem antes da gravação em disco.
* **Buffer Transacional & Sincronização Assíncrona:** Gravação em SQLite local (modo WAL). A cada 15 minutos, um motor em segundo plano descarrega os logs para o servidor de rede em formato JSON Lines particionado por data (`ANO=YYYY/MES=MM/DIA=DD/`), alimentando o **Telemetry Dashboard** com gráficos interativos em Chart.js.

### 4. Engenharia de Resiliência (*Self-Healing*) e Governança
* **Escrita Atômica contra Quedas de Energia:** Operações de salvamento de arquivos de configuração utilizam fluxo determinístico: escrita em arquivo temporário `.tmp`, descarte de buffer com `flush()`, sincronização física com o disco via `os.fsync()` e substituição atômica via `os.replace()`.
* **Auto-Cura de Configurações:** Arquivos danificados por desligamentos abruptos do Windows (bytes nulos `\x00`) são automaticamente detectados, isolados como `.corrompido` e recriados com parâmetros saudáveis, garantindo inicialização 100% à prova de falhas.
* **Governança Dinâmica e Bot Recall.ai:** Controle centralizado via `governanca.json` no servidor corporativo. Estações homologadas na Golden List contam com acesso para despachar bots de gravação e ata de reuniões integrados à API da Recall.ai.

---

## 📂 Estrutura do Repositório

```text
assitenteIA-engefast/
├── assets/
│   └── demo.mp4                    # Demonstração técnica em vídeo Full HD (1080p)
│
├── main.py                         # Ponto de entrada, mutex Win32, loop Tkinter e tray icon
├── config_manager.py               # Gerenciador de configurações com escrita atômica e Self-Healing
├── keyboard_listener.py            # Hook global Win32 para acionamento do duplo Ctrl+C
├── llm_service.py                  # Conexão TLS Keep-Alive com modelos Gemini e Groq
├── security.py                     # Governança corporativa, controle de Golden List e Kill Switch
│
├── rag_poc_nbrs.py                 # Motor RAG híbrido: ChromaDB, HNSW, SQLite e FlashRank
├── servidor_poc_web.py             # Servidor HTTP Turbo (porta 8090) com streaming SSE
├── Assistente_IA_Engefast.html     # Interface web moderna para consulta de normas
├── quota_tracker.py                # Gestão inteligente de quotas de embedding e failover
│
├── activity_monitor.py             # Telemetria 360° em softwares CAD/BIM e mascaramento LGPD
├── storage_manager.py              # Banco relacional local Offline-First em SQLite WAL
├── network_sync.py                 # Motor de sincronização em lote (15 min) para rede corporativa
├── bot_service.py                  # Integração com Recall.ai para bot gravador de reuniões
├── updater.py                      # Auto-updater atômico com validação de ZIP e rollback
├── wallpaper_manager.py            # Aplicação one-time do papel de parede institucional
│
├── tools/
│   └── telemetry_dashboard/        # Dashboard executivo (Chart.js, KPIs, Day View e exportação CSV)
│       ├── app.py                  # Servidor analítico de telemetria
│       ├── parser.py               # Parser de logs JSONL particionados
│       └── templates/              # Interface do dashboard executivo
│
├── build_and_clean.py              # Pipeline automatizado de empacotamento PyInstaller
├── compilar.bat                    # Script em lote para compilação com 1-clique
├── config.example.ini              # Template de configuração de produção
├── governanca.json                 # Definição centralizada de políticas e máquinas autorizadas
├── requirements.txt                # Dependências Python homologadas
├── DOCUMENTACAO_TECNICA.md         # Especificação detalhada de engenharia de software
└── INSTRUCOES_OPERACAO_MOTOR_RAG.md # Diretrizes normativas e System Prompt do motor RAG
```

---

## 🚀 Como Executar e Compilar

### 1. Pré-requisitos
* **Sistema Operacional:** Windows 10 ou 11 (64 bits).
* **Python:** Versão 3.12 (ou superior).

### 2. Execução em Ambiente de Desenvolvimento
```powershell
# Clone o repositório
git clone https://github.com/Cesaraugusto916/assitenteIA-engefast.git
cd assitenteIA-engefast

# Crie e ative o ambiente virtual
python -m venv venv
.\venv\Scripts\activate

# Instale as dependências
pip install -r requirements.txt

# Inicialize o Assistente
python main.py
```

### 3. Inicialização do Servidor RAG de Normas Técnicas
```powershell
# Inicia o servidor HTTP de busca normativa na porta 8090
python servidor_poc_web.py
```
Acesse a interface no navegador em `http://localhost:8090` ou utilize o atalho **"Pergunte à IA (RAG NBR)"** no menu da bandeja do Windows.

### 4. Compilação para Distribuição Corporativa
Para gerar o executável standalone com ícones embutidos e pacotes de auto-atualização para rede:
```powershell
python build_and_clean.py 3.6.1
```
O script gera o executável em `dist/`, empacota os arquivos de staging em `dist_staging/v3.6.1/` e cria o arquivo de atualização delta `update_3.6.1.zip`.

---

## 🛡️ Privacidade e Conformidade LGPD
O Assistente IA Engefast foi projetado segundo os princípios de *Privacy by Design*:
* **Ausência de Captura de Tela:** Nenhum screenshot ou buffer gráfico é gravado ou transmitido.
* **Ausência de Keylogging:** O teclado é monitorado exclusivamente para detecção da combinação específica `Ctrl+C+C` e tempo de ociosidade através da API `GetLastInputInfo`.
* **Sanitização de URLs na Origem:** URLs de páginas da web são higienizadas para remover tokens, sessões e parâmetros sensíveis antes de qualquer persistência em disco.

---

## 👨‍💻 Autor & Responsabilidade Técnica
* **Engenheiro Responsável:** César Carneiro
* **Organização:** Engefast Projetos de Engenharia
* **Licença:** Corporativa / Proprietária
