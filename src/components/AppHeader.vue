<script setup>
// Cabeçalho do app.
// PROP: "valoresVisiveis" diz se os valores estão aparecendo ou escondidos.
// EVENTO: ao clicar no botão, avisa o App.vue com o evento "alternar-valores".
defineProps({
  valoresVisiveis: { type: Boolean, default: true }
})

const emit = defineEmits(['alternar-valores'])

const dataHoje = new Date().toLocaleDateString('pt-BR', {
  weekday: 'long',
  day: 'numeric',
  month: 'long',
  year: 'numeric'
})
</script>

<template>
  <header class="cabecalho">
    <div class="cabecalho-conteudo">
      <div class="marca">
        <svg class="moeda" viewBox="0 0 64 64" aria-hidden="true">
          <circle cx="32" cy="32" r="30" fill="#f5b83d" />
          <circle cx="32" cy="32" r="23" fill="none" stroke="#b7791f" stroke-width="3" />
          <text x="32" y="42" font-size="28" font-weight="700" text-anchor="middle" fill="#7a4a0e">C</text>
        </svg>
        <div>
          <h1>Centavo</h1>
          <p class="slogan">Seu dinheiro, centavo por centavo.</p>
        </div>
      </div>

      <div class="acoes">
        <p class="data">{{ dataHoje }}</p>
        <button
          class="botao botao-claro"
          type="button"
          :aria-pressed="!valoresVisiveis"
          @click="emit('alternar-valores')"
        >
          {{ valoresVisiveis ? '🙈 Ocultar valores' : '👁️ Mostrar valores' }}
        </button>
      </div>
    </div>
  </header>
</template>

<style scoped>
.cabecalho {
  background: linear-gradient(135deg, #0b4f4a 0%, #0f766e 55%, #14998c 100%);
  color: #ffffff;
  padding: 22px 16px;
}

.cabecalho-conteudo {
  max-width: 1040px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  flex-wrap: wrap;
}

.marca {
  display: flex;
  align-items: center;
  gap: 14px;
}

.moeda {
  width: 52px;
  height: 52px;
  flex-shrink: 0;
  filter: drop-shadow(0 3px 6px rgba(0, 0, 0, 0.25));
}

h1 {
  font-size: 1.7rem;
  letter-spacing: 0.5px;
  line-height: 1.1;
}

.slogan {
  font-size: 0.92rem;
  opacity: 0.88;
}

.acoes {
  display: flex;
  align-items: center;
  gap: 16px;
  flex-wrap: wrap;
}

.data {
  font-size: 0.92rem;
  opacity: 0.9;
}

.data::first-letter {
  text-transform: uppercase;
}

@media (max-width: 640px) {
  .acoes {
    width: 100%;
    justify-content: space-between;
  }
}
</style>
