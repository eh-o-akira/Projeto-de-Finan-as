<script setup>
import { formatarMoeda } from '../utils/formatar.js'

// Card de resumo REUTILIZÁVEL: o App.vue usa este mesmo componente 3 vezes
// (Saldo, Receitas e Despesas), mudando apenas as PROPS.
defineProps({
  titulo: { type: String, required: true },
  valor: { type: Number, required: true },
  tipo: { type: String, default: 'saldo' }, // 'saldo' | 'receita' | 'despesa'
  icone: { type: String, default: '💰' },
  visivel: { type: Boolean, default: true }
})
</script>

<template>
  <article class="resumo-card" :class="[tipo, { negativo: tipo === 'saldo' && valor < 0 }]">
    <div class="icone" aria-hidden="true">{{ icone }}</div>
    <div class="texto">
      <h3 class="titulo">{{ titulo }}</h3>
      <p v-if="visivel" class="valor">{{ formatarMoeda(valor) }}</p>
      <p v-else class="valor oculto">R$ •••••</p>
    </div>
  </article>
</template>

<style scoped>
.resumo-card {
  display: flex;
  align-items: center;
  gap: 14px;
  padding: 18px;
  background: var(--cor-superficie);
  border: 1px solid var(--cor-borda);
  border-left: 5px solid var(--cor-primaria);
  border-radius: var(--raio);
  box-shadow: var(--sombra);
}

.resumo-card.receita {
  border-left-color: var(--cor-receita);
}

.resumo-card.despesa {
  border-left-color: var(--cor-despesa);
}

.icone {
  width: 46px;
  height: 46px;
  flex-shrink: 0;
  display: grid;
  place-items: center;
  font-size: 1.4rem;
  border-radius: 12px;
  background: var(--cor-primaria-clara);
}

.receita .icone {
  background: var(--cor-receita-fundo);
}

.despesa .icone {
  background: var(--cor-despesa-fundo);
}

.texto {
  min-width: 0;
}

.titulo {
  font-size: 0.85rem;
  font-weight: 600;
  color: var(--cor-texto-suave);
}

.valor {
  font-size: 1.45rem;
  font-weight: 700;
  font-variant-numeric: tabular-nums;
  overflow-wrap: anywhere;
}

.receita .valor {
  color: var(--cor-receita);
}

.despesa .valor {
  color: var(--cor-despesa);
}

.negativo .valor {
  color: var(--cor-despesa);
}

.oculto {
  letter-spacing: 1px;
  color: var(--cor-texto-suave);
}
</style>
