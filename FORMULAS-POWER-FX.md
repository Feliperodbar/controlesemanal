# FORMULAS-POWER-FX: Snippets de Código Prontos

> **Use**: Copy/paste direto no Power Apps  
> **Referência**: Todas as fórmulas utilizadas nas Fases 2-6

---

## 🔐 ISOLAMENTO DE DADOS

### Gallery - Mostrar apenas atividades do usuário logado

```
Filter(
  Filter(
    Filter(
      'Atividades_Qualidade',
      Responsável.Email = User().Email
    ),
    IsBlank(ddStatus.Value) OR Status = ddStatus.Value
  ),
  IsBlank(ddProjeto.Value) OR Projeto = ddProjeto.Value
)
```

### Validar acesso (só permite editar se Responsável = User)

```
IF(
  selectedRecord.Responsável.Email = User().Email,
  true,
  Error("Você não tem permissão para editar esta atividade")
)
```

---

## 📝 FORMULÁRIOS

### Pré-preencher Responsável com usuário logado

```
Set(varResponsavelUser, {
  DisplayName: User().FullName,
  Email: User().Email
});
formNova.Fields.Responsável.Update(varResponsavelUser)
```

### Validar Data_Termino > Data_Inicio

```
IF(
  IsBlank(formNova.Fields.Data_Inicio_Previsto) OR
  IsBlank(formNova.Fields.Data_Termino_Previsto),
  true,
  formNova.Fields.Data_Termino_Previsto.Value >
  formNova.Fields.Data_Inicio_Previsto.Value
)
```

### SubmitForm + Notificação + Voltar

```
SubmitForm(formNova);
Notify("Atividade criada com sucesso!", NotificationType.Success);
Navigate(HomePage, ScreenTransition.Fade)
```

---

## 📊 DASHBOARD - CARDS (KPIs)

### Total de Atividades do Usuário

```
CountRows(Filter('Atividades_Qualidade', Responsável.Email = User().Email))
```

### Atividades Concluídas

```
CountRows(Filter('Atividades_Qualidade',
  Status = "Concluído" AND Responsável.Email = User().Email
))
```

### Percentual de Conclusão

```
If(
  CountRows(Filter('Atividades_Qualidade', Responsável.Email = User().Email)) = 0,
  0,
  Round(
    Divide(
      CountRows(Filter('Atividades_Qualidade',
        Status = "Concluído" AND Responsável.Email = User().Email
      )),
      CountRows(Filter('Atividades_Qualidade',
        Responsável.Email = User().Email
      ))
    ) * 100,
    2
  )
) & "%"
```

### Atividades Vencidas

```
CountRows(Filter('Atividades_Qualidade',
  Data_Termino_Previsto < Today AND
  Status <> "Concluído" AND
  Responsável.Email = User().Email
))
```

---

## 📈 GRÁFICOS

### Gráfico Pizza - Distribuição por Status

```
GroupBy(
  Filter('Atividades_Qualidade', Responsável.Email = User().Email),
  Status,
  "Registro"
)
```

**Legend Key**: `Status`  
**Value**: `CountRows(Registro)`

### Gráfico Barras - Atividades por Projeto

```
GroupBy(
  Filter('Atividades_Qualidade', Responsável.Email = User().Email),
  Projeto,
  "RegistosProjeto"
)
```

**Category**: `Projeto`  
**Series Value**: `CountRows(RegistosProjeto)`

### Tabela - Últimos 30 dias com filtro de período

```
Filter('Atividades_Qualidade',
  Responsável.Email = User().Email AND
  Data_Inicio_Previsto >= dtDataInicio.Value AND
  Data_Termino_Previsto <= dtDataFim.Value
)
```

---

## 🔄 NAVEGAÇÃO

### Botão ir para HomePage

```
Navigate(HomePage, ScreenTransition.Fade)
```

### Botão ir para FormAtividadeNova

```
Navigate(FormAtividadeNova, ScreenTransition.Fade)
```

### Botão ir para FormAtividadeEditar (com registro selecionado)

