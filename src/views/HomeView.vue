<script>
import { defineComponent, reactive, computed } from 'vue'
import PaletteCard from '@/components/PaletteCard.vue'
import PalettesGrid from '@/components/PalettesGrid.vue'
import useFavorites from '@/composables/useFavorites.composable'
import usePaletteStore from '@/composables/usePaletteStore.composable'

export default defineComponent({
  name: 'HomeView',
  components: {
    PaletteCard,
    PalettesGrid
  },
  setup() {
    const { state, replacePaletteItem } = usePaletteStore()
    const palettes = computed(() => state.palettes);
    const { addPaletteToFavorites } = useFavorites()
    const animate = reactive([])

    /**
     * Replaces the palette at the specified index with a new palette.
     *
     * @param {number} index - The index of the palette to replace. Must be a non-negative integer.
     * @return {undefined}
     */
    // eslint-disable-next-line no-unused-vars
    function replacePalette(item, index = -1) {
      if (index < 0) {
        return
      }
      addPaletteToFavorites(item)
      replacePaletteItem(index)
      animate[index] = true

      setTimeout(() => {
        animate[index] = false
      }, 500)

    }

    return {
      palettes,
      animate,
      replacePalette
    }
  }
})
</script>

<template>
  <main>
    <h2>Discover Palettes</h2>
    <PalettesGrid class="home-view">
      <PaletteCard
        v-for="(item, index) in palettes"
        :key="item.id"
        :item="item"
        class="card"
        :class="{ hidden: animate[index] }"
        @toggle-favorite="replacePalette(item, index)"
      />
    </PalettesGrid>
  </main>
</template>

<style lang="scss" scoped>
h2 {
  margin-bottom: 3rem;
}

.home-view {

  .card {
    opacity: 1;
    transition: all 0.3s ease;

    &.hidden {
      opacity: 0;
      transform: translateY(-30px);
    }
  }
}
</style>
