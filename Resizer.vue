<template>
  <div
    class="resizer"
    :class="[
      direction,
      { 'resizer-horizontal': direction === 'horizontal', 'resizer-vertical': direction === 'vertical' }
    ]"
    @mousedown="startResize"
  ></div>
</template>

<script setup lang="ts">
interface Props {
  direction: 'horizontal' | 'vertical',
  containerSelector?: string,
  minPercent?: number,
  maxPercent?: number,
  modelValue: number
}

const props = withDefaults(defineProps<Props>(), {
  containerSelector: '',
  minPercent: 20,
  maxPercent: 80
});

const emit = defineEmits<{
  (e: 'update:modelValue', size: number): void
}>();

function startResize(event: MouseEvent) {
  event.preventDefault();
  document.body.style.userSelect = 'none';

  const target = event.currentTarget as HTMLElement;
  const container = props.containerSelector 
    ? document.querySelector(props.containerSelector)
    : target.parentElement;

  if (!container) return;

  let currentPercent = props.modelValue;
  let lastPosition = props.direction === 'horizontal' ? event.clientX : event.clientY;

  function onMouseMove(e: MouseEvent) {
    if (!container) return;
    
    const currentPosition = props.direction === 'horizontal' ? e.clientX : e.clientY;
    const difference = currentPosition - lastPosition;
    lastPosition = currentPosition;

    const containerSize = props.direction === 'horizontal' 
      ? container.clientWidth 
      : container.clientHeight;
    
    const deltaPercent = (difference / containerSize) * 100;
    const newPercent = currentPercent + deltaPercent;

    if (newPercent >= props.minPercent && newPercent <= props.maxPercent) {
      currentPercent = newPercent;
      emit('update:modelValue', newPercent);
    }
  }

  function onMouseUp() {
    document.body.style.userSelect = '';
    document.removeEventListener('mousemove', onMouseMove);
    document.removeEventListener('mouseup', onMouseUp);
  }

  document.addEventListener('mousemove', onMouseMove);
  document.addEventListener('mouseup', onMouseUp);
}
</script>

<style scoped>
.resizer {
  position: relative;
  z-index: 100;
  flex: none;
  transition: background-color 0.2s;
  background-color: transparent;
}

.resizer.horizontal {
  margin: 3px;
  width: 3px;
  cursor: col-resize;
}

.resizer.vertical {
  margin: 3px;
  height: 3px;
  cursor: row-resize;
}

.resizer:hover {
  background-color: #409eff;
}
</style>
