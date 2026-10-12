# 💰 Centavo — controle financeiro pessoal

**Centavo** é um sistema de finanças pessoais em formato **One Page**, feito com **Vue.js 3** para a atividade em equipe da disciplina **Front-End Frameworks (UNAMA)**. O tema sorteado para a equipe foi **Sistema de Finanças**.

Com o Centavo, a pessoa registra receitas e despesas, acompanha o saldo e acompanha o progresso de uma meta de economia, tudo em uma única página, sem navegação entre telas.

## ✨ Funcionalidades

- **Resumo financeiro:** saldo atual, total de receitas e total de despesas.
- **Nova transação:** formulário com tipo (receita ou despesa), descrição, valor, data e categoria, com validação dos campos.
- **Lista de transações:** ordenada da mais recente para a mais antiga, com filtro (Todas, Receitas, Despesas) e exclusão com confirmação.
- **Meta de economia:** barra de progresso baseada no saldo atual, com meta editável.
- **Ocultar valores:** botão no cabeçalho que esconde todos os valores da tela.
- **Dados salvos no navegador** (`localStorage`): ao recarregar a página, as informações continuam lá.
- **Responsivo** (celular, tablet e computador) e com **tema escuro automático**.

## 🚀 Como executar

Pré-requisito: [Node.js](https://nodejs.org/) versão **20.19 ou superior** (ou 22.12+).

```bash
# 1. Instalar as dependências
npm install

# 2. Rodar em modo de desenvolvimento
npm run dev
```

Depois, abra o endereço mostrado no terminal (normalmente `http://localhost:5173`).

Para gerar a versão final e testá-la:

```bash
npm run build
npm run preview
```

## 🧩 Tecnologias e limites da atividade

- Vue.js 3 com `<script setup>` e Vite.
- **Sem** Vue Router, **sem** bibliotecas externas de componentes, **sem** frameworks CSS (Bootstrap, Tailwind), **sem** bibliotecas de estado.
- Apenas os fundamentos vistos em aula: componentes `.vue`, props, `ref()`, eventos, `v-model`, `v-if`, `v-show`, `v-for` e CSS.

## 📁 Estrutura de arquivos

```
centavo/
├── index.html
├── package.json
├── vite.config.js
└── src/
    ├── main.js                    # inicia o app
    ├── App.vue                    # componente principal (junta tudo)
    ├── assets/
    │   └── global.css             # variáveis de cor e estilos compartilhados
    ├── components/
    │   ├── AppHeader.vue          # cabeçalho
    │   ├── ResumoCard.vue         # card de resumo (reutilizado 3 vezes)
    │   ├── TransacaoForm.vue      # formulário de nova transação
    │   ├── MetaEconomia.vue       # meta de economia com barra de progresso
    │   ├── TransacaoLista.vue     # lista com filtro
    │   └── TransacaoItem.vue      # uma linha da lista (reutilizada com v-for)
    ├── data/
    │   ├── categorias.js          # categorias do formulário
    │   └── transacoesIniciais.js  # dados de exemplo
    └── utils/
        ├── armazenamento.js       # leitura/gravação no localStorage
        └── formatar.js            # formatação de moeda e datas
```

## 🧱 Componentes

| Componente | Para que serve | Props | Eventos |
| --- | --- | --- | --- |
| `AppHeader` | Logo, data de hoje e botão de ocultar valores | `valoresVisiveis` | `alternar-valores` |
| `ResumoCard` | Mostra um valor (saldo, receitas ou despesas) | `titulo`, `valor`, `tipo`, `icone`, `visivel` | — |
| `TransacaoForm` | Cadastra uma nova receita ou despesa | — | `adicionar` |
| `MetaEconomia` | Mostra e edita a meta de economia | `meta`, `saldo`, `visivel` | `atualizar-meta` |
| `TransacaoLista` | Lista as transações e aplica o filtro | `transacoes`, `visivel` | `remover` |
| `TransacaoItem` | Uma transação da lista | `transacao`, `visivel` | `remover` |

### Reúso de componentes

- `ResumoCard` é usado **3 vezes** no `App.vue` (Saldo, Receitas e Despesas), mudando apenas as props.
- `TransacaoItem` é usado **uma vez para cada transação** através do `v-for` do `TransacaoLista`.

## 📚 Conceitos de Vue aplicados (e onde ver no código)

| Conceito | Onde |
| --- | --- |
| **Componentes `.vue` com `<script setup>`** | Todos os arquivos de `src/components/` e o `App.vue` |
| **Props** | `ResumoCard.vue` (`defineProps`) recebendo os valores enviados pelo `App.vue` |
| **`ref()` (reatividade)** | `App.vue` (`transacoes`, `meta`, `valoresVisiveis`), `TransacaoLista.vue` (`filtroAtivo`), `TransacaoForm.vue` (campos), `TransacaoItem.vue` (`confirmando`) |
| **Eventos** | `@click` (filtros, botões), `@submit.prevent` (formulários), `@input`, `@change` e eventos personalizados com `defineEmits` |
| **`v-model`** | `TransacaoForm.vue` (descrição, valor, data, tipo e categoria) e `MetaEconomia.vue` (nova meta) |
| **`v-if` / `v-else`** | Mensagens de erro, lista vazia, valores ocultos (`ResumoCard`, `TransacaoItem`) |
| **`v-show`** | Formulário de edição da meta (`MetaEconomia.vue`) e mensagem de sucesso (`TransacaoForm.vue`) |
| **`v-for`** | Lista de transações e filtros (`TransacaoLista.vue`), tipos e categorias (`TransacaoForm.vue`) |

## 🎨 Como o CSS foi usado

- **Variáveis CSS** (`:root`) em `src/assets/global.css` definem cores, sombras e bordas, permitindo trocar o visual do app em um só lugar.
- **Estilos globais compartilhados** (painel, botões, campos e mensagens) ficam no `global.css`.
- **Estilos de cada componente** ficam no próprio `.vue`, dentro de `<style scoped>`, valendo apenas para aquele componente.
- **Layout responsivo** com CSS Grid, Flexbox e `@media`, adaptando a página para celulares.
- **Tema escuro automático** com `prefers-color-scheme` e respeito a `prefers-reduced-motion`.
- Nenhuma biblioteca ou framework CSS externo foi utilizado.

## 🤖 Uso de Inteligência Artificial

A IA foi utilizada como ferramenta de apoio no desenvolvimento, sempre pedindo que respeitasse o conteúdo estudado e as limitações da atividade (apenas Vue com `<script setup>`, sem bibliotecas externas e sem Vue Router). Abaixo, os principais prompts e a etapa em que cada um foi usado.

| Etapa | Prompt |
| --- | --- |
| **1. Planejamento e divisão em componentes** | "Tenho uma aplicação Vue sobre um sistema de finanças chamado Centavo. Sugira uma divisão em componentes simples e reutilizáveis. Utilize apenas Vue com `<script setup>`, sem bibliotecas externas e sem Vue Router. A aplicação deve ser One Page, ter pelo menos 4 componentes `.vue`, usar props, `ref()`, eventos, `v-if`, `v-show`, `v-for`, `v-model` e CSS próprio." |
| **2. Componente com props e reúso** | "Crie um componente Vue simples que receba título, valor e tipo através de props. Utilize `<script setup>` e CSS próprio, sem bibliotecas externas." |
| **3. Reutilização com props** | "Como posso reutilizar este componente para apresentar diferentes informações usando props?" (usado para o card de resumo: saldo, receitas e despesas) |
| **4. Reatividade e eventos** | "Sugira uma interação simples utilizando `ref()` e `@click` para adicionar reatividade a este componente." (usado nos filtros da lista de transações) |
| **5. Formulário** | "Como utilizar `v-model` neste formulário Vue? Explique o funcionamento do código." (usado no formulário de nova transação) |
| **6. Lista com componente reutilizável** | "Como utilizar `v-for` para apresentar esta lista utilizando um componente reutilizável?" (usado na lista de transações com `TransacaoItem`) |
| **7. Estilização** | "Crie o CSS do aplicativo usando apenas CSS puro, com variáveis, layout responsivo para celular e tema escuro, sem Bootstrap, Tailwind ou outras bibliotecas." |
| **8. Revisão final** | "Revise este código utilizando somente os conceitos básicos de Vue: componentes, props, `ref`, eventos, `v-if`, `v-for` e `v-model`. Não utilize bibliotecas externas nem conceitos avançados." |

## 👥 Equipe

| Integrante | Contribuição |
| --- | --- |
| Samuel Teixeira | Programação e código |
| Enzo Vida | Vídeo de apresentação |
| João Victor Bacelar | Auxílio na gravação e edição do vídeo |
| Heitor Freire | Narração do vídeo |

Projeto desenvolvido para a disciplina **Front-End Frameworks** — Análise e Desenvolvimento de Sistemas, UNAMA.
