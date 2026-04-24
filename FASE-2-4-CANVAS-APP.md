# FASE 2-4: Construir Canvas App (HomePage + Formulários)

> **Duração**: 6-8 dias  
> **Objetivo**: Criar app com galeria, filtros, formulários e validações

---

## 📋 Visão Geral

```
Canvas App "ControleSemanal_Qualidade"
│
├─ Tela 1: HomePage
│  ├─ Gallery (lista de atividades)
│  ├─ Filtros (Status, Projeto, Busca)
│  └─ Botão "+ Nova Atividade"
│
├─ Tela 2: FormAtividadeNova
│  ├─ EditForm (criar novo)
│  ├─ Responsável: preenchido automaticamente
│  └─ Botões: Salvar / Cancelar
│
├─ Tela 3: FormAtividadeEditar
│  ├─ EditForm (editar existente)
│  └─ Botões: Salvar / Cancelar
│
└─ Tela 4: DashboardRelatorios
   ├─ Gráficos
   └─ KPIs
```

---

## 🚀 Passo 1: Criar Canvas App

1. Acesse [Power Apps](https://make.powerapps.com)
2. **+ Create** → **Canvas app**
3. **App name**: `ControleSemanal_Qualidade`
4. **Format**: Tablet (1024x768) ou Phone (360x667) - recomendado Tablet
5. Clique em **"Create"**

---

## 🔗 Passo 2: Conectar à Lista do SharePoint

1. No app editor, clique em **Data** (lado esquerdo)
2. Clique em **"+ Add data"** ou **"Connect to data"**
3. Procure por **"SharePoint"**
4. Escolha seu site (ex: `Qualidade`)
5. Selecione a lista: **`Atividades_Qualidade`**
6. Clique em **"Connect"**

✅ Agora a lista está disponível como data source!

---

## 📱 Passo 3: Criar Tela 1 - HomePage

### **Estrutura**

```
┌─ HomePage ─────────────────────────────────────┐
│ [Logo] Minhas Atividades    [+ Nova Atividade] │
├─────────────────────────────────────────────────┤
│ Filtros:                                        │
│ Status: [Dropdown]  Projeto: [Dropdown]         │
│ [🔍 Pesquisa]                                   │
├─────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────┐ │
│ │ ID: 1 | Atividade: Revisão de processos    │ │
│ │ Status: Em progresso | Projeto: Melhorias   │ │
│ │ Responsável: João | Prazo: 2026-05-15       │ │
│ │ [Editar] [Deletar]                          │ │
│ └─────────────────────────────────────────────┘ │
│ ┌─────────────────────────────────────────────┐ │
│ │ (outro item...)                              │ │
│ └─────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────┘
```

### **Criar Controles**

#### **Container Principal**

1. Insert → Container → Vertical container
2. **Name**: `containerMain`
3. Layout: Vertical

#### **Header**

1. Inside container: Insert → Label
2. **Text**: `"Minhas Atividades"`
3. **Font Size**: 24
4. **Bold**: Yes
5. **Color**: Azul corporativo

#### **Botão "+ Nova Atividade"**

1. Insert → Button
2. **Text**: `"+ Nova Atividade"`
3. **OnSelect**: `Navigate(FormAtividadeNova, ScreenTransition.Fade)`

#### **Filtros**

**Dropdown Status**

1. Insert → Dropdown
2. **Name**: `ddStatus`
3. **Items**: `DistinctRows(Atividades_Qualidade, Status)`
4. **Value**: `Status.Value`
5. **Allow multiple selections**: No

**Dropdown Projeto**

1. Insert → Dropdown
2. **Name**: `ddProjeto`
3. **Items**: `DistinctRows(Atividades_Qualidade, Projeto)`
4. **Value**: `Projeto.Value`

**TextInput Busca**

1. Insert → Text input
2. **Name**: `txSearchBox`
3. **Placeholder**: `"Buscar por atividade..."`

#### **Gallery (Lista de Atividades)**

1. Insert → Horizontal gallery
2. **Name**: `galleryAtividades`
3. **Items (FÓRMULA CRÍTICA)**:

   ```
   Filter(
     Filter(
       Filter(
         'Atividades_Qualidade',
         Responsável.Email = User().Email  /* Isolamento */
       ),
       IsBlank(ddStatus.Value) OR Status = ddStatus.Value  /* Filtro Status */
     ),
     IsBlank(ddProjeto.Value) OR Projeto = ddProjeto.Value  /* Filtro Projeto */
   )
   ```

4. **Layout**: Title and subtitle
5. Fields to display:
   - **Title**: `Atividade`
   - **Subtitle**: `"Status: " & Status & " | Projeto: " & Projeto`

#### **Botões na Gallery**

- **Editar**: `OnSelect: Navigate(FormAtividadeEditar, ScreenTransition.Fade); Set(selectedRecord, ThisItem)`
- **Deletar**: `OnSelect: Delete(ThisItem); Refresh('Atividades_Qualidade')`

---

## 📝 Passo 4: Criar Tela 2 - FormAtividadeNova

### **Estrutura**

```
FormAtividadeNova (Criar novo registro)
```

### **Criar EditForm**

1. Insert → Edit form
2. **Name**: `formNova`
3. **DataSource**: `Atividades_Qualidade`
4. **Item**: Leave blank (novo registro)

### **Configurar Campos**

#### **Atividade** (TextInput)

1. Add field: `Atividade`
2. **Required**: Yes
3. **Max length**: 255

#### **Descrição** (TextInput Multiline)

1. Add field: `Descrição`
2. **Mode**: Multiline
3. **Lines**: 4

#### **Solicitante** (ComboBox)

1. Add field: `Solicitante`
2. **Control type**: ComboBox
3. **Items**: `Distinct('Atividades_Qualidade', Solicitante)`

#### **Projeto** (Dropdown)

1. Add field: `Projeto`
2. **Control type**: Dropdown
3. **Items**: `["Melhorias", "Certificação ISO", "Auditorias", "Conformidade", "Outros"]`
4. **Required**: Yes

#### **Responsável** (Lookup - AUTOPREENCHIDO)\*\*

1. Add field: `Responsável`
2. **Control type**: Lookup
3. ✅ **OnVisible (CRÍTICO)**:
   ```
   Set(varResponsavelUser, {
     DisplayName: User().FullName,
     Email: User().Email
   });
   formNova.Fields.Responsável.Update(varResponsavelUser)
   ```
4. **Hint**: "Preenchido automaticamente com seu usuário"

#### **Datas**

1. Add field: `Data_Inicio_Previsto`
   - **Control type**: Date picker
2. Add field: `Data_Termino_Previsto`
   - **Control type**: Date picker
3. **Validate**: Data_Termino_Previsto > Data_Inicio_Previsto

#### **Status** (Dropdown)

1. Add field: `Status`
2. **Items**: `["Não iniciado", "Em progresso", "Concluído"]`
3. **Default**: `"Não iniciado"`

#### **ID** (Readonly)

1. Add field: `ID`
2. **Visible**: No (será gerado por Power Automate)

### **Botões**

- **Salvar**: `OnSelect: SubmitForm(formNova); Notify("Atividade criada com sucesso!"); Navigate(HomePage, ScreenTransition.Fade)`
- **Cancelar**: `OnSelect: ResetForm(formNova); Navigate(HomePage, ScreenTransition.Fade)`

---

## ✏️ Passo 5: Criar Tela 3 - FormAtividadeEditar

**Mesma estrutura de FormAtividadeNova**, mas:

- **Item**: `selectedRecord` (vem da gallery)
- **Responsável**: ReadOnly (não pode mudar)
- **Data_Inicio_Real** e **Data_Termino_Real**: Aparecem aqui
- Adicionar validação: Se `Responsável.Email ≠ User().Email`, mostrar erro

---

## 🔄 Passo 6: Criar Tela 4 - DashboardRelatorios

✅ **Detalhes completos em**: [FASE-6-DASHBOARD.md](FASE-6-DASHBOARD.md)

---

## 🧪 Passo 7: Validações e Regras

### **Validações de Formulário**

```
/* Validar Data_Termino > Data_Inicio */
IF(
  IsBlank(formNova.Fields.Data_Inicio_Previsto) OR
  IsBlank(formNova.Fields.Data_Termino_Previsto),
  true,
  formNova.Fields.Data_Termino_Previsto.Value > formNova.Fields.Data_Inicio_Previsto.Value
)
```

### **Validar Acesso**

```
/* Só permite editar se Responsável = User */
IF(
  selectedRecord.Responsável.Email = User().Email,
  true,
  Error("Você não tem permissão para editar esta atividade")
)
```

---

## 📸 Checklist Fase 2-4

- ✅ Canvas App criado
- ✅ Conectado à lista do SharePoint
- ✅ HomePage com Gallery (filtra por User)
- ✅ Filtros funcionando
- ✅ Form novo com Responsável pré-preenchido
- ✅ Form editar com Responsável readonly
- ✅ Botões Salvar/Cancelar funcionando
- ✅ Validações implantadas
- ✅ Navegação entre telas OK
- ✅ Dashboard placeholder

---

## 📌 Fórmulas Key (Copy/Paste)

Todas as fórmulas estão também em: [FORMULAS-POWER-FX.md](FORMULAS-POWER-FX.md)

---

## 🎯 Próximo Passo

→ **[FASE-5-POWER-AUTOMATE.md](FASE-5-POWER-AUTOMATE.md)**

Implementar automação (ID auto-incremento + Status auto-atualizar)

---

**Status**: ✅ Fase 2-4 Pronta
