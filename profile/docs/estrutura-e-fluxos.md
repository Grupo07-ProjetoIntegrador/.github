# 02. Mapeamento de Interfaces e Fluxos Detalhados

Esta seção vinculas as telas do protótipo aos componentes de codificação e fluxos de dados de background.

## 📌 Sumário
1. [Módulo 1: Perfil, Configurações e Infraestrutura de Testes](#módulo-1-perfil-configurações-e-infraestrutura-de-testes)
2. [Módulo 2: Parametrização e Configuração de Geofencing](#módulo-2-parametrização-e-configuração-de-geofencing)
3. [Módulo 3: Ciclo de Vida de Treinamentos e Geração de Links](#módulo-3-ciclo-de-vida-de-treinamentos-e-geração-de-links)
4. [Módulo 4: Portaria Digital e Validação Física (Check-In Móvel)](#módulo-4-portaria-digital-e-validação-física-check-in-móvel)
5. [Módulo 5: Disparo de Campanhas e Comunicação Transacional](#módulo-5-disparo-de-campanhas-e-comunicação-transacional)
6. [Módulo 6: Dashboard Analítico de Engajamento](#módulo-6-dashboard-analítico-de-engajamento)
7. [Módulo 7: Engine de Emissão de PDFs Corporativos](#módulo-7-engine-de-emissão-de-pdfs-corporativos)

---

## Módulo 1: Perfil, Configurações e Infraestrutura de Testes

Este módulo centraliza o painel de controle operacional do desenvolvedor e o status das conexões do ecossistema.

### Fragmento do código

```mermaid
sequenceDiagram
    actor Dev as Desenvolvedor / RH
    participant UI as Interface (Perfil/Configurações)
    participant Go as Backend (Go)
    
    Dev->>UI: Clica em "Simular Confirmação Local"
    UI->>Go: POST /api/debug/modo-local (true)
    Go-->>UI: HTTP 200 Modo Simulação Ativado
    Note over Go: Ignora validações complexas de DNS externo<br/>durante testes locais.
```

### Detalhamento da Interface

* **Seção de Debug Técnico:** Contém controles contextuais explícitos para alternar estados da aplicação local em localhost, expondo os botões:
  * **"Simular confirmação local":** Ativa uma flag no Core Go para forçar bypass em validações externas de ambiente e agilizar testes unitários.
  * **"Desativar modo de teste local":** Retorna a API para o comportamento padrão de produção.
* **Card Google Connect API:** Exibe o status da ponte de dados com o Workspace. Quando o fluxo de autenticação via OAuth 2.0 é finalizado pelo `oauth_handler.go`, a interface exibe o badge "Canal Ativo" em tom verde junto à conta administradora vinculada.
<img width="605" height="638" alt="image" src="https://github.com/user-attachments/assets/55402a8e-ac28-49b4-8227-4ff71f53cb34" />
<img width="1024" height="712" alt="image" src="https://github.com/user-attachments/assets/48a68967-51bc-4f5d-b578-44e12aa82117" />
<img width="1323" height="851" alt="image" src="https://github.com/user-attachments/assets/0bd6bbcd-6a7d-401d-bbc4-a0363ba0d52a" />
<img width="873" height="945" alt="image" src="https://github.com/user-attachments/assets/0310e4c2-90f0-42f8-ad71-8c9a196fc3fc" />


---

## Módulo 2: Parametrização e Configuração de Geofencing

Módulo responsável por gerenciar os limites geográficos e as cercas virtuais utilizadas no controle antifraude de presença.

### Fragmento do código

```mermaid
graph LR
    A[Painel de Mapas] -->|Salva Latitude/Longitude| B(Go API: presenca_handler)
    B -->|Persiste Cerca| C[(Supabase - Tabela geofences)]
```

### Detalhamento da Interface

* **Painel de Controle Geográfico:** Interface equipada com mapa interativo que lista os locais homologados do shopping e seus respectivos raios de tolerância.
* **Componentes de Entrada de Dados:** Formulário administrativo para inserção de:
  * **Nome do Local:** Identificação da sala ou praça.
  * **Latitude e Longitude:** Coordenadas decimais geográficas capturadas pelo ponto de âncora.
  * **Raio (Metros):** Definição da cerca métrica. Exemplos configurados: Centro de Aulas Aroeira (Restrição estrita de 150m) e Shopping Flamboyant Geral (Raio expandido de 300m).

<img width="1360" height="838" alt="image" src="https://github.com/user-attachments/assets/3eea04a6-f7ff-48b9-994d-7df346ccbaca" />

---

## Módulo 3: Ciclo de Vida de Treinamentos e Geração de Links

Responsável pela criação de eventos de T&D e o acionamento automático de artefatos externos no Workspace de forma assíncrona.

### Fragmento do código

```mermaid
sequenceDiagram
    autonumber
    actor Admin as RH do Shopping
    participant FE as Frontend (React)
    participant Go as Core Backend (Go)
    participant Py as Worker (Python)
    participant GAPI as Google Forms API

    Admin->>FE: Preenche campos e seleciona a Cerca Virtual
    FE->>Go: POST /api/treinamentos (Payload JSON)
    Go->>Py: HTTP POST Assíncrono (Task Queue)
    Go-->>FE: HTTP 201 Created (Libera interface humana)
    Py->>GAPI: Cria estrutura do questionário de inscrição
    GAPI-->>Py: Retorna ID do Form e URL pública
    Py->>Go: Atualiza registro com links gerados
```

### Detalhamento da Interface

* **Formulário de Inclusão:** Recebe o Tema do treinamento (ex: Manejo Seguro de GLP e Redes de Gás), Facilitador, Data/Hora, Vagas Máximas, Segmento-Alvo e a seleção obrigatória da Cerca Virtual parametrizada.
* **Tabela de Listagem:** Lista os treinamentos com paginação indexada, contadores de registros cadastrados e a volumetria de vagas ocupadas.
* **Painel de Links e Ações:** Exibe os resultados finais do processamento do `forms_handler.py`:
  * **Link Externo do Google Forms:** Rota para o lojista responder à inscrição.
  * **Link de Auto-Presença:** Endereço dinâmico (`/autocheckin/[ID]`) associado ao QR Code projetado.

  <img width="1600" height="791" alt="image" src="https://github.com/user-attachments/assets/e68d2f13-78fb-461c-a688-cf7d5a9a5308" />
  <img width="1600" height="633" alt="image" src="https://github.com/user-attachments/assets/f7954c6d-f2f2-4d72-8c68-965c226a9924" />
  <img width="1600" height="800" alt="image" src="https://github.com/user-attachments/assets/73c769b8-ce5e-40fe-8655-5978b711c67f" />
  <img width="626" height="942" alt="image" src="https://github.com/user-attachments/assets/f44ef454-a5c1-4ab7-9bdb-07ff5dd13adb" />
  
---

## Módulo 4: Portaria Digital e Validação Física (Check-In Móvel)

A portaria digital atua na captura e processamento da telemetria móvel para comprovação física de presença, mitigando marcações remotas.

### Fragmento do código

```mermaid
sequenceDiagram
    actor Lojista
    participant Mob as Celular (Navegador)
    participant Go as Core Backend (Go)

    Lojista->>Mob: Lê o QR Code e autoriza GPS
    Mob->>Go: POST /api/presenca/checkin (Lat, Lon, UserID)
    Note over Go: Aplica Cálculo de Haversine
    alt Distância > Raio Permitido
        Go-->>Mob: HTTP 403 Forbidden
        Note over Mob: Renderiza Estado de Erro Crítico (UI Vermelha)
    end
```

### Detalhamento da Interface

* **Interface Responsiva do Cliente:** Acionada pelo celular do funcionário. Invoca nativamente a API de localização do navegador (`navigator.geolocation`).
* **Estado de Erro Crítico:** Se o lojista efetuar a leitura fora da sala de treinamento, o algoritmo trigonométrico implementado no `presenca_handler.go` bloqueia o check-in e a tela exibe o erro em vermelho: *"Geofencing não localizado para este treinamento"*, expondo os metadados de satélite coletados para fins de auditoria.

<img width="1024" height="510" alt="image" src="https://github.com/user-attachments/assets/73b0347e-babd-44cd-89d4-591866912300" />


---

## Módulo 5: Disparo de Campanhas e Comunicação Transacional

Interface de comunicação em massa integrada à API do Gmail para o envio automatizado de instruções e confirmações.

### Fragmento do código

```mermaid
graph TD
    Admin[RH] -->|Seleciona Contatos| Campanha(Interface de Disparo)
    Campanha -->|POST /api/emails| Go(Core Backend)
    Go -->|Background Task| Py(email_service.py)
    Py -->|Gmail API| Lojista[Caixa de Entrada do Lojista]
```

### Detalhamento da Interface

* **Painel de Seleção Coletiva:** Tabela robusta com listagem e filtros de centenas de registros de lojistas e representantes habilitados para o treinamento, permitindo seleção individualizada ou disparo em massa.
* **E-mails Transacionais Emitidos (`email_service.py`):**
  * **E-mail de Convite:** Layout em HTML estruturado com a identidade do Shopping Flamboyant, contendo a data, hora, local, link direto do Google Forms para inscrição e link de portaria.
  * **E-mail de Confirmação:** Disparado automaticamente na caixa de entrada do lojista no segundo exato em que o check-in geolocalizado é validado pelo Go, servindo como recibo digital.

<img width="1600" height="606" alt="image" src="https://github.com/user-attachments/assets/5954d897-17e8-4ca6-b732-dc2cae5b6375" />
<img width="466" height="736" alt="image" src="https://github.com/user-attachments/assets/adb3ff2d-4c71-44a7-88eb-fb1681d48799" />
<img width="527" height="661" alt="image" src="https://github.com/user-attachments/assets/541099a5-bc2a-4e18-be36-b2e9ee4aa2ba" />




---

## Módulo 6: Dashboard Analítico de Engajamento

Central de inteligência de dados voltada para a governança e auditoria de conformidade das franquias do shopping.

### Fragmento do código

```mermaid
graph LR
    DB[(Supabase)] -->|Agregação de Dados| FE[React: DashboardAnalitico.tsx]
    FE -->|Renderiza KPIs| UI[Cards e Rankings de Adesão]
```

### Detalhamento da Interface

* **Cards de Indicadores Macro (KPIs):**
  * **Adesão Média das Lojas:** Métrica percentual geral de frequência do mall.
  * **Lojas Ativas:** Contagem volumétrica de marcas participantes das capacitações.
  * **Engajamento por Segmento:** Gráfico de barras comparativo que identifica a aderência por categoria de comércio (Alimentação, Vestuário, etc.).
* **Tabela de Conformidade por Loja:** Listagem consolidada que exibe a razão social da loja, categoria de mercado, taxa de frequência nominal e botões contextuais de ação direta para exportação do histórico.

<img width="1894" height="940" alt="image" src="https://github.com/user-attachments/assets/cf3a0316-c843-4493-92f0-ab3d2f827270" />
<img width="1864" height="473" alt="image" src="https://github.com/user-attachments/assets/e312da09-3a20-41f8-9eaf-a400d233e28f" />
<img width="1886" height="935" alt="image" src="https://github.com/user-attachments/assets/719fe5ed-04c7-4c2a-abe6-013e1a47881f" />
<img width="1881" height="739" alt="image" src="https://github.com/user-attachments/assets/5384cdc3-ee81-475d-bbfb-f26b22c9530c" />


---

## Módulo 7: Engine de Emissão de PDFs Corporativos

Serviço em Python (`gerar_pdf.py`) responsável por materializar dados estruturados do Supabase in relatórios portáveis de auditoria com formatação e identidade visual.

### 1. Lista de Chamada Oficial (Ata de Treinamento)
Documento analítico emitido por evento de treinamento, gerado sob demanda. Possui alto valor jurídico e de conformidade predial.

* **Cabeçalho e Metadados:** Consolida o Tema (Manejo Seguro de GLP e Redes de Gás), Facilitador (Eng. Roberto Dias), Data/Hora, Carga Horária (2 horas) e Local (Praça de Alimentação).
* **Indicadores de Controle:**
  * **Taxa Geral de Frequência:** Percentual de comparecimento em relação aos inscritos (ex: 85%).
  * **Auditoria de Vagas:** Contador absoluto de presença (ex: 34/40 Presentes vs. Capacidade Máxima).
* **Tabela de Evidências:** Listagem nominal detalhando Nome/Cargo do Representante, Franquia/Loja e o Horário exato do Check-In validado por satélite.

### 2. Dossiê de Performance Consolidado (Histórico por Loja)
Documento analítico temporal focado no rastreamento histórico de conformidade de uma franquia específica em relação às convocações do shopping.

* **Identificação da Unidade:** Exibe o Nome da Loja (ex: ANIMALE), Segmentação (Moda Feminina) e o Período Analisado (01/07/2025 até 31/12/2026).
* **Métrica de Performance:** Destaque visual centralizado indicando a Taxa Geral de Frequência da Loja (ex: 38%), expondo se a franquia cumpre ou negligencia as metas estabelecidas.
* **Histórico Nominal Crônico por Evento:** Tabela matricial detalhada contendo:
  * **Módulo Corporativo / Data:** Nome do treinamento e data de ocorrência.
  * **Representantes Presentes:** Nome do funcionário que validou o check-in físico.
  * **Ausentes ou Pendentes:** Identificação nominal clara dos funcionários convocados que faltaram ou deixaram o treinamento em pendência (ex: Natalia Werner, Tânia Santos Oliveira), fornecendo insumos diretos para cobranças contratuais ou operacionais por parte da administração do shopping.

[dossie_animale_2025-07-01_2026-12-31.pdf](https://github.com/user-attachments/files/28717125/dossie_animale_2025-07-01_2026-12-31.pdf)  
[lista_chamada_manejo_seguro_de_glp_e_redes_de_gás.pdf](https://github.com/user-attachments/files/28717154/lista_chamada_manejo_seguro_de_glp_e_redes_de_gas.pdf)

