<template>
  <div class="relative" ref="dropdownRef">
    <button
      @click="toggleDropdown"
      :disabled="switching"
      class="inline-flex h-6 items-center gap-[3px] border-r border-gray-200/80 pr-2 pl-0 text-[11px] font-medium text-gray-600 transition-colors hover:text-gray-900 disabled:opacity-60 dark:border-dark-600 dark:text-dark-300 dark:hover:text-white"
      :title="currentLocale?.name"
      :aria-label="currentLocale?.name"
    >
      <span class="locale-switcher-icon" aria-hidden="true">
        <span class="locale-switcher-char locale-switcher-char--top" style="font-family: '48NH' !important;">文</span>
        <span class="locale-switcher-char locale-switcher-char--bottom" style="font-family: '48NH' !important;">A</span>
      </span>
      <Icon
        name="chevronDown"
        size="xs"
        class="text-[8px] text-gray-400 transition-transform duration-200"
        :class="{ 'rotate-180': isOpen }"
      />
    </button>

    <transition name="dropdown">
      <div
        v-if="isOpen"
        class="absolute left-0 top-full z-50 mt-2 w-[7.6rem] overflow-hidden rounded-[1rem] border border-gray-200/75 bg-[#fffdf9]/98 p-1.5 shadow-[0_8px_18px_rgba(15,23,42,0.08)] backdrop-blur-sm dark:border-dark-700 dark:bg-dark-800/96"
      >
        <button
          v-for="locale in availableLocales"
          :key="locale.code"
          :disabled="switching"
          @click="selectLocale(locale.code)"
          class="flex w-full items-center rounded-lg px-2.5 py-1.5 text-left text-[0.9rem] font-medium tracking-[-0.01em] text-gray-700 transition-colors hover:bg-gray-50/70 dark:text-gray-100 dark:hover:bg-dark-700/70"
          :class="{
            'text-gray-900 dark:text-white': locale.code === currentLocaleCode
          }"
        >
          <span>{{ locale.name }}</span>
        </button>
      </div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'
import { useI18n } from 'vue-i18n'
import Icon from '@/components/icons/Icon.vue'
import { setLocale, availableLocales } from '@/i18n'

const { locale } = useI18n()

const isOpen = ref(false)
const dropdownRef = ref<HTMLElement | null>(null)
const switching = ref(false)

const currentLocaleCode = computed(() => locale.value)
const currentLocale = computed(() => availableLocales.find((l) => l.code === locale.value))

function toggleDropdown() {
  isOpen.value = !isOpen.value
}

async function selectLocale(code: string) {
  if (switching.value || code === currentLocaleCode.value) {
    isOpen.value = false
    return
  }
  switching.value = true
  try {
    await setLocale(code)
    isOpen.value = false
  } finally {
    switching.value = false
  }
}

function handleClickOutside(event: MouseEvent) {
  if (dropdownRef.value && !dropdownRef.value.contains(event.target as Node)) {
    isOpen.value = false
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onBeforeUnmount(() => {
  document.removeEventListener('click', handleClickOutside)
})
</script>

<style scoped>
.locale-switcher-icon {
  position: relative;
  display: inline-flex;
  height: 1.05rem;
  width: 0.86rem;
  align-items: center;
  justify-content: center;
}

.locale-switcher-char {
  position: absolute;
  line-height: 1;
  font-weight: 500;
  color: currentColor;
}

.locale-switcher-char--top {
  top: -0.1rem;
  left: -0.03rem;
  font-size: 0.7rem;
}

.locale-switcher-char--bottom {
  right: -0.03rem;
  bottom: -0.12rem;
  font-size: 0.62rem;
}

.dropdown-enter-active,
.dropdown-leave-active {
  transition: all 0.15s ease;
}

.dropdown-enter-from,
.dropdown-leave-to {
  opacity: 0;
  transform: scale(0.95) translateY(-4px);
}
</style>
