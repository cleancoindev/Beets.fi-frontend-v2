<script setup lang="ts">
import { ref, computed, watch } from 'vue';

type PopoverTrigger = 'click' | 'hover';

type Props = {
  trigger?: PopoverTrigger;
  align?: string;
  verticalAlign?: string;
  closeOnClickInside?: boolean;
};

const props = withDefaults(defineProps<Props>(), {
  trigger: 'click',
  align: 'right',
  verticalAlign: 'top',
  closeOnClickInside: false
});

const emit = defineEmits<{
  (e: 'show'): void;
  (e: 'hide'): void;
}>();

/**
 * STATE
 */
const popoverOpened = ref(false);

/**
 * COMPUTED
 */
const popoverWrapperClasses = computed(() => ({
  'bal-popover-wrapper-visible': popoverOpened.value,
  [`${props.align}-0`]: true,
  [`${props.verticalAlign}-full`]: true
}));

watch(popoverOpened, () => {
  if (popoverOpened.value) {
    emit('show');
  } else {
    emit('hide');
  }
});

/**
 * METHODS
 */
function togglePopover() {
  popoverOpened.value = !popoverOpened.value;
}

function showPopover() {
  popoverOpened.value = true;
}

function hidePopover() {
  popoverOpened.value = false;
}

function handleClickOutside() {
  if (props.trigger === 'click') {
    hidePopover();
  }
}
function handleClickInside() {
  props.closeOnClickInside && hidePopover();
}
</script>

<template>
  <div class="relative" v-click-outside="handleClickOutside">
    <div
      class="bal-popover-activator group"
      @click="trigger === 'click' && togglePopover()"
      @mouseenter="trigger === 'hover' && showPopover()"
      @mouseleave="trigger === 'hover' && hidePopover()"
    >
      <slot name="activator" />
    </div>
    <div
      @click="trigger === 'click' && handleClickInside()"
      :class="['bal-popover-wrapper', popoverWrapperClasses]"
    >
      <BalCard shadow="lg" v-bind="$attrs" darkBgColor="800" class="">
        <slot :close="hidePopover" />
      </BalCard>
    </div>
  </div>
</template>

<style scoped>
.bal-popover-wrapper {
  @apply invisible opacity-0 absolute z-30 pt-3;
  transition: all 0.2s ease-in-out;
}

.bal-popover-wrapper-visible {
  @apply visible opacity-100;
}

.bal-popover-wrapper:hover {
  @apply visible opacity-100;
}
</style>
