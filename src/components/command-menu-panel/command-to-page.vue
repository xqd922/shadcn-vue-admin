<script setup lang="ts">
import type { NavGroup, NavItem } from '../app-sidebar/types'
import {
  CommandGroup,
  CommandItem,
} from '@/components/ui/command'
import { useSidebar } from '@/composables/use-sidebar'
import CommandItemHasIcon from './command-item-has-icon.vue'

const emit = defineEmits<{
  (e: 'click'): void
}>()

const { navData, otherPages } = useSidebar()

function getFlatNavItems(navData: NavGroup[]): NavItem[] {
  const flatItems: NavItem[] = []
  navData.forEach((group) => {
    group.items.forEach((item) => {
      if (item.items) {
        flatItems.push(...getFlatNavItems([item as unknown as NavGroup]))
      }
      else {
        flatItems.push(item)
      }
    })
  })
  return flatItems
}

const commands = getFlatNavItems([...navData.value!, ...otherPages.value!])

const router = useRouter()
const route = useRoute()
function commandItemClick(url: string) {
  emit('click')
  if (route.fullPath !== url) {
    router.push(url)
  }
}
</script>

<template>
  <CommandGroup heading="Pages">
    <CommandItem
      v-for="command in commands"
      :key="command.title"
      :value="command.title"
      @click="commandItemClick(command.url!)"
    >
      <CommandItemHasIcon :name="command.title" :icon="command.icon" />
    </CommandItem>
  </CommandGroup>
</template>

<style scoped>
</style>
