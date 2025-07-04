<script setup lang="ts">
import { Icon } from '@iconify/vue'

// Props
interface Props {
  isExpanded?: boolean
  showMobileSidebar?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  isExpanded: true,
  showMobileSidebar: false,
})

// Emits
const emit = defineEmits<{
  'update:isExpanded': [value: boolean]
  'update:showMobileSidebar': [value: boolean]
}>()

// Composables
const route = useRoute()

// Mock user data (replace with actual user data)
const userName = ref('John Doe')

// Navigation items - only user-accessible pages
interface NavItem {
  name: string
  href: string
  icon: string
  children?: NavItem[]
}

const navigationItems: NavItem[] = [
  {
    name: 'Dashboard',
    href: '/',
    icon: 'heroicons:home',
  },
  {
    name: 'Calendar',
    href: '/calendar',
    icon: 'heroicons:calendar',
  },
  {
    name: 'Project Scheduler',
    href: '/scheduler',
    icon: 'mdi:calendar-clock',
  },
  {
    name: 'Sensor Control & Summary',
    href: '/sensors',
    icon: 'heroicons:cpu-chip',
  },
  {
    name: 'About',
    href: '/about',
    icon: 'heroicons:information-circle',
  },
  {
    name: 'Settings',
    href: '/settings',
    icon: 'heroicons:cog-6-tooth',
  },
]

// Methods
function isActiveRoute(href: string): boolean {
  return route.path === href
}

function toggleSidebar() {
  emit('update:isExpanded', !props.isExpanded)
}

function closeMobileSidebar() {
  emit('update:showMobileSidebar', false)
}
</script>

