# FASE 6: Dashboard com Gráficos e KPIs

> **Duração**: 2-3 dias  
> **Objetivo**: Criar 4ª tela com visualizações, gráficos e indicadores-chave

---

## 📊 Visão Geral

```
┌─ DashboardRelatorios ──────────────────────────────┐
│ Total de Atividades: 15  |  Concluídas: 8 (53%)    │
│ Vencidas: 2             |  Em progresso: 5         │
├────────────────────────────────────────────────────┤
│ [Gráfico Pizza: Status]    [Gráfico Barras: Projeto] │
├────────────────────────────────────────────────────┤
│ [Tabela: Atividades do mês]                         │
│ [Filtro por período]                               │
└────────────────────────────────────────────────────┘
```

---

## 🖼️ Passo 1: Criar 4ª Tela - DashboardRelatorios

1. No Power Apps editor, clique em **"+ New screen"**
2. **Layout**: Select "Blank"
3. **Name**: `DashboardRelatorios`

---

## 📈 Passo 2: Adicionar Gráficos

### **Card 1: Total de Atividades**

1. Insert → **Card** (ou **Text label**)
2. Title: `"Total de Atividades"`
3. Value:
   ```
   CountRows(Filter('Atividades_Qualidade', Responsável.Email = User().Email))
   ```

### **Card 2: Atividades Concluídas**

1. Insert → Card
2. Title: `"Concluídas"`
3. Value:
   ```
   CountRows(Filter('Atividades_Qualidade',
     Status = "Concluído" And Responsável.Email = User().Email
   ))
   ```

### **Card 3: % Conclusão**

1. Insert → Card
2. Title: `"% Conclusão"`
3. Value:
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

### **Card 4: Atividades Vencidas**

1. Insert → Card
2. Title: `"Vencidas"`
3. Value:
   ```
   CountRows(Filter('Atividades_Qualidade',
     Data_Termino_Previsto < Today AND
     Status <> "Concluído" AND
     Responsável.Email = User().Email
   ))
   ```

---

## 📊 Passo 3: Gráfico de Pizza (Distribuição por Status)

1. Insert → **Pie chart**
2. **Name**: `chartStatus`
3. **Items**:
   ```
   GroupBy(
     Filter('Atividades_Qualidade', Responsável.Email = User().Email),
     Status,
     "Registro"
   )
   ```
4. **Legend Key**: `Status`
5. **Value**:
   ```
   CountRows(Registro)
   ```

---

## 📊 Passo 4: Gráfico de Barras (Atividades por Projeto)

1. Insert → **Bar chart** (horizontal)
2. **Name**: `chartProjeto`
3. **Items**:
   ```
   GroupBy(
     Filter('Atividades_Qualidade', Responsável.Email = User().Email),
     Projeto,
     "RegistosProjeto"
   )
   ```
4. **Category**: `Projeto`
5. **Series 1 - Value**:
   ```
   CountRows(RegistosProjeto)
   ```

---

## 📋 Passo 5: Tabela Resumida (Últimos 30 dias)

1. Insert → **Data table**
2. **Name**: `tableAtividades`
3. **Items**:
   ```
   Filter('Atividades_Qualidade',
     Responsável.Email = User().Email AND
     Data_Criacao >= DateAdd(Today(), -30, Days)
   )
   ```
4. **Columns to display**:
   - ID
   - Atividade
   - Status
   - Projeto
   - Data_Termino_Previsto

---

## 🔄 Passo 6: Filtro por Período

1. Insert → **Date picker** (x2)
2. **Name**: `dtDataInicio` e `dtDataFim`
3. **Label**: "Data Início" e "Data Fim"
4. **Default**: `Today()` e `DateAdd(Today(), 30, Days)`

Update tabela Items:

```
Filter('Atividades_Qualidade',
  Responsável.Email = User().Email AND
  Data_Inicio_Previsto >= dtDataInicio.Value AND
  Data_Termino_Previsto <= dtDataFim.Value
)
```

---

## 🎨 Passo 7: Layout e Formatação

### **Estrutura Recomendada**

```
┌───Title───────────────────────────────┐
│ Dashboard - Minhas Atividades         │
├───KPIs (4 cards em linha)─────────────┤
│ [Total] [Concluídas] [%] [Vencidas]   │
├───Gráficos (2 colunas)───────────────┤
│ [Pizza]                [Barras]       │
├───Tabela (full width)─────────────────┤
│ [Data Início] [Data Fim]              │
│ [Tabela com scroll]                   │
└───────────────────────────────────────┘
```

### **Cores Sugeridas**

- Status "Concluído": Verde (#27AE60)
- Status "Em progresso": Azul (#2E86AB)
- Status "Não iniciado": Cinza (#95A5A6)
- Vencidas: Vermelho (#E74C3C)

---

## 📊 Passo 8: Navegação

1. Na HomePage, adicione botão **"Dashboard"**:

   ```
   OnSelect: Navigate(DashboardRelatorios, ScreenTransition.Fade)
   ```

2. Na DashboardRelatorios, adicione botão **"Voltar"**:
   ```
   OnSelect: Navigate(HomePage, ScreenTransition.Fade)
   ```

---

## 🔄 Passo 9: Refresh Automático (Opcional)

Para atualizar dados a cada 30 segundos:

1. Insert → **Timer**
2. **Duration**: `30000` (30 segundos)
3. **AutoStart**: `true`
4. **Repeat**: `true`
5. **OnTimerEnd**:
   ```
   Refresh('Atividades_Qualidade');
   ```

---

## 📸 Checklist Fase 6

- ✅ Tela DashboardRelatorios criada
- ✅ 4 Cards com KPIs (Total, Concluídas, %, Vencidas)
- ✅ Gráfico Pizza (Status)
- ✅ Gráfico Barras (Projeto)
- ✅ Tabela com dados últimos 30 dias
- ✅ Filtro por período funcionando
- ✅ Navegação HomePage ↔ Dashboard OK
- ✅ Cores e layout profissional
- ✅ Performance OK (dados carregam < 2s)

---

## 📌 Fórmulas Chave

Todas disponíveis também em: [FORMULAS-POWER-FX.md](FORMULAS-POWER-FX.md)

---

## 🎯 Próximo Passo

→ **FASE-7** (Opcional): Exportar para Excel  
→ **FASE-8**: Testes finais + documentação

---

**Status**: ✅ Fase 6 Pronta
