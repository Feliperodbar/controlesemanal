# Guia de Configuração e Implantação

## Pré-requisitos

- Licença Microsoft 365 com acesso ao **Power Apps** e ao **SharePoint Online**
- Permissão de contribuição na coleção de sites do SharePoint de destino
- Acesso ao **Power Apps Studio** (make.powerapps.com)

---

## 1. Criar a Lista SharePoint

### Opção A – Criação manual

1. Acesse o site SharePoint desejado
2. Clique em **+ Novo → Lista**
3. Selecione **Lista em branco**
4. Nomeie como **`AtividadesQualidade`**
5. Adicione as seguintes colunas:

| Nome da coluna          | Tipo                        | Configurações adicionais                              |
|-------------------------|-----------------------------|-------------------------------------------------------|
| `Solicitante`           | Escolha (Choice)            | Ative "Preencher valor" para novos itens; adicione os valores iniciais desejados |
| `Projeto`               | Escolha (Choice)            | Ative "Preencher valor" para novos itens; adicione os valores iniciais desejados |
| `Atividade`             | Linha única de texto        | Obrigatório = Sim                                    |
| `Descricao`             | Várias linhas de texto      | –                                                     |
| `Responsavel`           | Linha única de texto        | –                                                     |
| `Data_Inicio_Previsto`  | Data e hora                 | Formato = Somente data                               |
| `Data_Termino_Previsto` | Data e hora                 | Formato = Somente data                               |
| `Data_Inicio_Real`      | Data e hora                 | Formato = Somente data                               |
| `Data_Termino_Real`     | Data e hora                 | Formato = Somente data                               |
| `Status`                | Escolha (Choice)            | Opções fixas: `Não iniciado`, `Em progresso`, `Concluído` · Padrão: `Não iniciado` · Desative "Preencher valor" |

> **Nota:** A coluna `ID` já existe por padrão em toda lista SharePoint como campo numérico sequencial automático.

### Opção B – Script PnP PowerShell (automatizado)

```powershell
# Instale o módulo PnP se necessário:
# Install-Module PnP.PowerShell

Connect-PnPOnline -Url "https://seutenante.sharepoint.com/sites/seusite" -Interactive

New-PnPList -Title "AtividadesQualidade" -Template GenericList

Add-PnPField -List "AtividadesQualidade" -DisplayName "Solicitante"           -InternalName "Solicitante"            -Type Choice   -AddToDefaultView -Choices @() -AddToView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Projeto"               -InternalName "Projeto"                -Type Choice   -AddToDefaultView -Choices @() -AddToView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Atividade"             -InternalName "Atividade"              -Type Text     -AddToDefaultView -Required
Add-PnPField -List "AtividadesQualidade" -DisplayName "Descrição"             -InternalName "Descricao"              -Type Note     -AddToDefaultView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Responsável"           -InternalName "Responsavel"            -Type Text     -AddToDefaultView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Data Início Previsto"  -InternalName "Data_Inicio_Previsto"   -Type DateTime -AddToDefaultView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Data Término Previsto" -InternalName "Data_Termino_Previsto"  -Type DateTime -AddToDefaultView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Data Início Real"      -InternalName "Data_Inicio_Real"       -Type DateTime -AddToDefaultView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Data Término Real"     -InternalName "Data_Termino_Real"      -Type DateTime -AddToDefaultView
Add-PnPField -List "AtividadesQualidade" -DisplayName "Status"                -InternalName "Status"                 -Type Choice   -AddToDefaultView -Choices @("Não iniciado","Em progresso","Concluído")

# Definir valor padrão do Status
Set-PnPField -List "AtividadesQualidade" -Identity "Status" -Values @{DefaultValue = "Não iniciado"}
```

---

## 2. Adicionar Valores às Picklists (Solicitante e Projeto)

Após criar a lista, adicione os valores iniciais às colunas `Solicitante` e `Projeto`:

1. Acesse as **Configurações da Lista → Colunas → Solicitante**
2. Na seção **Opções de Escolha**, insira os valores (um por linha)
3. Repita para a coluna `Projeto`

