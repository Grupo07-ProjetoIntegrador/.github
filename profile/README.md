# Projeto e Arquitetura de Software

Este diretório contém toda a documentação arquitetural, especificações de design e fluxos operacionais do módulo de **Treinamentos e Engajamento** desenvolvido para o ecossistema corporativo do **Shopping Flamboyant**.

O objetivo deste pacote é detalhar a solução técnica poliglota, combinando a alta concorrência do core em **Go** com a flexibilidade da esteira de automações assíncronas em **Python**, garantindo auditoria jurídica rígida de presença por *Geofencing* e integração nativa com o Google Workspace.

---

## 📌 Sumário de Artefatos

Navegue diretamente pelos documentos que compõem a arquitetura do sistema:

1. **[01. Visão Geral e Arquitetura do Sistema](docs/visao-geral.md)**
   * Detalhamento da topologia de quatro camadas (React, Go, Python e Supabase).
   * Justificativa das escolhas tecnológicas (Go para picos de check-in concorrentes e Python para o Google SDK e PDFs).
   * Estratégia de terceirização de UI via Google Forms e webhooks assíncronos.

2. **[02. Mapeamento de Interfaces e Fluxos Detalhados](docs/estrutura-e-fluxos.md)**
   * Diagramas de sequência e fluxogramas em *Mermaid* mapeando o comportamento de cada módulo.
   * Especificações das interfaces de debug local, parâmetros de cercas geográficas, disparo de e-mails em lote pelo Gmail e motor analítico.
   * Regras para geração de Atas Oficiais de Treinamento e Dossiês Crônicos de Performance.

3. **[03. Diretrizes e Decisões de Design de Interface (UI/UX)](docs/decisoes-de-design.md)**
   * Guia de estilo com a paleta de cores institucional baseada no Vinho Flamboyant (`#5C061E`) e Doutado Nobre (`#C5A059`).
   * Escala tipográfica e padrões de acessibilidade WCAG aplicados na engenharia de componentes React (Vite + TypeScript).
   * Anatomia de tabelas com *Sticky Headers*, estados de carregamento (*Skeletons*) e tratamento visual de erros de portaria digital.

---

## 🛠️ Repositórios e Código-Fonte do Protótipo

Todo o código-fonte funcional do ecossistema, incluindo o frontend em React, o core backend em Go e o microsserviço worker em Python, está centralizado na nossa organização oficial do GitHub.

Acesse a lista completa de repositórios do projeto através do link abaixo:

🔗 **[Organização GitHub - Grupo 07 Projeto Integrador](https://github.com/orgs/Grupo07-ProjetoIntegrador/repositories)**
