# CHECKLIST DE TESTES E VALIDAÇÃO

> **Objetivo**: Validar todas as funcionalidades antes de deploy em produção  
> **Criticidade**: 🔴 ALTA - Executar com 100% de cobertura

---

## 📋 Como Usar

1. Abra este arquivo
2. Para cada teste, siga os passos
3. Marque ✅ se passou, ❌ se falhou
4. Se falhar, execute Ação Corretiva
5. Só conclua quando TODOS os testes passarem

---

## 🧪 SEÇÃO 1: SharePoint & Dados

### Teste 1.1: Lista Criada com Colunas Corretas

**Passos:**

1. Vá para SharePoint → Lista `Atividades_Qualidade`
2. Verifique se existem 11 colunas: ID, Solicitante, Projeto, Atividade, Descrição, Responsável, 4x Datas, Status

**Resultado esperado:**

- ✅ Todas as 11 colunas existem
- ✅ Tipos de dados corretos (Número, Choice, Person, Date)
- ✅ Validações configuradas (obrigatórios, defaults)

**Ação corretiva**: Se falhar → voltar para [FASE-1-SHAREPOINT.md](FASE-1-SHAREPOINT.md)

---

### Teste 1.2: Adicionar Item Manualmente

**Passos:**

1. No SharePoint, clique em "+ New"
2. Preencha:
   - Atividade: "Teste Manual"
   - Projeto: "Melhorias"
   - Responsável: (selecione seu usuário)
   - Status: "Não iniciado"
   - 2 datas (qualquer data no futuro)
3. Clique em "Save"

**Resultado esperado:**

- ✅ Item criado com sucesso
- ✅ ID preenchido automaticamente (quando fluxo rodar)
- ✅ Sem erros de validação

---

### Teste 1.3: Permissões do SharePoint

**Passos:**

1. Vá para List Settings → Shared with
2. Verifique: team tem acesso (Contribuidor/Editor)

**Resultado esperado:**

- ✅ Todo o time tem permissão de visualizar e editar
- ✅ Sem bloqueios de acesso

---

## 🎨 SEÇÃO 2: Canvas App - Estrutura

### Teste 2.1: App Criada e Conectada

**Passos:**

