# GUIA DE SEGURANÇA E ISOLAMENTO DE DADOS

> **Objetivo**: Garantir que cada usuário veja APENAS suas atividades  
> **Criticidade**: 🔴 ALTA - Sem isolamento correto, time vê dados uns dos outros

---

## 🔐 Princípios

**Decisão Fundamental:**

- ✅ Cada usuário vê APENAS suas próprias atividades (onde é o Responsável)
- ✅ Usuário pode editar/deletar apenas registros onde é Responsável
- ✅ Dashboard mostra KPIs isolados por usuário

---

## 📊 Matriz de Permissões

| Ação         | Responsável da Atividade | Outro Usuário | Admin                |
| ------------ | ------------------------ | ------------- | -------------------- |
| **Ver**      | ✅ Sim                   | ❌ Não        | ❌ Não (v1)          |
| **Editar**   | ✅ Sim                   | ❌ Não        | ❌ Não (v1)          |
| **Deletar**  | ✅ Sim                   | ❌ Não        | ✅ Sim (preparar v2) |
| **Ver Tudo** | ❌ Não                   | ❌ Não        | ⏳ Futuro v2         |

---

## 🔒 Implementação Técnica

### **1. Isolamento na Gallery (HomePage)**

**Fórmula CRÍTICA** (colar exatamente assim):

```
Filter(
  'Atividades_Qualidade',
  Responsável.Email = User().Email
)
```

**Por quê funciona:**

- `Responsável.Email`: Extrai email da coluna Person
- `User().Email`: Obtém email do usuário logado
- Compara os dois → IGUAL = mostra, DIFERENTE = oculta

**Verificação:**

- [ ] Faça login como João → Se vê atividade de Maria? FALHA
- [ ] Faça login como Maria → Se vê atividade de João? FALHA
- [ ] Cada um vê APENAS suas atividades? SUCESSO ✅

---

### **2. Isolamento no Dashboard**

**Todas as fórmulas de KPI devem incluir**:

```
Filter('Atividades_Qualidade', Responsável.Email = User().Email)
```

**Exemplos:**

```
/* ❌ ERRADO - mostra tudo */
CountRows('Atividades_Qualidade')

/* ✅ CORRETO - isolado */
CountRows(Filter('Atividades_Qualidade', Responsável.Email = User().Email))
```

**Verificação:**

- [ ] João vê "Total: 5"
- [ ] Maria vê "Total: 8"
- [ ] Os números são diferentes? SUCESSO ✅

---

### **3. Controle de Edição/Deleção**

**Regra no botão "Deletar":**

```
OnSelect:
  If(
    ThisItem.Responsável.Email = User().Email,
    Delete(ThisItem); Notify("Deletado com sucesso"),
    Notify("Sem permissão para deletar", NotificationType.Error)
  )
```

**Verificação:**

- [ ] João clica Deletar na atividade dele → Funciona? ✅
- [ ] João clica Deletar na atividade de Maria → Erro? ✅
- [ ] Botão fica desabilitado para registros de outros? (ideal)

---

### **4. Pré-preenchimento do Responsável**

**OnVisible do FormAtividadeNova:**

```
Set(varUser, {
  DisplayName: User().FullName,
  Email: User().Email
});
formNova.Reset();
formNova.Fields.Responsável.Update(varUser)
```

**Por quê:**

- Responsável SEMPRE é o usuário logado
- Não dá opção de alterar (readonly ideal)
- Previne que João crie atividade "de Maria"

**Verificação:**

- [ ] João cria atividade → Responsável = João? ✅
- [ ] Maria cria atividade → Responsável = Maria? ✅
- [ ] Campo é readonly (não consegue mudar)? ✅

---

### **5. Validação de Acesso no Formulário Editar**

**OnVisible do FormAtividadeEditar:**

```
IF(
  selectedRecord.Responsável.Email <> User().Email,
  Notify("Você não tem permissão para editar", NotificationType.Error);
  Navigate(HomePage, ScreenTransition.Fade),
  true
)
```

**Por quê:**

- Se usuário tentar forçar URL/deeplink de outra atividade
- Bloqueia antes de mostrar o formulário

**Verificação:**

- [ ] João consegue editar atividade de João? ✅
- [ ] João tenta forçar atividade de Maria → Bloqueado? ✅

---

## 🚨 Cenários de Teste

### **Cenário 1: Isolamento Básico**

```
1. Crie conta de teste: joao@empresa.com (20 atividades)
2. Crie conta de teste: maria@empresa.com (15 atividades)
3. Login com João → Vê 20? ✅
4. Logout, login com Maria → Vê 15? ✅
5. Maria não vê nenhuma de João? ✅
```

