# 📋 Canvas App: Controle de Qualidade - Power Apps

> **Status**: Implementação em progresso (Fase 1)  
> **Data**: Abril 2026  
> **Time**: Qualidade  
> **Objetivo**: Centralizar controle de atividades com isolamento por usuário

---

## 📌 Resumo Executivo

Este projeto implementa um **Canvas App no Power Apps** conectado a uma **lista do SharePoint** para gerenciar atividades de um **time de Qualidade (16+ pessoas)**.

### Características Principais

- ✅ **Isolamento de dados**: Cada usuário vê apenas suas atividades
- ✅ **Autopreenchimento**: Campo "Responsável" preenche automaticamente com usuário logado
- ✅ **Automação**: ID auto-incremento + Status auto-atualizar
- ✅ **Dashboard**: Gráficos com KPIs
- ✅ **Filtros**: Status, Projeto, Busca por texto
- ✅ **Exportação**: Relatórios em Excel

---

## 📁 Estrutura de Arquivos

```
/controlesemanal/
├── README.md (este arquivo)
├── FASE-1-SHAREPOINT.md              [Criar lista do SharePoint]
├── FASE-2-4-CANVAS-APP.md            [Build Canvas App]
├── FASE-5-POWER-AUTOMATE.md          [Fluxos automação]
├── FASE-6-DASHBOARD.md               [Gráficos e KPIs]
├── FORMULAS-POWER-FX.md              [Snippets prontos]
├── GUIA-SEGURANCA-ISOLAMENTO.md      [Regras de acesso]
├── CHECKLIST-TESTES.md               [Validação final]
└── TEMPLATES/                         [Arquivos reutilizáveis]
    ├── power-automate-fluxo-1.json
    └── power-automate-fluxo-2.json
```

---

## 🚀 Como Começar

### **Pré-requisitos**

- ✅ Acesso ao Power Apps (licença corporativa)
- ✅ Acesso ao SharePoint (site ou criar novo)
- ✅ Permissões para criar listas e canvas apps
- ✅ Acesso ao Power Automate

### **Timeline Estimada**

| Fase                 | Duração         | Status              |
| -------------------- | --------------- | ------------------- |
| Fase 1: SharePoint   | 1-2 dias        | ⏳ Próximo          |
| Fase 2-4: Canvas App | 6-8 dias        | Aguardando Fase 1   |
| Fase 5: Automação    | 2-3 dias        | Aguardando Fase 1   |
| Fase 6: Dashboard    | 2-3 dias        | Aguardando Fase 2-4 |
| Fase 7-8: Testes     | 3-5 dias        | Final               |
| **Total**            | **3-4 semanas** |                     |

---

## 📊 Estrutura de Dados (SharePoint)

### Colunas da Lista `Atividades_Qualidade`

| #   | Coluna                    | Tipo           | Descrição                                               |
| --- | ------------------------- | -------------- | ------------------------------------------------------- | ------------ | --------- |
| 1   | **ID**                    | Número         | Auto-incremento sequencial                              |
| 2   | **Solicitante**           | Choice         | Gerenciável pelo usuário                                |
| 3   | **Projeto**               | Choice         | Pré-definidos (ex: Melhorias, Certificação, Auditorias) |
| 4   | **Atividade**             | Single line    | Título da atividade                                     |
| 5   | **Descrição**             | Multiple lines | Detalhes completos                                      |
| 6   | **Responsável**           | Person         | Preenchido automaticamente = User logado                |
| 7   | **Data_Inicio_Previsto**  | Date           | Data início planejada                                   |
| 8   | **Data_Termino_Previsto** | Date           | Data conclusão planejada                                |
| 9   | **Data_Inicio_Real**      | Date           | Quando realmente iniciou                                |
| 10  | **Data_Termino_Real**     | Date           | Quando realmente terminou                               |
| 11  | **Status**                | Choice         | Não iniciado                                            | Em progresso | Concluído |

---

## 🔐 Isolamento de Dados

**Decisão**: Cada usuário vê APENAS suas próprias atividades (onde é o Responsável)

```
User A (João)  →  Vê apenas atividades com Responsável = João
User B (Maria) →  Vê apenas atividades com Responsável = Maria
Admin          →  [Futuro] Dashboard com todas as atividades
```

### Implementação

- Gallery filtra por: `Responsável.Email = User().Email`
- Dashboard mostra KPIs apenas de seu dados
- Editar/Deletar: Só se Responsável = User()

---

## 📋 Arquivos de Guia

Cada fase tem seu próprio arquivo `.md` com passo-a-passo detalhado:

1. **[FASE-1-SHAREPOINT.md](FASE-1-SHAREPOINT.md)** ← **COMECE AQUI**
   - Como criar lista
   - Configurar colunas
   - Definir permissões

2. **[FASE-2-4-CANVAS-APP.md](FASE-2-4-CANVAS-APP.md)**
   - Estrutura do app
   - Fórmulas e propriedades
   - Validações

3. **[FASE-5-POWER-AUTOMATE.md](FASE-5-POWER-AUTOMATE.md)**
   - 2 fluxos (Auto-ID + Auto-Status)
   - JSON para importar

4. **[FASE-6-DASHBOARD.md](FASE-6-DASHBOARD.md)**
   - Gráficos e KPIs
   - Fórmulas GroupBy/CountRows

5. **[FORMULAS-POWER-FX.md](FORMULAS-POWER-FX.md)** ← COPY/PASTE pronto
   - Snippets de todas as fórmulas
   - Use direto no Power Apps

6. **[GUIA-SEGURANCA-ISOLAMENTO.md](GUIA-SEGURANCA-ISOLAMENTO.md)**
   - Matriz de permissões
   - Testes de segurança

7. **[CHECKLIST-TESTES.md](CHECKLIST-TESTES.md)**
   - 25+ itens de validação
   - Antes de go-live

---

## ⚙️ Decisões Confirmadas

✅ **Fonte de dados**: SharePoint (Lista)  
✅ **Ambiente**: Power Apps com licença em dia  
✅ **Team size**: Grande (16+)  
✅ **Automação**: Power Automate (fluxos)  
✅ **Dashboard**: Sim, com gráficos  
✅ **Exportação**: Excel com dados filtrados  
✅ **Isolamento**: Usuário vê APENAS suas atividades  
✅ **Responsável automático**: Sim, User() logado

---

## 🎯 Próximo Passo

👉 **Abra agora**: [FASE-1-SHAREPOINT.md](FASE-1-SHAREPOINT.md)

Lá tem **passo-a-passo completo e imagens** para criar a lista do SharePoint.

---

## 📞 Dúvidas Frequentes

**P: E se não temos um site SharePoint?**  
R: Você pode criar um novo site em `https://seuempresa.sharepoint.com` → "Create site" → escolher "Team site"

**P: Como users acessam o app?**  
R: URL do app (Power Apps link) + permissão de leitura da lista SharePoint

**P: Quanto custa?**  
R: Depende da licença Power Apps (plano corporativo, geralmente já incluso)

**P: Posso customizar os Projetos depois?**  
R: Sim! Choice fields são editáveis no SharePoint a qualquer hora

---

## 📝 Mudanças & Histórico

| Data       | Mudança                                 |
| ---------- | --------------------------------------- |
| 2026-04-24 | Criação inicial - Documentação Fase 1-8 |

---

**Vamos começar! 🚀**
