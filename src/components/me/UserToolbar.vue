<template>
  <div class="navbar shadow-lg bg-base-300" :class="collapsed ? 'collapsed flex justify-center' : ''">
    <div class="flex-none">
      <Popper placement="top">
        <template v-slot:activator>
          <button class="btn btn-circle btn-ghost">
            <UserAvatar :user="user" :isCaptain="captainIsMe || leaderIsMe" />
          </button>
        </template>
        <UserStatusMenu :user="user" :collapsed="collapsed" @openSettings="openModal" />
      </Popper>
    </div>
    <div class="hide-on-collapsed flex-1 mx-2 flex flex-col items-start overflow-hidden">
      <h3 class="text-lg font-bold w-full truncate" :title="user.name">{{ user.name }}</h3>
      <span class="text-xs font-extralight text-gray-400 -mt-1.5">#{{ user.pin }}</span>
    </div>

    <div class="hide-on-collapsed">
      <Popper placement="top-start">
        <template v-slot:activator>
          <button class="btn btn-square btn-sm btn-ghost" title="I/O Toolbar">
            <Icon icon="mdi:chevron-up" width="24" />
          </button>
        </template>
        <IOToolbar :user="user" />
      </Popper>
      <button @click="openModal" class="btn btn-square btn-sm btn-ghost" title="My Settings">
        <Icon icon="mdi:cog" width="24" />
      </button>
    </div>
  </div>

  <UserSettings :isOpen="openSettings" :user="user" @close="closeModal" />
</template>

<script setup lang="ts">
import { computed, defineProps, PropType, ref, toRefs } from 'vue';
import { Icon } from '@iconify/vue';

import UserAvatar from '@/components/users/UserAvatar.vue';
import Popper from '@/components/DockUI/Popper.vue';
import UserStatusMenu from '@/components/me/UserStatusMenu.vue';
import IOToolbar from '@/components/me/UserIOToolbar.vue';
import UserSettings from '@/components/me/UserSettingsModal.vue';
import { IUser } from '@/data/interfaces';
import { useDeckStore } from '@/stores/deck';
import { useAuthStore } from '@/stores/auth';
import { useSpaceStore } from '@/stores/space';

const props = defineProps({
  user: {
    type: Object as PropType<IUser>,
    default: {},
  },
  collapsed: Boolean,
});

const store = {
  auth: useAuthStore(),
  deck: useDeckStore(),
  space: useSpaceStore(),
};

const captainIsMe = computed(() => store.auth.userId === store.deck.currentDeck?.captain_id);
const leaderIsMe = computed(() => store.auth.userId === store.space.currentSpace?.leader_id);

const openSettings = ref(false);
function openModal() {
  openSettings.value = true;
}
function closeModal() {
  openSettings.value = false;
}
</script>
