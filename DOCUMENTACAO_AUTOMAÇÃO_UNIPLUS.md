# Resumo da Integração API Uniplus e Automatização
Data: 16 de Abril de 2026

Este documento detalha toas as melhorias e modificações implementadas para modernizar e automatizar o **Painel de Reposição de Estoque** (Compras-Estoque_Minimo.html), integrando-o através da API do Uniplus via Supabase e disponibilizando online pelo GitHub Pages. 

---

## 1. Supabase Edge Function (`uniplus-estoque-proxy`)
Para evitar exibir credenciais no painel HTML e permitir a conversação segura com os dados, criamos uma Edge Function no Supabase (Projeto: `natubrava-central`).

- **Localização Virtual:** Hospedado no Supabase do usuário sob o endpoint `https://qcxudhpaiqorriclcgyb.supabase.co/functions/v1/uniplus-estoque-proxy`.
- **Modificações Aplicadas:**
  - Foi configurada sem a exigência de JWT (`verify_jwt: false`), permitindo que a própria interface do HTML chamasse a API de forma facilitada.
  - **Tratamento de Dados (CSV):** A API passou a receber informações do Google Sheets (mantidos pela automação já funcional), identificando linhas, processando os totais, estoques de saldo minímos/negativos, custo e preço de venda.
  - **Filtros Avançados:** Processa direto no servidor apenas os itens que tem relevância para o usuário, retornando de forma assíncrona informações de `saldo <= mínimo`.
  - **Fallback Relatório:** Usa parte do fluxo consolidado de Jobs (`/jobs/posicoes-estoque/...`) de outras ferramentas caso decida-se migrar ou necessite uma leitura massiva forçada diretamente do Uniplus Web.

## 2. Modificações na Página `Compras-Estoque_Minimo.html`
Nenhuma funcionalidade que você já usava (como o arrastar as planilhas e a seleção de pasta) foi removida ou danificada. Foram estendimentos modulares cirúrgicos:

- **Novo Botão (UX Premium):** 
  - Criação do botão **"⚡ Importar via API"** ao lado na área principal.
  - Utilizado um design com gradiente elegante, com `box-shadow` profundo combinando com a identidade gráfica nova solicitada.
- **Integração de Barra de Progresso:**
  - Inclusão visual de uma barra de "Conectando..." -> "Recebendo..." -> "Processando..." que informa na tela como está a importação quando a API está processando a busca online.
- **Lógica e Variáveis no Javascript:**
  - Criação da função assíncrona `importarViaAPI()`.
  - O JSON vindo do Supabase é convertido exatamente na mesma estrutura do mapeamento interno que a "Drag & Drop" gerava (objeto `dadosConsolidados`).
  - Adaptação na faixa de status `resumoBar` que agora indica um 'badge' (etiqueta) escrito **"⚡ API ao vivo"** quando o recarregamento dos dados ocorre por lá, ao invés do carimbo de data tradicional do arquivo de planilha manipulada no computador.
- **Manutenção de Filtros:** Atualiza e auto-preenche a busca combinada dos produtos, preservando os metadados existentes da categoria.

## 3. Implantação e Publicação Online
O projeto foi convertido para rodar nativamente via Internet, de qualquer lugar, sem necessitar download da pasta física (ainda pode rodar como fallback).

- **Arquivo Original:** Copiado o script base para um novo arquivo `index.html` tornando-o root page dos servidores.
- **GIT Iniciado:** Comitado usando um script batch direto na sua pasta atual, registrando as ações com o author `Ackerss`.
- **Repositório GitHub:** Anexados todos os arquivos e feito o upload online para o repositório (`https://github.com/Ackerss/buscaencapsuladosesuplementos`). O usuário só precisa configurar a funcionalidade do *GitHub Pages* como instruído para ter acesso ao seu dashboard online do painel via URI do tipo `https://ackerss.github.io/buscaencapsuladosesuplementos/`.

---
*Status das Extensões e Outras Planilhas do Workspace: **Totalmente preservadas e inalteradas***. A conversação do Uniplus não gerou conflitos com a Ferramenta F12 legada, e as integrações antigas continuam funcionais a parte.
