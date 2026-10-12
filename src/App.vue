<script setup>
import { ref } from 'vue'
import AppHeader from './components/AppHeader.vue'
import ResumoCard from './components/ResumoCard.vue'
import TransacaoForm from './components/TransacaoForm.vue'
import MetaEconomia from './components/MetaEconomia.vue'
import TransacaoLista from './components/TransacaoLista.vue'
import { transacoesIniciais } from './data/transacoesIniciais.js'
import { lerArmazenado, salvarArmazenado } from './utils/armazenamento.js'

const CHAVE_TRANSACOES = 'centavo:transacoes'
const CHAVE_META = 'centavo:meta'
const META_PADRAO = 5000

function copiarExemplos() {
  return transacoesIniciais.map((transacao) => ({ ...transacao }))
}

function carregarTransacoes() {
  const salvas = lerArmazenado(CHAVE_TRANSACOES, null)
  return Array.isArray(salvas) ? salvas : copiarExemplos()
}

function carregarMeta() {
  const salva = lerArmazenado(CHAVE_META, META_PADRAO)
  return typeof salva === 'number' && salva > 0 ? salva : META_PADRAO
}

// ---------- ESTADO REATIVO (ref) ----------
// Sempre que um destes valores muda, a tela é atualizada sozinha.
const transacoes = ref(carregarTransacoes())
const meta = ref(carregarMeta())
const valoresVisiveis = ref(true)

// ---------- CÁLCULOS ----------
// Soma o valor de todas as transações de um tipo ('receita' ou 'despesa').
function somar(tipo) {
  const total = transacoes.value
    .filter((transacao) => transacao.tipo === tipo)
    .reduce((soma, transacao) => soma + transacao.valor, 0)
  return Math.round(total * 100) / 100
}

function saldo() {
  return Math.round((somar('receita') - somar('despesa')) * 100) / 100
}

// ---------- AÇÕES (chamadas pelos eventos dos componentes filhos) ----------
function adicionarTransacao(novaTransacao) {
  transacoes.value.unshift(novaTransacao)
  salvarArmazenado(CHAVE_TRANSACOES, transacoes.value)
}

function removerTransacao(id) {
  transacoes.value = transacoes.value.filter((transacao) => transacao.id !== id)
  salvarArmazenado(CHAVE_TRANSACOES, transacoes.value)
}

function atualizarMeta(novaMeta) {
  meta.value = novaMeta
  salvarArmazenado(CHAVE_META, novaMeta)
}

function alternarValores() {
  valoresVisiveis.value = !valoresVisiveis.value
}

function restaurarExemplos() {
  transacoes.value = copiarExemplos()
  meta.value = META_PADRAO
  salvarArmazenado(CHAVE_TRANSACOES, transacoes.value)
  salvarArmazenado(CHAVE_META, META_PADRAO)
}
</script>

<template>
  <AppHeader :valores-visiveis="valoresVisiveis" @alternar-valores="alternarValores" />

  <main class="conteudo">
    <!-- REÚSO DE COMPONENTE: o mesmo ResumoCard aparece 3 vezes, com props diferentes -->
    <section class="resumo" aria-label="Resumo financeiro">
      <ResumoCard
        titulo="Saldo atual"
        tipo="saldo"
        icone="💰"
        :valor="saldo()"
        :visivel="valoresVisiveis"
      />
      <ResumoCard
        titulo="Receitas"
        tipo="receita"
        icone="📈"
        :valor="somar('receita')"
        :visivel="valoresVisiveis"
      />
      <ResumoCard
        titulo="Despesas"
        tipo="despesa"
        icone="📉"
        :valor="somar('despesa')"
        :visivel="valoresVisiveis"
      />
    </section>

    <div class="colunas">
      <TransacaoForm @adicionar="adicionarTransacao" />
      <MetaEconomia
        :meta="meta"
        :saldo="saldo()"
        :visivel="valoresVisiveis"
        @atualizar-meta="atualizarMeta"
      />
    </div>

    <TransacaoLista
      :transacoes="transacoes"
      :visivel="valoresVisiveis"
      @remover="removerTransacao"
    />
  </main>

  <footer class="rodape">
    <p>Centavo · Projeto de Front-End Frameworks, feito com Vue.js</p>
    <button class="botao-texto" type="button" @click="restaurarExemplos">
      Restaurar dados de exemplo
    </button>
  </footer>
</template>

<style scoped>
.conteudo {
  max-width: 1040px;
  margin: 0 auto;
  padding: 24px 16px 32px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.resumo {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 16px;
}

.colunas {
  display: grid;
  grid-template-columns: minmax(0, 1.15fr) minmax(0, 1fr);
  gap: 20px;
  align-items: start;
}

.rodape {
  max-width: 1040px;
  margin: 0 auto;
  padding: 8px 16px 32px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px 16px;
  flex-wrap: wrap;
  font-size: 0.85rem;
  color: var(--cor-texto-suave);
}

@media (max-width: 820px) {
  .colunas {
    grid-template-columns: minmax(0, 1fr);
  }
}

@media (max-width: 640px) {
  .resumo {
    grid-template-columns: minmax(0, 1fr);
  }

  .rodape {
    justify-content: center;
    text-align: center;
  }
}
</style>
