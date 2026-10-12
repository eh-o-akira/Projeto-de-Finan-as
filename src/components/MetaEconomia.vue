<script setup>
import { ref } from 'vue'
import { formatarMoeda } from '../utils/formatar.js'

// Meta de economia: mostra quanto do objetivo já foi alcançado (usando o saldo atual).
// PROPS: meta, saldo e visivel.
const props = defineProps({
  meta: { type: Number, required: true },
  saldo: { type: Number, required: true },
  visivel: { type: Boolean, default: true }
})

// EVENTO: quando o usuário salva uma meta nova, avisa o App.vue.
const emit = defineEmits(['atualizar-meta'])

// REATIVIDADE: "editando" mostra/esconde o formulário (v-show); "novaMeta" é o campo (v-model).
const editando = ref(false)
const novaMeta = ref(props.meta)
const erro = ref('')

// Quantos % da meta já foram alcançados (sempre entre 0 e 100).
function porcentagem() {
  if (props.meta <= 0) {
    return 0
  }
  const percentual = (props.saldo / props.meta) * 100
  return Math.min(100, Math.max(0, Math.round(percentual)))
}

function alternarEdicao() {
  editando.value = !editando.value
  novaMeta.value = props.meta
  erro.value = ''
}

function salvarMeta() {
  if (typeof novaMeta.value !== 'number' || !(novaMeta.value > 0)) {
    erro.value = 'Digite uma meta maior que zero.'
    return
  }
  emit('atualizar-meta', Math.round(novaMeta.value * 100) / 100)
  editando.value = false
  erro.value = ''
}
</script>

<template>
  <section class="painel meta" aria-labelledby="titulo-meta">
    <div class="topo">
      <h2 id="titulo-meta" class="painel-titulo">Meta de economia</h2>
      <button class="botao-texto" type="button" @click="alternarEdicao">
        {{ editando ? 'Fechar' : 'Editar meta' }}
      </button>
    </div>

    <p class="explicacao">Acompanhe quanto do seu objetivo você já alcançou com o saldo atual.</p>

    <div
      class="barra"
      role="progressbar"
      aria-label="Progresso da meta de economia"
      aria-valuemin="0"
      aria-valuemax="100"
      :aria-valuenow="porcentagem()"
    >
      <div class="preenchimento" :style="{ width: porcentagem() + '%' }"></div>
    </div>

    <p class="progresso">
      <strong>{{ porcentagem() }}%</strong> da meta
    </p>

    <p v-if="visivel" class="valores">{{ formatarMoeda(saldo) }} de {{ formatarMoeda(meta) }}</p>
    <p v-else class="valores">R$ ••••• de R$ •••••</p>

    <p v-if="meta > 0 && saldo >= meta" class="situacao conquista">🎉 Meta atingida! Parabéns!</p>
    <p v-else-if="visivel" class="situacao">
      Faltam {{ formatarMoeda(Math.max(meta - saldo, 0)) }} para chegar lá.
    </p>
    <p v-else class="situacao">Faltam R$ ••••• para chegar lá.</p>

    <form v-show="editando" class="editar" novalidate @submit.prevent="salvarMeta">
      <div class="campo">
        <label class="rotulo" for="campo-meta">Nova meta (R$)</label>
        <input
          id="campo-meta"
          v-model.number="novaMeta"
          class="entrada"
          type="number"
          step="0.01"
          min="0"
          inputmode="decimal"
        />
      </div>
      <p v-if="erro" class="mensagem erro" role="alert">{{ erro }}</p>
      <button class="botao botao-primario" type="submit">Salvar meta</button>
    </form>
  </section>
</template>

<style scoped>
.topo {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}

.topo .painel-titulo {
  margin-bottom: 0;
}

.explicacao {
  margin: 6px 0 16px;
  font-size: 0.9rem;
  color: var(--cor-texto-suave);
}

.barra {
  height: 14px;
  border-radius: 999px;
  background: var(--cor-superficie-suave);
  border: 1px solid var(--cor-borda);
  overflow: hidden;
}

.preenchimento {
  height: 100%;
  border-radius: 999px;
  background: linear-gradient(90deg, #14998c, #f5b83d);
  transition: width 0.4s ease;
}

.progresso {
  margin-top: 10px;
  font-size: 1.05rem;
}

.valores {
  font-weight: 600;
  font-variant-numeric: tabular-nums;
  overflow-wrap: anywhere;
}

.situacao {
  margin-top: 4px;
  font-size: 0.9rem;
  color: var(--cor-texto-suave);
}

.conquista {
  color: var(--cor-receita);
  font-weight: 700;
}

.editar {
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-top: 18px;
  padding-top: 16px;
  border-top: 1px dashed var(--cor-borda);
}
</style>