<template>
  <div>
    <!-- Desktop Sidebar -->
    <aside
      class="hidden lg:block h-full bg-tenang-50 dark:bg-gray-800 border-r border-gray-200 dark:border-gray-700 transition-all duration-300 ease-in-out" :class="[
        isExpanded ? 'w-64' : 'w-16',
      ]"
    >
      <!-- Toggle Button and Greeting -->
      <div class="flex items-center justify-between h-16 px-4 border-b border-gray-200 dark:border-gray-700">
        <div class="flex items-center w-full">
          <button
            class="p-2 rounded-lg text-gray-600 dark:text-gray-300 hover:bg-tenang-100 dark:hover:bg-tenang-light-100 transition-colors"
            @click="toggleSidebar"
          >
            <Icon
              :icon="isExpanded ? 'heroicons:chevron-left' : 'heroicons:chevron-right'"
              class="w-5 h-5"
            />
          </button>
          <transition name="slide-fade">
            <span
              v-if="isExpanded"
              class="ml-3 text-sm text-gray-700 dark:text-gray-300 whitespace-nowrap font-medium"
            >
              Hello, {{ userName }}
            </span>
          </transition>
        </div>
      </div>

      <!-- Navigation Menu -->
      <nav class="mt-4 px-2">
        <ul class="space-y-1">
          <li v-for="item in navigationItems" :key="item.name">
            <!-- Menu Item -->
            <NuxtLink
              :to="item.href"
              class="flex items-center px-3 py-2 rounded-lg text-sm font-medium transition-colors" :class="[
                isActiveRoute(item.href)
                  ? 'bg-tenang-primary dark:bg-tenang-primary-dark text-white dark:text-black shadow-sm'
                  : 'text-gray-700 dark:text-gray-300 hover:bg-tenang-primary/10 dark:hover:bg-tenang-primary-dark/10 hover:text-tenang-primary dark:hover:text-tenang-primary-dark',
              ]"
            >
              <Icon :icon="item.icon" class="w-5 h-5 flex-shrink-0" />
              <span v-if="isExpanded" class="ml-3">{{ item.name }}</span>
            </NuxtLink>

            <!-- Submenu -->
            <ul v-if="'children' in item && item.children && isExpanded" class="mt-1 ml-4 space-y-1">
              <li v-for="child in item.children" :key="child.name">
                <NuxtLink
                  :to="child.href"
                  class="flex items-center px-3 py-2 rounded-lg text-sm transition-colors" :class="[
                    isActiveRoute(child.href)
                      ? 'bg-tenang-primary/20 dark:bg-tenang-primary-dark/20 text-tenang-primary dark:text-tenang-primary-dark font-medium'
                      : 'text-gray-600 dark:text-gray-400 hover:bg-tenang-primary/10 dark:hover:bg-tenang-primary-dark/10 hover:text-tenang-primary dark:hover:text-tenang-primary-dark',
                  ]"
                >
                  <Icon :icon="child.icon" class="w-4 h-4 flex-shrink-0" />
                  <span class="ml-3">{{ child.name }}</span>
                </NuxtLink>
              </li>
            </ul>
          </li>
        </ul>
      </nav>

      <!-- Version Info -->
      <div class="absolute bottom-4 left-4 right-4">
        <div
          class="text-xs text-gray-500 dark:text-gray-400 text-center font-medium" :class="[
            isExpanded ? 'block' : 'hidden',
          ]"
        >
          v1.0.0
        </div>
      </div>
    </aside>

    <!-- Mobile Sidebar Overlay -->
    <div
      v-if="showMobileSidebar"
      class="fixed inset-0 bg-black bg-opacity-50 z-40 lg:hidden"
      @click="closeMobileSidebar"
    />

    <!-- Mobile Sidebar -->
    <aside
      class="fixed top-0 left-0 h-full bg-tenang-50 dark:bg-gray-800 border-r border-gray-200 dark:border-gray-700 z-50 transition-transform duration-300 ease-in-out lg:hidden w-64" :class="[
        showMobileSidebar ? 'translate-x-0' : '-translate-x-full',
      ]"
    >
      <!-- Mobile Header -->
      <div class="flex items-center justify-between h-16 px-4 border-b border-gray-200 dark:border-gray-700">
        <div class="flex items-center">
          <span class="text-lg font-semibold text-gray-900 dark:text-white text-tenang-primary dark:text-tenang-primary-dark">
            Hello, {{ userName }}
          </span>
        </div>
        <button
          class="p-1 rounded-lg text-gray-500 hover:bg-tenang-100 dark:hover:bg-tenang-light-100 hover:text-tenang-600 dark:hover:text-tenang-primary-dark transition-colors"
          @click="closeMobileSidebar"
        >
          <Icon icon="heroicons:x-mark" class="w-6 h-6" />
        </button>
      </div>

      <!-- Mobile Navigation Menu -->
      <nav class="mt-4 px-2">
        <ul class="space-y-1">
          <li v-for="item in navigationItems" :key="item.name">
            <NuxtLink
              :to="item.href"
              class="flex items-center px-3 py-2 rounded-lg text-sm font-medium transition-colors" :class="[
                isActiveRoute(item.href)
                  ? 'bg-tenang-primary dark:bg-tenang-primary-dark text-white dark:text-black shadow-sm'
                  : 'text-gray-700 dark:text-gray-300 hover:bg-tenang-primary/10 dark:hover:bg-tenang-primary-dark/10 hover:text-tenang-primary dark:hover:text-tenang-primary-dark',
              ]"
              @click="closeMobileSidebar"
            >
              <Icon :icon="item.icon" class="w-5 h-5 flex-shrink-0" />
              <span class="ml-3">{{ item.name }}</span>
            </NuxtLink>

            <!-- Mobile Submenu -->
            <ul v-if="'children' in item && item.children" class="mt-1 ml-4 space-y-1">
              <li v-for="child in item.children" :key="child.name">
                <NuxtLink
                  :to="child.href"
                  class="flex items-center px-3 py-2 rounded-lg text-sm transition-colors" :class="[
                    isActiveRoute(child.href)
                      ? 'bg-tenang-primary/20 dark:bg-tenang-primary-dark/20 text-tenang-primary dark:text-tenang-primary-dark font-medium'
                      : 'text-gray-600 dark:text-gray-400 hover:bg-tenang-primary/10 dark:hover:bg-tenang-primary-dark/10 hover:text-tenang-primary dark:hover:text-tenang-primary-dark',
                  ]"
                  @click="closeMobileSidebar"
                >
                  <Icon :icon="child.icon" class="w-4 h-4 flex-shrink-0" />
                  <span class="ml-3">{{ child.name }}</span>
                </NuxtLink>
              </li>
            </ul>
          </li>
        </ul>
      </nav>
    </aside>
  </div>
</template>

<style scoped>
.slide-fade-enter-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}
.slide-fade-leave-active {
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
}
.slide-fade-enter-from {
  opacity: 0;
  transform: translateX(-24px);
}
.slide-fade-enter-to {
  opacity: 1;
  transform: translateX(0);
}
.slide-fade-leave-from {
  opacity: 1;
  transform: translateX(0);
}
.slide-fade-leave-to {
  opacity: 0;
  transform: translateX(-24px);
}
</style>
