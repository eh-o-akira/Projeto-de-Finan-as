<script setup>
import { ref } from 'vue'
import TransacaoItem from './TransacaoItem.vue'

// Lista de transações com filtro (Todas / Receitas / Despesas).
// PROPS: recebe o array de transações e se os valores estão visíveis.
const props = defineProps({
  transacoes: { type: Array, required: true },
  visivel: { type: Boolean, default: true }
})

// EVENTO: repassa para o App.vue o pedido de remoção vindo do TransacaoItem.
const emit = defineEmits(['remover'])

const filtros = [
  { id: 'todas', rotulo: 'Todas' },
  { id: 'receita', rotulo: 'Receitas' },
  { id: 'despesa', rotulo: 'Despesas' }
]

// REATIVIDADE: quando o usuário clica em um filtro, "filtroAtivo" muda
// e a lista na tela é atualizada automaticamente.
const filtroAtivo = ref('todas')

function escolherFiltro(id) {
  filtroAtivo.value = id
}

function filtrar(id) {
  if (id === 'todas') {
    return props.transacoes
  }
  return props.transacoes.filter((transacao) => transacao.tipo === id)
}

// Transações do filtro atual, da mais recente para a mais antiga.
function listaVisivel() {
  return [...filtrar(filtroAtivo.value)].sort(
    (a, b) => b.data.localeCompare(a.data) || b.id - a.id
  )
}
</script>

<template>
  <section class="painel" aria-labelledby="titulo-lista">
    <div class="topo">
      <h2 id="titulo-lista" class="painel-titulo">Transações</h2>

      <div class="filtros" role="group" aria-label="Filtrar transações">
        <button
          v-for="filtro in filtros"
          :key="filtro.id"
          type="button"
          class="filtro"
          :class="{ ativo: filtroAtivo === filtro.id }"
          :aria-pressed="filtroAtivo === filtro.id"
          @click="escolherFiltro(filtro.id)"
        >
          {{ filtro.rotulo }}
          <span class="contagem">{{ filtrar(filtro.id).length }}</span>
        </button>
      </div>
    </div>

    <p v-if="listaVisivel().length === 0" class="vazio">
      {{
        filtroAtivo === 'todas'
          ? 'Nenhuma transação ainda. Adicione a primeira no formulário!'
          : 'Nenhuma transação neste filtro.'
      }}
    </p>

    <ul v-else class="itens">
      <TransacaoItem
        v-for="transacao in listaVisivel()"
        :key="transacao.id"
        :transacao="transacao"
        :visivel="visivel"
        @remover="emit('remover', $event)"
      />
    </ul>
  </section>
</template>

<style scoped>
.topo {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  flex-wrap: wrap;
  margin-bottom: 8px;
}

.topo .painel-titulo {
  margin-bottom: 0;
}

.filtros {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
}

.filtro {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 7px 14px;
  border: 1px solid var(--cor-borda);
  border-radius: 999px;
  background: var(--cor-superficie);
  color: var(--cor-texto);
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.15s, color 0.15s;
}

.filtro:hover {
  background: var(--cor-superficie-suave);
}

.filtro.ativo {
  background: var(--cor-primaria);
  border-color: var(--cor-primaria);
  color: var(--cor-sobre-primaria);
}

.contagem {
  min-width: 22px;
  padding: 1px 7px;
  border-radius: 999px;
  background: var(--cor-superficie-suave);
  color: var(--cor-texto-suave);
  font-size: 0.78rem;
  text-align: center;
}

.filtro.ativo .contagem {
  background: rgba(255, 255, 255, 0.25);
  color: inherit;
}

.itens {
  list-style: none;
  margin: 0;
  padding: 0;
}

.vazio {
  padding: 28px 8px;
  text-align: center;
  color: var(--cor-texto-suave);
}
</style>