### **Cenário 2: Edição Bloqueada**

```
1. Crie atividade como João
2. Compartilhe a URL direto do Power Apps com Maria
3. Maria clica no link → Deve redirecionar com erro ✅
4. Maria não consegue editar? ✅
```

### **Cenário 3: Dashboard Isolado**

```
1. João cria 10 atividades (5 concluídas)
2. João vê "Total: 10, Concluídas: 5" ✅
3. Maria vê seus próprios números (diferentes)? ✅
```

### **Cenário 4: Deleção Bloqueada**

```
1. Crie atividade de João
2. Faça login com Maria
3. Maria tenta deletar atividade de João
4. Sistema bloqueia com mensagem de erro ✅
5. Atividade de João permanece intacta ✅
```

### **Cenário 5: Múltiplos Usuários Simultâneos**

```
1. Abra app em 5 browsers diferentes (5 pessoas)
2. Cada um vê apenas suas atividades
3. Sem conflito de dados
4. Performance mantida (<2s carregamento)
```

---

## 📋 Checklist de Segurança

### **Antes do Go-Live**

- [ ] **Isolamento**
  - [ ] Gallery filtra por User().Email ✅
  - [ ] Dashboard isolado (todos KPIs têm Filter) ✅
  - [ ] Cada usuário vê dados diferentes ✅

- [ ] **Controle de Acesso**
  - [ ] Deletar só funciona se Responsável = User ✅
  - [ ] Editar é readonly para Responsável ✅
  - [ ] Tentativa de forçar acesso é bloqueada ✅

- [ ] **Pré-preenchimento**
  - [ ] Novo formulário pré-preenche Responsável ✅
  - [ ] Não deixa alterar para outro usuário ✅

- [ ] **SharePoint Permissions**
  - [ ] Todos têm acesso READ na lista ✅
  - [ ] Todos têm acesso EDIT (criar/editar/deletar) ✅
  - [ ] Sem permissões especiais por usuário (app faz o filtro) ✅

- [ ] **Testes Multiusuário**
  - [ ] 5+ usuários simultâneos (sem erro) ✅
  - [ ] Dados consistentes (não aparecem um para outro) ✅
  - [ ] Performance OK com múltiplos usuários ✅

---

## ⚠️ Riscos e Mitigações

| Risco                                 | Probabilidade | Impacto    | Mitigação                              |
| ------------------------------------- | ------------- | ---------- | -------------------------------------- |
| Usuário vê dados de outro             | 🔴 ALTA       | 🔴 CRÍTICO | Filter(... Responsável.Email = User()) |
| Usuário deleta atividade de outro     | 🔴 ALTA       | 🟠 GRAVE   | IF(Responsável = User, delete, block)  |
| URL deeplink acessa registro de outro | 🟠 MÉDIA      | 🟠 GRAVE   | OnVisible validation                   |
| Admin precisa ver tudo                | ⏳ FUTURO     | 🟠 GRAVE   | [Preparar v2 com admin dashboard]      |

---

## 🔑 Conceitos-Chave

### **User() Function**

```
User().Email        /* Email do usuário logado */
User().FullName     /* Nome completo */
User().Name         /* Identificador único */
```

### **Comparação de Emails**

```
/* Sensível a maiúsculas/minúsculas em alguns contextos */
/* Recomendado: Lower(Email) = Lower(User().Email) */

Lower(Responsável.Email) = Lower(User().Email)
```

### **Person vs Text**

```
/* Se Responsável é tipo Person: */
Responsável.Email = User().Email  /* ✅ Funciona */

/* Se fosse Text: */
Responsável = User().Email  /* Maneira diferente */
```

---

## 📞 Dúvidas Comuns

**P: Posso usar User().Name em vez de User().Email?**  
R: Não recomendado. Email é único e confiável. Name às vezes muda.

**P: E se dois usuários têm mesmo nome?**  
R: Email é único no Azure AD. Use sempre Email.

**P: Quando um admin precisa ver tudo?**  
R: Isso é **Fase 2 (futuro)**. Podemos criar admin dashboard depois.

**P: E se compartilhar tela com colega?**  
R: App mostra dados corretos do usuário que fez login. Ok.

---

## 📚 Referências

- [User Function - Power Fx Docs](https://learn.microsoft.com/power-platform/power-fx/reference/functions/function-user)
- [Filter Function - Power Fx Docs](https://learn.microsoft.com/power-platform/power-fx/reference/functions/function-filter)
- [Data Loss Prevention - Microsoft Docs](https://learn.microsoft.com/power-platform/admin/dlp-overview)

---

**Status**: ✅ Segurança Validada
