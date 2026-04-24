# FASE 1: Criar Lista do SharePoint

> **Duração**: 1-2 dias  
> **Objetivo**: Estruturar a fonte de dados com 10 colunas, permissões e vistas auxiliares

---

## 📋 Passo-a-Passo

### **Passo 1: Escolher/Criar Site do SharePoint**

1. Acesse: `https://seuempresa.sharepoint.com` (ou seu SharePoint corporativo)
2. **Opção A**: Usar site existente (Qualidade, RH, etc.)
3. **Opção B**: Criar novo site
   - Clique em "+ Create site"
   - Escolha "Team site"
   - Nome: `Qualidade` ou `Controle Qualidade`
   - Descrição: "Gerenciamento de atividades do time de qualidade"
   - Privacidade: Privado (apenas time tem acesso)
   - Clique em "Create"

---

### **Passo 2: Criar Lista**

1. No site, clique em **+ New → List**
2. Escolha **"Blank list"** (lista em branco)
3. Configure:
   - **Name**: `Atividades_Qualidade`
   - **Description**: "Registro de atividades, projetos e tarefas do time de qualidade"
   - Clique em **"Create"**

---

### **Passo 3: Adicionar Colunas**

A lista já tem uma coluna padrão "Title". Agora adiciomaré as 10 colunas:

#### **Coluna 1: ID** (Número)

1. Clique em **"+ Add column"**
2. **Column name**: `ID`
3. **Type**: Number
4. **Number of decimal places**: 0
5. ✅ **Important**: Marque **"This column cannot be blank"** (será gerado por Power Automate)
6. Clique em **"Save"**

#### **Coluna 2: Solicitante** (Choice - Gerenciável)

1. Clique em **"+ Add column"**
2. **Column name**: `Solicitante`
3. **Type**: Choice
4. **Choices** (adicione valores, um por linha):
   ```
   João Silva
   Maria Santos
   Pedro Costa
   Ana Oliveira
   Carlos Ferreira
   ```
   ⚠️ Adicione nomes do seu time ou deixe em branco (usuários adicionarão depois)
5. ✅ **Allow "Fill-in" choices**: SIM (permite novos valores)
6. Clique em **"Save"**

#### **Coluna 3: Projeto** (Choice - Pré-definidos)

1. Clique em **"+ Add column"**
2. **Column name**: `Projeto`
3. **Type**: Choice
4. **Choices** (pré-definidos, um por linha):
   ```
   Melhorias
   Certificação ISO
   Auditorias
   Conformidade
   Outros
   ```
   ⚠️ **Customize**: Você pode alterar depois no SharePoint
5. ✅ **Allow "Fill-in" choices**: NÃO (apenas valores pré-definidos)
6. Clique em **"Save"**

#### **Coluna 4: Atividade** (Single line)

1. Clique em **"+ Add column"**
2. **Column name**: `Atividade`
3. **Type**: Single line of text
4. **Max length**: 255
5. ✅ **This column cannot be blank**: SIM
6. Clique em **"Save"**

#### **Coluna 5: Descrição** (Multiple lines)

1. Clique em **"+ Add column"**
2. **Column name**: `Descrição`
3. **Type**: Multiple lines of text
4. **Number of lines**: 6
5. Clique em **"Save"**

#### **Coluna 6: Responsável** (Person)

1. Clique em **"+ Add column"**
2. **Column name**: `Responsável`
3. **Type**: Person or Group
4. **Allow multiple selections**: NÃO (apenas 1 pessoa)
5. ✅ **This column cannot be blank**: SIM
6. Clique em **"Save"**

#### **Coluna 7-10: Datas**

Repita o processo abaixo para cada coluna de data:

**Coluna 7: Data_Inicio_Previsto**

- **Column name**: `Data_Inicio_Previsto`
- **Type**: Date
- **Format**: Data e Hora (ou apenas Data)
- Clique em **"Save"**

**Coluna 8: Data_Termino_Previsto**

- **Column name**: `Data_Termino_Previsto`
- **Type**: Date
- Clique em **"Save"**

**Coluna 9: Data_Inicio_Real**

- **Column name**: `Data_Inicio_Real`
- **Type**: Date
- Clique em **"Save"**

**Coluna 10: Data_Termino_Real**

- **Column name**: `Data_Termino_Real`
- **Type**: Date
- Clique em **"Save"**

#### **Coluna 11: Status** (Choice - Fixo)

1. Clique em **"+ Add column"**
2. **Column name**: `Status`
3. **Type**: Choice
4. **Choices** (exatamente estas 3):
   ```
   Não iniciado
   Em progresso
   Concluído
   ```
5. ✅ **Allow "Fill-in" choices**: NÃO
6. **Default value**: `Não iniciado`
7. Clique em **"Save"**

---

### **Passo 4: Configurar Permissões**

1. No SharePoint, clique em **"Settings"** (engrenagem) → **"List settings"**
2. Clique em **"Shared with"** (ou "Permissions")
3. Garantir que:
   - ✅ Todo o time tem acesso (Contribuidor/Editor)
   - ✅ Proprietários podem deletar
   - ✅ Viewers podem visualizar

**Para Power Apps acessar:**

1. Vá para **Settings → Versioning settings**
2. Ative **"Draft item security"** = "Any user who can edit items" (recomendado)

---

### **Passo 5: Criar Vistas Auxiliares**

Vistas facilitam relatórios e filtragens:

#### **Vista 1: "Minha Responsabilidade"**

1. Na lista, clique em **"All Items"** → **"Save as new view"**
2. **View name**: `Minha Responsabilidade`
3. **Type**: Standard
4. **Filters**: `Responsável is [Me]` (mostra apenas atividades onde Responsável = usuário logado)
5. Clique em **"Save"**

#### **Vista 2: "Por Status"**

1. Clique em **"All Items"** → **"Save as new view"**
2. **View name**: `Por Status`
3. **Type**: Standard
4. **Group by**: `Status`
5. Clique em **"Save"**

#### **Vista 3: "Por Projeto"**

1. Clique em **"All Items"** → **"Save as new view"**
2. **View name**: `Por Projeto`
3. **Type**: Standard
4. **Group by**: `Projeto`
5. Clique em **"Save"**

---

### **Passo 6: Verificação Final**

Checklist da Fase 1:

- ✅ Lista `Atividades_Qualidade` criada
- ✅ 11 colunas adicionadas (ID, Solicitante, Projeto, Atividade, Descrição, Responsável, 4x Datas, Status)
- ✅ Tipos de dados corretos
- ✅ Validações configuradas (obrigatórios, defaults)
- ✅ Permissões OK (time com acesso)
- ✅ 3 vistas criadas

**Resultado esperado:**
Uma lista do SharePoint com todos os dados estruturados, pronta para conectar ao Canvas App.

---

## 🔗 Próximo Passo

👉 Quando esta fase estiver completa, vá para **[FASE-2-4-CANVAS-APP.md](FASE-2-4-CANVAS-APP.md)**

---

## ⚠️ Troubleshooting

**P: Não consigo adicionar coluna Person?**  
R: Alguns tenants exigem permissão de admin. Contate seu admin de TI.

**P: Erro de permissão ao compartilhar?**  
R: Verifique se você é proprietário da lista ou site.

**P: Posso adicionar mais Projetos depois?**  
R: Sim! Vá em Settings → List settings → Columns → Projeto → Edit → adicione novos valores.

---

**Status**: ✅ Fase 1 Pronta para próximo passo