1. Abra [Power Apps](https://make.powerapps.com)
2. Procure por `ControleSemanal_Qualidade`
3. Abra em edit mode
4. Verifique data connection (Data → SharePoint)

**Resultado esperado:**

- ✅ App existe e abre sem erro
- ✅ Conectada à lista `Atividades_Qualidade`
- ✅ Sem warnings de conexão

---

### Teste 2.2: 4 Telas Criadas

**Passos:**

1. No editor, vá para "Screens" (lado esquerdo)
2. Verifique: HomePage, FormAtividadeNova, FormAtividadeEditar, DashboardRelatorios

**Resultado esperado:**

- ✅ 4 telas existem
- ✅ Nenhuma tela está quebrada (sem X vermelho)

---

### Teste 2.3: Publicar App

**Passos:**

1. Clique em "Publish"
2. Aguarde conclusão
3. Clique em "Share" (opcional)

**Resultado esperado:**

- ✅ Publicado sem erros
- ✅ Sem warnings críticos

---

## 📱 SEÇÃO 3: HomePage - Gallery

### Teste 3.1: Gallery Carrega Dados

**Passos:**

1. Abra app em Play mode (Press F5 ou Play button)
2. Na HomePage, verifique gallery

**Resultado esperado:**

- ✅ Gallery mostra itens (3+ mínimo)
- ✅ Carrega em < 2 segundos
- ✅ Sem erros de fórmula

---

### Teste 3.2: Isolamento - Usuário Vê Apenas Suas Atividades

**Passos:**

1. Faça login como Usuário A
2. Contar quantas atividades aparecem (ex: 5)
3. Logout, faça login como Usuário B
4. Contar quantas atividades aparecem (ex: 8, diferente)

**Resultado esperado:**

- ✅ Usuário A e B veem números DIFERENTES
- ✅ Nenhum usuário vê atividade do outro
- ✅ Dados isolados com sucesso

**Ação corretiva**: Se ambos veem as mesmas, verificar fórmula da Gallery (deve ter `Responsável.Email = User().Email`)

---

### Teste 3.3: Filtro por Status

**Passos:**

1. Na HomePage, abra dropdown "Status"
2. Selecione "Em progresso"
3. Verifique se gallery atualiza

**Resultado esperado:**

- ✅ Gallery mostra APENAS itens com "Em progresso"
- ✅ Ao limpar filtro, volta a mostrar todos

---

### Teste 3.4: Filtro por Projeto

**Passos:**

1. Abra dropdown "Projeto"
2. Selecione "Melhorias"
3. Verifique gallery

**Resultado esperado:**

- ✅ Gallery mostra APENAS "Melhorias"
- ✅ Combinado com filtro Status = AND (não OR)

---

### Teste 3.5: Busca por Texto

**Passos:**

1. Na search box, digite parte de uma atividade (ex: "Revisão")
2. Verifique gallery

**Resultado esperado:**

- ✅ Gallery filtra por texto (case-insensitive)
- ✅ Resultados em tempo real

---

### Teste 3.6: Botão "+ Nova Atividade"

**Passos:**

1. Clique no botão "+ Nova Atividade"
2. Verifique navegação

**Resultado esperado:**

- ✅ Navega para FormAtividadeNova
- ✅ Sem erros de transição

---

## 📝 SEÇÃO 4: FormAtividadeNova

### Teste 4.1: Formulário Abre e Limpo

**Passos:**

1. Clique em "+ Nova Atividade"
2. Verifique formulário

**Resultado esperado:**

- ✅ Campos vazios (exceto Responsável)
- ✅ Status padrão = "Não iniciado"

---

### Teste 4.2: Responsável Pré-preenchido

**Passos:**

1. No formulário, verifique campo "Responsável"
2. Deve estar preenchido com seu nome/email

**Resultado esperado:**

- ✅ Responsável pré-preenchido com User() logado
- ✅ Readonly (não consegue mudar)

---

### Teste 4.3: Criar Nova Atividade

**Passos:**

1. Preencha:
   - Atividade: "Teste Form Novo"
   - Descrição: "Descrição teste"
   - Solicitante: "João Silva"
   - Projeto: "Certificação ISO"
   - Data_Inicio: Hoje
   - Data_Termino: Amanhã
2. Clique "Salvar"

**Resultado esperado:**

- ✅ Salva com sucesso
- ✅ Mostra notificação "Atividade criada com sucesso"
- ✅ Volta para HomePage
- ✅ Novo item aparece na Gallery

---

### Teste 4.4: Validação - Campo Obrigatório

**Passos:**

1. Deixe campo "Atividade" em branco
2. Clique "Salvar"

**Resultado esperado:**

- ✅ Mostra erro "Campo obrigatório"
- ✅ Não salva sem preencher

---

### Teste 4.5: Validação - Data_Termino > Data_Inicio

**Passos:**

1. Preencha Data_Inicio: 2026-05-15
2. Preencha Data_Termino: 2026-05-10 (anterior)
3. Clique "Salvar"

**Resultado esperado:**

- ✅ Mostra erro "Data término deve ser após início"
- ✅ Não salva com datas inválidas

---

### Teste 4.6: Botão Cancelar

**Passos:**

1. Preencha alguns campos
2. Clique "Cancelar"

**Resultado esperado:**

- ✅ Volta para HomePage
- ✅ Dados NÃO são salvos
- ✅ Sem confirmação desnecessária (cancela direto)

---

## ✏️ SEÇÃO 5: FormAtividadeEditar

### Teste 5.1: Editar Atividade Própria

**Passos:**

1. Na HomePage, clique "Editar" em uma de suas atividades
2. Altere Status para "Em progresso"
3. Clique "Salvar"

**Resultado esperado:**

- ✅ Edita com sucesso
- ✅ Status atualiza na Gallery
- ✅ Volta para HomePage

---

### Teste 5.2: Responsável Readonly ao Editar

**Passos:**

1. Abra formulário de editar
2. Tente clicar/alterar campo "Responsável"

**Resultado esperado:**

- ✅ Campo é readonly (não consegue editar)
- ✅ Mostra seu nome (não muda)

---

### Teste 5.3: Preencher Data_Termino_Real

**Passos:**

1. Abra editar formulário
2. Preencha "Data_Termino_Real" com hoje
3. Clique "Salvar"
4. **Aguarde 30-60 segundos** (fluxo Power Automate rodar)
5. Recarregue a página

**Resultado esperado:**

- ✅ Status mudou para "Concluído" automaticamente
- ✅ Regra Power Automate funcionou

---

### Teste 5.4: Tentativa de Editar Atividade de Outro

**Passos:**

1. Abra atividade de colega (via link direto ou força)

**Resultado esperado:**

- ✅ Mensagem de erro: "Sem permissão"
- ✅ Volta para HomePage
- ✅ Não consegue editar dados de outro

---

### Teste 5.5: Botão Deletar

**Passos:**

1. Clique em "Deletar" em uma atividade sua
2. Confirme

**Resultado esperado:**

- ✅ Item deletado
- ✅ Desaparece da Gallery
- ✅ Mostra notificação "Deletado com sucesso"

---

## 📊 SEÇÃO 6: Dashboard

### Teste 6.1: Dashboard Carrega

**Passos:**

1. Na HomePage, clique botão "Dashboard"
2. Aguarde carregar

**Resultado esperado:**

- ✅ Dashboard carrega em < 2 segundos
- ✅ Sem erros de fórmula
- ✅ 4 KPI cards visíveis

---

### Teste 6.2: KPI Total de Atividades

**Passos:**

1. No Dashboard, verifique card "Total de Atividades"
2. Compare com número de registros na Gallery

**Resultado esperado:**

- ✅ Número é igual ao da Gallery
- ✅ Isolado por usuário

---

### Teste 6.3: KPI % Conclusão

**Passos:**

1. Se tem 5 atividades, e 3 estão "Concluído"
2. % deve ser 60%

**Resultado esperado:**

- ✅ Cálculo correto (3/5 \* 100)

---

### Teste 6.4: Gráfico Pizza (Status)

**Passos:**

1. Verifique gráfico tipo pizza
2. Dividido em: Não iniciado | Em progresso | Concluído

**Resultado esperado:**

- ✅ Gráfico mostra 3 cores/segmentos
- ✅ Proporcional aos dados

---

### Teste 6.5: Gráfico Barras (Projeto)

**Passos:**

1. Verifique gráfico tipo barras horizontais
2. Com labels: Melhorias, Certificação, etc.

**Resultado esperado:**

- ✅ Gráfico mostra projetos
- ✅ Barras proporcionais

---

### Teste 6.6: Filtro de Período

**Passos:**

1. Clique em "Data Início" → selecione 01/05/2026
2. Clique em "Data Fim" → selecione 15/05/2026
3. Verifique tabela

**Resultado esperado:**

- ✅ Tabela filtra por período
- ✅ Mostra APENAS atividades nesse range

---

## ⚙️ SEÇÃO 7: Power Automate

### Teste 7.1: Fluxo Auto_ID Funciona

**Passos:**

1. Crie novo item no SharePoint (formulário padrão)
2. Aguarde 30 segundos
3. Recarregue página

**Resultado esperado:**

- ✅ ID foi preenchido (1, 2, 3...)
- ✅ Sequencial, sem gaps
- ✅ Novo item tem ID diferente dos anteriores

**Ação corretiva**: Se não funcionar → verificar se Fluxo 1 está ativado em Power Automate

---

### Teste 7.2: Fluxo Auto_Status Funciona

**Passos:**

1. Edite um item via formulário padrão
2. Preencha "Data_Termino_Real"
3. Save
4. Aguarde 30-60 segundos
5. Recarregue página

**Resultado esperado:**

- ✅ Status mudou para "Concluído"
- ✅ Automático (usuário não alterou)

**Ação corretiva**: Se não funcionar → verificar se Fluxo 2 está ativado

---

## 👥 SEÇÃO 8: Multi-Usuário

### Teste 8.1: 2 Usuários Simultâneos

**Passos:**

1. Abra app como Usuário A em browser/aba 1
2. Abra app como Usuário B em browser/aba 2 (ou outro device)
3. Ambos fazem ações simultaneamente (criar, editar)
4. Aguarde sincronização

**Resultado esperado:**

- ✅ Sem conflito de dados
- ✅ Cada um vê seus dados isolados
- ✅ Performance aceitável (< 2s)
- ✅ Sem erro "Acesso negado"

---

### Teste 8.2: 5+ Usuários - Teste de Carga

**Passos:**

1. Coordene com 5+ pessoas da equipe
2. Abram app simultaneamente
3. Façam ações (criar, editar, filtrar)
4. Meça performance

**Resultado esperado:**

- ✅ App não crasheia
- ✅ Carregamento < 3 segundos
- ✅ Sem erro 429 (rate limit)

---

## 🔒 SEÇÃO 9: Segurança

### Teste 9.1: Isolamento de Dados

**Passos:**

1. Usuário A cria 3 atividades
2. Usuário B faz login
3. Tenta acessar via deeplink atividade de A

**Resultado esperado:**

- ✅ Acesso bloqueado ou redireciona
- ✅ Não consegue ver dados de A

---

### Teste 9.2: Deletar Atividade de Outro

**Passos:**

1. Usuário A cria atividade
2. Usuário B tenta deletar via API/deeplink

**Resultado esperado:**

- ✅ Bloqueado com mensagem de erro
- ✅ Atividade de A permanece intacta

---

## 📱 SEÇÃO 10: Mobile/Responsive

### Teste 10.1: App em Mobile (Browser)

**Passos:**

1. Abra app em smartphone (via browser)
2. Navegue entre telas
3. Tente criar/editar

**Resultado esperado:**

- ✅ Layout adaptável
- ✅ Campos legíveis
- ✅ Botões clicáveis
- ✅ Sem quebra de layout

---

## 📋 SEÇÃO 11: Documentação

### Teste 11.1: Manual de Usuário Completo

**Passos:**

1. Verifique se README.md está atualizado
2. Verifique se todos os .md estão acessíveis

**Resultado esperado:**

- ✅ Documentação clara e completa
- ✅ Screenshots/descrições visuais

---

### Teste 11.2: Treinamento do Time

**Passos:**

1. Apresente app para 2-3 pessoas do time
2. Peça feedback

**Resultado esperado:**

- ✅ Conseguem navegar sozinhas
- ✅ Entendem isolamento de dados
- ✅ Sem dúvidas críticas

---

## ✅ RESUMO FINAL

### Contador de Testes

- **Total**: 38 testes
- **Passados**: ✅ \_\_\_
- **Falhados**: ❌ \_\_\_
- **Ações Corretivas**: 🔧 \_\_\_

### Status Go-Live

- [ ] **SEM FALHAS**: ✅ PRONTO PARA PRODUÇÃO
- [ ] **COM FALHAS**: ⏳ REVISAR E CORRIGIR

### Aprovação Final

- **Testado por**: ********\_********
- **Data**: **********\_\_\_**********
- **Assinatura**: ********\_\_********

---

## 📞 Dúvidas Durante Testes?

1. Consulte [GUIA-SEGURANCA-ISOLAMENTO.md](GUIA-SEGURANCA-ISOLAMENTO.md)
2. Consulte [FORMULAS-POWER-FX.md](FORMULAS-POWER-FX.md)
3. Consulte [README.md](README.md)

---

**Status**: ✅ Pronto para Testes em Produção
