<script setup>
import { ref } from 'vue'
import { categoriasReceita, categoriasDespesa } from '../data/categorias.js'
import { hojeIso } from '../utils/formatar.js'

// Formulário para cadastrar uma nova receita ou despesa.
// EVENTO: quando os dados são válidos, avisa o App.vue com o evento "adicionar".
const emit = defineEmits(['adicionar'])

const tiposDeTransacao = [
  { valor: 'despesa', rotulo: 'Despesa', icone: '📉' },
  { valor: 'receita', rotulo: 'Receita', icone: '📈' }
]

// REATIVIDADE: cada campo do formulário é um ref() ligado ao input com v-model.
const tipo = ref('despesa')
const descricao = ref('')
const valor = ref('')
const categoria = ref(categoriasDespesa[0].nome)
const data = ref(hojeIso())
const erro = ref('')
const sucesso = ref('')

function listaDeCategorias(tipoEscolhido) {
  return tipoEscolhido === 'receita' ? categoriasReceita : categoriasDespesa
}

// Quando o tipo muda, a categoria volta para a primeira opção da lista nova.
function trocarTipo(novoTipo) {
  categoria.value = listaDeCategorias(novoTipo)[0].nome
}

function limparMensagens() {
  erro.value = ''
  sucesso.value = ''
}

function enviar() {
  const textoDescricao = descricao.value.trim()

  if (textoDescricao === '') {
    erro.value = 'Informe uma descrição para a transação.'
    sucesso.value = ''
    return
  }

  if (typeof valor.value !== 'number' || !(valor.value > 0)) {
    erro.value = 'Informe um valor maior que zero.'
    sucesso.value = ''
    return
  }

  if (data.value === '') {
    erro.value = 'Escolha a data da transação.'
    sucesso.value = ''
    return
  }

  emit('adicionar', {
    id: Date.now(),
    descricao: textoDescricao,
    valor: Math.round(valor.value * 100) / 100,
    tipo: tipo.value,
    categoria: categoria.value,
    data: data.value
  })

  erro.value = ''
  sucesso.value = tipo.value === 'receita' ? 'Receita adicionada!' : 'Despesa adicionada!'
  descricao.value = ''
  valor.value = ''
}
</script>

<template>
  <form class="painel formulario" novalidate @submit.prevent="enviar" @input="limparMensagens">
    <h2 class="painel-titulo">Nova transação</h2>

    <fieldset class="tipos">
      <legend class="rotulo">Tipo</legend>
      <label
        v-for="opcao in tiposDeTransacao"
        :key="opcao.valor"
        class="tipo"
        :class="[opcao.valor, { marcado: tipo === opcao.valor }]"
      >
        <input
          v-model="tipo"
          type="radio"
          name="tipo"
          :value="opcao.valor"
          @change="trocarTipo(opcao.valor)"
        />
        <span>{{ opcao.icone }} {{ opcao.rotulo }}</span>
      </label>
    </fieldset>

    <div class="campo">
      <label class="rotulo" for="campo-descricao">Descrição</label>
      <input
        id="campo-descricao"
        v-model="descricao"
        class="entrada"
        type="text"
        maxlength="60"
        placeholder="Ex.: Compras no mercado"
        autocomplete="off"
      />
    </div>

    <div class="linha">
      <div class="campo">
        <label class="rotulo" for="campo-valor">Valor (R$)</label>
        <input
          id="campo-valor"
          v-model.number="valor"
          class="entrada"
          type="number"
          step="0.01"
          min="0"
          inputmode="decimal"
          placeholder="0,00"
        />
      </div>

      <div class="campo">
        <label class="rotulo" for="campo-data">Data</label>
        <input id="campo-data" v-model="data" class="entrada" type="date" />
      </div>
    </div>

    <div class="campo">
      <label class="rotulo" for="campo-categoria">Categoria</label>
      <select id="campo-categoria" v-model="categoria" class="entrada">
        <option v-for="item in listaDeCategorias(tipo)" :key="item.nome" :value="item.nome">
          {{ item.icone }} {{ item.nome }}
        </option>
      </select>
    </div>

    <p v-if="erro" class="mensagem erro" role="alert">{{ erro }}</p>
    <p v-show="sucesso !== ''" class="mensagem sucesso" role="status">{{ sucesso }}</p>

    <button class="botao botao-primario" type="submit">Adicionar transação</button>
  </form>
</template>

<style scoped>
.formulario {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.formulario .painel-titulo {
  margin-bottom: 0;
}

.tipos {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin: 0;
  padding: 0;
  border: none;
  min-width: 0;
}

.tipos .rotulo {
  grid-column: 1 / -1;
  padding: 0;
  margin-bottom: 2px;
}

.tipo {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 11px 12px;
  border: 2px solid var(--cor-borda);
  border-radius: var(--raio-pequeno);
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.15s, border-color 0.15s;
}

.tipo:hover {
  background: var(--cor-superficie-suave);
}

.tipo input {
  position: absolute;
  opacity: 0;
  pointer-events: none;
}

.tipo.despesa.marcado {
  border-color: var(--cor-despesa);
  background: var(--cor-despesa-fundo);
  color: var(--cor-despesa);
}

.tipo.receita.marcado {
  border-color: var(--cor-receita);
  background: var(--cor-receita-fundo);
  color: var(--cor-receita);
}

.tipo:has(input:focus-visible) {
  outline: 3px solid var(--cor-primaria);
  outline-offset: 2px;
}

.linha {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

@media (max-width: 420px) {
  .linha {
    grid-template-columns: 1fr;
  }
}
</style>