```
Navigate(FormAtividadeEditar, ScreenTransition.Fade);
Set(selectedRecord, ThisItem)
```

### Botão ir para DashboardRelatorios

```
Navigate(DashboardRelatorios, ScreenTransition.Fade)
```

---

## 🗑️ OPERAÇÕES

### Deletar item (com confirmação)

```
OnSelect: Delete(ThisItem);
Notify("Atividade removida");
Refresh('Atividades_Qualidade')
```

### Resetar formulário

```
ResetForm(formNova)
```

---

## ⏱️ DATAS E PERÍODOS

### Verificar se data está vencida

```
Data_Termino_Previsto < Today
```

### Datas dos últimos 7 dias

```
Data_Criacao >= DateAdd(Today(), -7, Days)
```

### Datas dos últimos 30 dias

```
Data_Criacao >= DateAdd(Today(), -30, Days)
```

### Datas do mês atual

```
And(
  Data_Criacao >= DateAdd(Today(), -Day(Today()) + 1, Days),
  Data_Criacao < DateAdd(DateAdd(Today(), -Day(Today()) + 1, Days), 1, Months)
)
```

---

## 🔴 NOTIFICAÇÕES

### Sucesso

```
Notify("Atividade criada com sucesso!", NotificationType.Success)
```

### Erro

```
Notify("Ocorreu um erro ao salvar", NotificationType.Error)
```

### Aviso

```
Notify("Atenção: Data vencida!", NotificationType.Warning)
```

### Informação

```
Notify("Operação concluída", NotificationType.Information)
```

---

## 📌 USER & ENV

### Obter usuário logado

```
User().Email
```

### Obter nome completo

```
User().FullName
```

### Obter identificador único

```
User().Name
```

### Hoje

```
Today()
```

### Data/Hora atual

```
Now()
```

---

## 🔀 LÓGICA CONDICIONAL

### IF simples

```
If(condicao, valor_verdadeiro, valor_falso)
```

### IF aninhado

```
Switch(status,
  "Concluído", "✅ Completo",
  "Em progresso", "⏳ Em andamento",
  "Não iniciado", "⭕ Pendente"
)
```

### AND / OR

```
And(condicao1, condicao2)
Or(condicao1, condicao2)
Not(condicao)
```

---

## 📋 TEXTO

### Concatenar strings

```
"ID: " & ID & " | Status: " & Status
```

### Texto em maiúsculas

```
Upper("texto")
```

### Primeira letra maiúscula

```
Proper("joão silva") /* Resultado: João Silva */
```

### Remover espaços

```
Trim("   texto   ") /* Resultado: texto */
```

---

## 💾 VARIÁVEIS

### Declarar variável

```
Set(varMinhaVariavel, "valor")
```

### Incrementar contador

```
Set(varContador, varContador + 1)
```

### Usar variável em fórmula

```
If(varAtivado = true, "Sim", "Não")
```

---

## 📍 BUSCA E FILTRO

### Busca por texto (case-insensitive)

```
Filter('Atividades_Qualidade',
  Or(
    Search(txSearchBox.Value, Atividade),
    Search(txSearchBox.Value, Descrição)
  )
)
```

### Busca com filtros combinados

```
Filter(
  Filter(
    'Atividades_Qualidade',
    If(IsBlank(txSearch.Value), true,
      Or(Search(Upper(txSearch.Value), Upper(Atividade)))
    )
  ),
  Responsável.Email = User().Email
)
```

---

## ✅ VALIDAÇÃO

### Campo vazio?

```
IsBlank(campo.Value)
```

### Campo preenchido?

```
Not(IsBlank(campo.Value))
```

### Campo é número?

```
IsNumeric(campo.Value)
```

### Campo é email válido?

```
Match(campo.Value, Email.Email)
```

---

## 📚 Referência Completa Power Fx

Docs oficial: https://learn.microsoft.com/power-platform/power-fx/

---

**Dica**: Copie e cole estas fórmulas conforme necessário. Adapte nomes de controles e campos conforme seu projeto.
