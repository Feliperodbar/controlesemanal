# Controle Semanal – Canvas App para Controle de Projetos e Atividades (Quality Team)

Solução de Canvas App para o Power Apps, focada no controle de projetos e atividades de um time de Qualidade. O aplicativo permite criar, editar, visualizar e acompanhar atividades, com filtros avançados e regras automáticas de status.

---

## 📦 Estrutura do Repositório

```
controlesemanal/
├── App.fx.yaml                     # Definição do App (variáveis globais, inicialização)
├── Src/
│   ├── TelaInicial.fx.yaml         # Tela principal com Gallery e filtros
│   ├── TelaFormulario.fx.yaml      # Formulário de criação e edição
│   └── TelaDashboard.fx.yaml       # Dashboard de acompanhamento
├── DataSources/
│   └── ListaAtividades.json        # Esquema da lista SharePoint
└── docs/
    └── SetupGuide.md               # Guia de configuração e implantação
```

---

## 🗂️ Estrutura da Fonte de Dados

A fonte de dados é uma **lista SharePoint** (ou tabela Dataverse) chamada `AtividadesQualidade` com as seguintes colunas:

| Coluna                  | Tipo                       | Detalhes                                      |
|-------------------------|----------------------------|-----------------------------------------------|
| `ID`                    | Número (automático)        | Incremento sequencial, preenchimento automático |
| `Solicitante`           | Choice (Picklist)          | Valores definidos pelo usuário, reutilizáveis  |
| `Projeto`               | Choice (Picklist)          | Valores pré-definidos, persistentes           |
| `Atividade`             | Linha única de texto       | Nome/título da atividade                      |
| `Descricao`             | Múltiplas linhas de texto  | Detalhamento da atividade                     |
| `Responsavel`           | Texto/Pessoa               | Responsável pela atividade                    |
| `Data_Inicio_Previsto`  | Data                       | DatePicker                                    |
| `Data_Termino_Previsto` | Data                       | DatePicker                                    |
| `Data_Inicio_Real`      | Data                       | DatePicker                                    |
| `Data_Termino_Real`     | Data                       | DatePicker                                    |
| `Status`                | Choice (Picklist)          | Não iniciado / Em progresso / Concluído       |

---

## 🖥️ Telas do Aplicativo

### 1. Tela Inicial (`TelaInicial`)
- **Gallery** listando todas as atividades
- **Filtros** por: Status, Projeto, Responsável
- Botão **Nova Atividade** (navega para `TelaFormulario` em modo criação)
- Botão **Dashboard** (navega para `TelaDashboard`)
- Clique em item do gallery → navega para `TelaFormulario` em modo edição

### 2. Tela Formulário (`TelaFormulario`)
- **EditForm** com todos os campos da tabela
- Campo `ID` exibido apenas para leitura (gerado automaticamente)
- Campos de data usando **DatePicker**
- `Solicitante`, `Projeto` e `Status` usando **Dropdown / ComboBox**
- Botão **Salvar** – regra automática: se `Data_Termino_Real` preenchida → Status = "Concluído"
- Botão **Cancelar** – retorna para `TelaInicial` sem salvar
- Novo item criado com Status padrão = "Não iniciado"

### 3. Tela Dashboard (`TelaDashboard`)
- Cards com contadores por Status (Não iniciado, Em progresso, Concluído)
- Gráfico de atividades por Projeto
- Gráfico de atividades por Responsável
- Filtro de período (Data_Inicio_Previsto)

---

## ⚙️ Regras Automáticas

| Regra                                             | Comportamento                                   |
|---------------------------------------------------|-------------------------------------------------|
| Novo item criado                                  | Status padrão = "Não iniciado"                 |
| `Data_Termino_Real` preenchida ao salvar         | Status atualizado automaticamente para "Concluído" |

---

## 🚀 Como Importar e Configurar

Consulte o [Guia de Configuração](./docs/SetupGuide.md) para instruções detalhadas de:
1. Criação da lista SharePoint
2. Importação do arquivo `.msapp` no Power Apps
3. Conexão com a fonte de dados
4. Publicação e compartilhamento

---

## 📋 Requisitos

- **Microsoft Power Apps** (licença por usuário ou por app)
- **SharePoint Online** (para a lista de dados) ou **Microsoft Dataverse**
- **Microsoft 365** com permissões adequadas