> O Power Apps exibirá esses valores no ComboBox, e o usuário poderá gerenciar os valores diretamente pela configuração da lista SharePoint.

---

## 3. Criar o App no Power Apps

### 3.1 Novo Canvas App em branco

1. Acesse [make.powerapps.com](https://make.powerapps.com)
2. Clique em **+ Criar → Aplicativo de tela em branco**
3. Escolha **Tablet** como formato
4. Nomeie o app como **`Controle de Atividades – Quality`**

### 3.2 Conectar a lista SharePoint como fonte de dados

1. No painel lateral, clique em **Dados → + Adicionar dados**
2. Busque e selecione **SharePoint**
3. Insira a URL do site e selecione a lista **`AtividadesQualidade`**
4. Clique em **Conectar**

### 3.3 Criar as telas

Crie três telas e nomeie exatamente como abaixo:
- `TelaInicial`
- `TelaFormulario`
- `TelaDashboard`

### 3.4 Copiar as fórmulas dos arquivos YAML

Os arquivos YAML em `Src/` contêm a estrutura completa de cada tela.
Utilize-os como referência para criar os controles e inserir as fórmulas Power Fx:

| Arquivo                         | Tela correspondente   |
|---------------------------------|-----------------------|
| `Src/TelaInicial.fx.yaml`       | `TelaInicial`         |
| `Src/TelaFormulario.fx.yaml`    | `TelaFormulario`      |
| `Src/TelaDashboard.fx.yaml`     | `TelaDashboard`       |
| `App.fx.yaml`                   | Propriedade `OnStart` do App |

> **Dica:** Use o recurso **Exibição de código (Code view)** do Power Apps Studio (disponível em ambientes com recurso experimental ativado) para colar os blocos YAML diretamente.

---

## 4. Configurar o Evento OnStart do App

No Power Apps Studio:
1. Selecione o objeto **App** na árvore de controles
2. Vá à propriedade **OnStart**
3. Cole a fórmula do arquivo `App.fx.yaml`

---

## 5. Regras Automáticas – Verificação

### Status padrão "Não iniciado"
- Na propriedade `Default` do dropdown `ddStatus` na `TelaFormulario`:
  ```
  If(varModoFormulario = "Novo", "Não iniciado", varItemSelecionado.Status.Value)
  ```

### Auto-conclusão ao preencher Data_Termino_Real
- Na propriedade `Update` do DataCard `DataCard_Status` na `TelaFormulario`:
  ```
  If(
      !IsBlank(DataCard_DataTerminoReal.dpDataTerminoReal.SelectedDate),
      {Value: "Concluído"},
      ddStatus.Selected
  )
  ```
  Esta fórmula garante que, ao submeter o formulário com `Data_Termino_Real` preenchida,
  o campo `Status` é automaticamente enviado como "Concluído" independentemente do valor
  selecionado no dropdown.

---

## 6. Publicar e Compartilhar

1. Clique em **Arquivo → Salvar**
2. Clique em **Publicar**
3. Em **Compartilhar**, adicione os membros do time de Qualidade
4. Certifique-se de que eles também têm acesso à lista SharePoint

---

## 7. Gerenciar Picklists (Solicitante e Projeto)

Para adicionar novos valores às picklists `Solicitante` e `Projeto`:

1. Acesse a lista SharePoint `AtividadesQualidade`
2. Vá em **Configurações da Lista → Colunas → [Solicitante ou Projeto]**
3. Adicione ou edite os valores na seção **Opções de Escolha**
4. Os novos valores aparecerão automaticamente no app (via `Choices()`)

---

## 8. Manutenção

| Tarefa                              | Como fazer                                      |
|-------------------------------------|-------------------------------------------------|
| Adicionar novo Solicitante          | Editar coluna na lista SharePoint               |
| Adicionar novo Projeto              | Editar coluna na lista SharePoint               |
| Alterar opções de Status            | Editar coluna `Status` na lista SharePoint + atualizar lógica no app |
| Backup dos dados                    | Exportar lista SharePoint como Excel            |
| Atualizar o app                     | Power Apps Studio → Editar → Publicar           |
