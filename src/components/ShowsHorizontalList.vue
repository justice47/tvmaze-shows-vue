<template>
  <div
    ref="container"
    class="grid grid-flow-col gap-4 overflow-scroll scrollbar-hide justify-start"
    @mouseenter="mouseEnter"
    @mouseleave="mouseLeave"
  >
    <ShowCard
      v-for="(show, index) in data"
      :key="index"
      :show="show"
    />
  </div>
</template>

<script setup lang="ts">
  import { ref, watch, nextTick, onBeforeUnmount } from 'vue';
  import ShowCard from '@/components/ShowCard.vue'

  interface Props {
    standardDirection?: boolean
    data?: any
  }

  const props = withDefaults(defineProps<Props>(), {
    standardDirection: true,
  });

  const scrollDirection = ref('left')

  const container = ref<HTMLDivElement | null>(null);
  const interval = ref(0);

  onBeforeUnmount(() => {
    destroyInterval();
  })

  watch(() => props.data, async () => {
    destroyInterval();

    container.value?.scroll(0, 0)

    await nextTick()

    if (!props.standardDirection) {
      container.value?.scroll(3000, 0)
      scrollDirection.value = 'right'
    }

    startScrollHandler()
  })

  const scrollHandlerLeft = () => {
    interval.value = setInterval(() => {
      container.value?.scroll(container.value?.scrollLeft + 0.5, 0)

      if (container.value!.offsetWidth + container.value!.scrollLeft >= container.value!.scrollWidth) {
        destroyInterval();

        scrollDirection.value = 'right'
        scrollHandlerRight();
      }
    }, 30)
  }

  const scrollHandlerRight = () => {
    interval.value = setInterval(() => {
      container.value?.scroll(container.value?.scrollLeft - 0.5, 0)

      if (container.value?.scrollLeft === 0) {
        destroyInterval();

        scrollDirection.value = 'left'
        scrollHandlerLeft();
      }
    }, 30)
  }

  const startScrollHandler = () => {
    if (scrollDirection.value === 'left') {
      scrollHandlerLeft();
    } else {
      scrollHandlerRight();
    }
  }

  const mouseEnter = () => {
    destroyInterval();
  }

  const mouseLeave = () => {
    startScrollHandler();
  }

  const destroyInterval = () => {
    clearInterval(interval.value)
  }
</script>