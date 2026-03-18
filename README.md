# 🚀 DJM AI Engine - Technical README

## 1. Visão Geral do Sistema
A **DJM AI Engine** é um ecossistema de *Agentic AI* (IA Agêntica) desenhado para operar como uma corporação digital autónoma. O sistema gere todo o ciclo de vida do software corporativo, desde o faturamento e licenciamento (DRM) até à engenharia reversa de falhas em produção (Self-Healing). 

A arquitetura está dividida em microsserviços integrados através de uma base de dados central (Supabase) e barramentos de status (Trello e GitHub).

---

## 2. A Arquitetura em 4 Pilares

### Pilar I: Gestão, DRM e Financeiro (O Cofre)
Controla o acesso, a faturação e a base de conhecimento global (RAG).
* **Gestão de Clientes e Utilizadores:** CRUD completo com bloqueio de licenciamento por data de vencimento e permissões modulares.
* **Billing Automatizado:** Emissão de faturas e integração com Banco Inter (Bolepix mTLS).
* **Motor RAG:** Ingestão de manuais locais (PDF, TXT) e *web scraping* de documentações online para alimentar o cérebro das IAs.

### Pilar II: Negócios, Produto e Customer Success
Focado em *discovery*, retenção e expansão de receita.
* **Product Management (PM/PO):** Definição de OKRs e quebra autónoma de Épicos em User Stories no Trello.
* **Inteligência de CS:** Geração de Heatmaps de envolvimento e Curvas ABC de uso para prever *Churn* e sugerir *Upsell*.
* **Suporte N1/N2/N3:** Triagem de chamados e resolução técnica autónoma baseada em RAG.

### Pilar III: Software House Autónoma (CI/CD)
A esteira de engenharia que transforma linguagem natural em software em produção.
1.  **Entrevistador:** Recolhe requisitos via chat e aciona a pipeline.
2.  **Documentador:** Gera BRD, SAD, DER e Swagger.
3.  **UI/UX Designer:** Extrai *Brand Guidelines*, gera código Tailwind e tira screenshots multiplataforma (Web, Mobile, Desktop) entregando um *Figma-style Handoff*.
4.  **Fábrica Full-Stack:** Consome o Handoff e o RAG para escrever o código-fonte final e executa o *Push* para o GitHub.
5.  **QA Universal:** Executa testes funcionais robóticos (Playwright, Appium, PyWinAuto) e Code Review rigoroso.

### Pilar IV: Sustentação e SRE (Zero Downtime)
A camada de resiliência que monitoriza o sistema em produção.
* **Host Agents:** SDKs e executáveis locais que monitorizam *logs* e *crashes* na máquina do cliente.
* **APM (Application Performance Monitoring):** Recebe os *stack traces* capturados.
* **Self-Healing:** O Agente SRE faz engenharia reversa do erro, reescreve o código, abre um *Hotfix Pull Request* no GitHub e notifica no Slack.

---

## 3. Dicionário de Ficheiros (Módulos)

| Ficheiro | Função Principal | Core Tech / Libs |
| :--- | :--- | :--- |
| `main_hub.py` | Roteador central. Valida o login e renderiza o menu lateral de navegação unificada. | Streamlit, Supabase |
| `app_admin_financeiro.py` | Painel do CEO. Gestão de DRM, faturas, Bolepix e upload de RAG. | Supabase, BeautifulSoup |
| `app_pm_po.py` | Agente de Produto. Gestão de OKRs, Discovery e criação de cards. | CrewAI, Trello API |
| `app_suporte_cs.py` | Agente de Suporte. Triagem N1, investigação N2 e resolução N3. | CrewAI, Playwright |
| `app_copiloto_rpa.py` | Copiloto Operacional. Executa tarefas repetitivas e gera Curva ABC. | CrewAI, Altair, Pandas |
| `app_entrevistador.py` | Agente de Requisitos. Aciona a pipeline dinâmica baseada nas licenças do cliente. | CrewAI |
| `app_documentador.py` | Arquiteto de Software. Gera a documentação de engenharia em 4 fases. | CrewAI |
| `app_ui_ux.py` | Product Designer. Gera protótipos HTML/Tailwind e *Handoff* técnico. | CrewAI, Playwright |
| `app_mvp.py` | Fábrica de Software. Escreve código (Frontend/Backend) e commita no repositório. | CrewAI, GitHub API |
| `app_qa.py` | QA Universal. Corre testes em Base de Dados, Desktop, Web e Mobile. | CrewAI, SQLAlchemy, Appium |
| `app_devops.py` | Release Manager. Analisa PRs, faz merge ou aplica *Self-Healing* em conflitos. | CrewAI, GitHub API |
| `app_sre_apm.py` | Painel SRE. Lê falhas de produção e gera correções automáticas (*Hotfixes*). | CrewAI, GitHub API |
| `app_smart_agent.py` | *Watchdog* local. Descobre a linguagem do executável e monitoriza logs. | Watchdog, Sockets |
| `djm_cs_tracker.py` | SDK injetável para capturar *tracebacks* em Python e enviar para a cloud. | Sys, Traceback |
| `djm_telemetria.py` | Barramento de logs. Regista o ROI de cada agente e dispara alertas no Slack. | Requests, Supabase |
| `view_dashboard.py` | Dashboard Executivo. Exibe métricas de ROI, Taxa de Sucesso e Volume de Automações. | Plotly, Pandas |
| `run_*.py` | *Wrappers* de inicialização para empacotamento via PyInstaller (Modo Desktop). | Subprocess, Webbrowser |

---

## 4. Stack Tecnológico e Integrações

* **Orquestração de IA:** CrewAI (Processos Sequenciais e Agentes).
* **Modelos LLM:** Google Gemini 2.5 Flash / Pro (via API `generativelanguage`).
* **Frontend/UI:** Streamlit (com customizações em Markdown e Altair/Plotly).
* **Base de Dados & Auth:** Supabase (PostgreSQL).
* **Automação Visual/QA:** Playwright (Web), Appium (Mobile), PyWinAuto (Desktop Windows).
* **Integrações Externas:**
    * **GitHub API:** Para versionamento, Pull Requests e Merges.
    * **Trello API:** Para gestão de fila Kanban (Backlog).
    * **Slack Webhooks:** Para telemetria e alertas de missão crítica.
    * **Banco Inter API:** Para emissão de cobranças.

---
*Documentação gerada de forma autónoma.*
