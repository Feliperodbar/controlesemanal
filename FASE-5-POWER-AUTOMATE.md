# FASE 5: Power Automate - Automação (ID Auto-Incremento + Status Automático)

> **Duração**: 2-3 dias  
> **Objetivo**: Criar 2 fluxos para automação:  
> 1️⃣ Auto-incremento de ID  
> 2️⃣ Atualização automática de Status quando data real preenchida

---

## 📋 Visão Geral

| Fluxo                    | Trigger         | Ação                    | Resultado                      |
| ------------------------ | --------------- | ----------------------- | ------------------------------ |
| **Fluxo 1: Auto_ID**     | Item criado     | Incrementar ID          | Novo item recebe ID sequencial |
| **Fluxo 2: Auto_Status** | Item modificado | IF Data_Real preenchida | Status muda para "Concluído"   |

---

## 🔄 Fluxo 1: Auto_Incremento_ID

### Objetivo

Quando um novo item é criado, gera automaticamente um ID sequencial começando do 1.

### Criar Fluxo

1. Acesse [Power Automate](https://make.powerautomate.com)
2. **+ Create** → **Cloud flow** → **Automated cloud flow**
3. **Flow name**: `Auto_Incremento_ID`
4. **Choose your flow's trigger**: Selecione **"When an item is created"** (SharePoint)

---

### Configurar Trigger

1. **Site Address**: Selecione seu SharePoint (ex: `Qualidade`)
2. **List**: `Atividades_Qualidade`

---

### Adicionar Ações

#### **Ação 1: Get items**

1. Clique em **"+ New step"**
2. Procure por: **"Get items"** (SharePoint)
3. Configure:
   - **Site Address**: (mesmo de cima)
   - **List**: `Atividades_Qualidade`
   - **Order by**: `ID`
   - **Ascending**: `false` (ordem decrescente)
   - **Top**: `1` (só o primeiro)

#### **Ação 2: Compose (calcular novo ID)**

1. Clique em **"+ New step"**
2. Procure por: **"Compose"**
3. **Input**:
   ```
   addProperty(
     outputs('Compose'),
     'IDSequencial',
     if(
       empty(body('Get_items')?['value']),
       1,
       add(int(first(body('Get_items')?['value']).ID), 1)
     )
   )
   ```

#### **Ação 3: Update item**

1. Clique em **"+ New step"**
2. Procure por: **"Update item"** (SharePoint)
3. Configure:
   - **Site Address**: (mesmo de cima)
   - **List**: `Atividades_Qualidade`
   - **ID**: Clique em campo, procure por **"ID"** do trigger
   - **ID (coluna)**: Clique em **Expression** → Cole:
     ```
     if(
       empty(body('Get_items')?['value']),
       1,
       add(int(first(body('Get_items')?['value']).ID), 1)
     )
     ```

4. Clique em **"Save"**

---

## 🔄 Fluxo 2: Auto_Status_Concludido

### Objetivo

Quando `Data_Termino_Real` é preenchida, atualiza automaticamente `Status` para "Concluído".

### Criar Fluxo

1. **+ Create** → **Cloud flow** → **Automated cloud flow**
2. **Flow name**: `Auto_Status_Concludido`
3. **Choose trigger**: **"When an item is modified"** (SharePoint)

---

### Configurar Trigger

1. **Site Address**: Seu SharePoint
2. **List**: `Atividades_Qualidade`

---

### Adicionar Ações

#### **Ação 1: Condition (verificar se Data_Real preenchida)**

1. Clique em **"+ New step"**
2. Procure por: **"Condition"**
3. Configure:
   - **Choose a value**: Clique, procure `Data_Termino_Real` → selecione
   - **is not equal to**: Escolha operador
   - **Choose a value** (direita): deixe em branco
4. Clique em toggle azul para entrar em **"Edit in advanced mode"**:
   ```
   @not(equals(triggerBody()?['Data_Termino_Real'], null))
   ```

#### **Ação 2a: Se VERDADEIRO - Update item**

1. No ramo **"If yes"**, clique em **"Add an action"**
2. Procure: **"Update item"** (SharePoint)
3. Configure:
   - **Site Address**: (mesmo)
   - **List**: `Atividades_Qualidade`
   - **ID**: `@triggerBody()?['ID']`
   - **Status**: `Concluído`
4. Clique em **"Save"**

#### **Ação 2b: Se FALSO (opcional - deixe vazio)**

---

## ✅ Testar Fluxos

1. Vá para a lista `Atividades_Qualidade` no SharePoint
2. **Teste Fluxo 1**:
   - Crie um novo item manualmente
   - Verifique se ID foi preenchido automaticamente (deve ser 1, depois 2, 3, etc.)

3. **Teste Fluxo 2**:
   - Edite um item
   - Preencha `Data_Termino_Real`
   - Espere 30 segundos
   - Recarregue a página
   - Verifique se `Status` mudou para "Concluído"

---

## 🐛 Troubleshooting

**P: ID não está sendo gerado**

- Verificar se Fluxo 1 está ativado
- Checar histórico de execução: **My cloud flows** → **Auto_Incremento_ID** → Ver histórico
- Possível erro: Coluna ID está ReadOnly? (Configure como editável)

**P: Status não muda automaticamente**

- Verificar se Fluxo 2 está ativado
- Confirmar que a condição está correta (Data_Termino_Real não vazia)
- Delay de 30-60 segundos é normal

---

## 📄 JSON para Importar (Opcional)

Se preferir importar fluxos prontos:

### Fluxo 1: Auto_Incremento_ID.json

[Disponível conforme necessário - contate admin]

### Fluxo 2: Auto_Status_Concludido.json

[Disponível conforme necessário - contate admin]

---

## 📸 Checklist Fase 5

- ✅ Fluxo 1 (Auto_ID) criado e testado
- ✅ Fluxo 2 (Auto_Status) criado e testado
- ✅ Ambos fluxos ativados
- ✅ Comportamento esperado validado
- ✅ Sem erros de execução

---

## 🎯 Próximo Passo

→ **[FASE-6-DASHBOARD.md](FASE-6-DASHBOARD.md)**

Implementar Dashboard com gráficos e KPIs

---

**Status**: ✅ Fase 5 Pronta
