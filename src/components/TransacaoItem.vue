<script setup>
import { ref } from 'vue'
import { formatarMoeda, formatarData } from '../utils/formatar.js'
import { iconeDaCategoria } from '../data/categorias.js'

// Uma linha da lista de transações. É REUTILIZADO pelo v-for do TransacaoLista.vue:
// o mesmo componente aparece várias vezes, cada um com uma transação diferente (PROP).
const props = defineProps({
  transacao: { type: Object, required: true },
  visivel: { type: Boolean, default: true }
})

// EVENTO personalizado: avisa o componente pai qual transação deve ser removida.
const emit = defineEmits(['remover'])

// REATIVIDADE: "confirmando" controla se o botão mostra "Excluir" ou "Confirmar/Cancelar".
const confirmando = ref(false)

function pedirConfirmacao() {
  confirmando.value = true
}

function cancelar() {
  confirmando.value = false
}

function confirmar() {
  emit('remover', props.transacao.id)
}
</script>

<template>
  <li class="item" :class="[transacao.tipo, { confirmando }]">
    <span class="icone" aria-hidden="true">
      {{ iconeDaCategoria(transacao.tipo, transacao.categoria) }}
    </span>

    <div class="info">
      <p class="descricao">{{ transacao.descricao }}</p>
      <p class="detalhes">{{ transacao.categoria }} · {{ formatarData(transacao.data) }}</p>
    </div>

    <p v-if="visivel" class="valor">
      {{ transacao.tipo === 'receita' ? '+' : '−' }} {{ formatarMoeda(transacao.valor) }}
    </p>
    <p v-else class="valor oculto">•••••</p>

    <div class="acoes">
      <button
        v-if="!confirmando"
        class="botao-icone"
        type="button"
        :aria-label="`Excluir ${transacao.descricao}`"
        @click="pedirConfirmacao"
      >
        🗑️
      </button>
      <template v-else>
        <button class="botao-mini perigo" type="button" @click="confirmar">Excluir</button>
        <button class="botao-mini" type="button" @click="cancelar">Cancelar</button>
      </template>
    </div>
  </li>
</template>

<style scoped>
.item {
  display: grid;
  grid-template-columns: auto minmax(0, 1fr) auto auto;
  align-items: center;
  gap: 12px;
  padding: 12px 4px;
  border-bottom: 1px solid var(--cor-borda);
}

/* Quando está pedindo confirmação, os botões ocupam uma linha própria embaixo */
.item.confirmando .acoes {
  grid-column: 1 / -1;
  justify-content: flex-end;
}

.item:last-child {
  border-bottom: none;
}

.icone {
  width: 40px;
  height: 40px;
  flex-shrink: 0;
  display: grid;
  place-items: center;
  font-size: 1.2rem;
  border-radius: 11px;
  background: var(--cor-despesa-fundo);
}

.receita .icone {
  background: var(--cor-receita-fundo);
}

.info {
  min-width: 0;
}

.descricao {
  font-weight: 600;
  overflow-wrap: anywhere;
}

.detalhes {
  font-size: 0.82rem;
  color: var(--cor-texto-suave);
}

.valor {
  font-weight: 700;
  font-variant-numeric: tabular-nums;
  white-space: nowrap;
  color: var(--cor-despesa);
}

.receita .valor {
  color: var(--cor-receita);
}

.valor.oculto {
  color: var(--cor-texto-suave);
}

.acoes {
  display: flex;
  gap: 6px;
  align-items: center;
}

.botao-icone {
  width: 36px;
  height: 36px;
  display: grid;
  place-items: center;
  border: 1px solid transparent;
  border-radius: 10px;
  background: transparent;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.15s;
}

.botao-icone:hover {
  background: var(--cor-superficie-suave);
}

.botao-mini {
  padding: 7px 12px;
  border: 1px solid var(--cor-borda);
  border-radius: 8px;
  background: var(--cor-superficie);
  color: var(--cor-texto);
  font-size: 0.85rem;
  font-weight: 600;
  cursor: pointer;
}

.botao-mini:hover {
  background: var(--cor-superficie-suave);
}

.botao-mini.perigo {
  background: var(--cor-despesa);
  border-color: var(--cor-despesa);
  color: #ffffff;
}

.botao-mini.perigo:hover {
  filter: brightness(0.92);
}
</style>
