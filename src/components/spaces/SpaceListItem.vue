<template>
  <li>
    <div
      :title="space.name"
      class="space relative pr-3 py-2 border-l-4 hover:border-primary hover:bg-gradient-to-r from-gray-500"
      :class="isActive(space.id) ? 'active' : 'border-transparent'"
    >
      <div
        class="ribbon-tail flex items-center justify-between mx-2 overflow-hidden"
        :class="isActive(space.id) && 'before:pr-5 before:bg-base-100 before:bg-opacity-50'"
      >
        <div class="flex items-center pl-1 gap-2 overflow-hidden">
          <button
            v-if="type !== SpaceType.TEXT"
            @click="fetchSpaceTeam(space.id)"
            :title="state.expanded ? 'Close' : 'Expand'"
            class="btn btn-xs btn-ghost btn-square"
            :class="state.loading && 'loading'"
          >
            <Icon
              v-if="!state.loading"
              icon="ion:caret-forward-sharp"
              width="14"
              :class="state.expanded && 'transform rotate-90'"
            />
          </button>

          <div v-else class="pl-2" />
          <button v-if="isTeam" class="btn btn-sm btn-circle btn-outline border-2">
            <Icon icon="mdi:account-group-outline" width="28" />
          </button>
          <router-link :to="spaceUrl" class="truncate">{{ space.name }}</router-link>
        </div>

        <div class="actions">
          <button @click="$emit('edit', space)" class="btn btn-xs btn-square btn-ghost" title="Edit Space">
            <Icon icon="mdi:dots-horizontal" width="20" />
          </button>
        </div>
      </div>

      <div v-if="isNew" class="notification absolute -left-1 top-0 flex h-full w-1">
        <span class="animate-ping absolute inline-flex h-full w-full bg-accent opacity-75"></span>
        <span class="relative inline-flex rounded-full h-full w-1 bg-accent"></span>
      </div>
    </div>

    <div v-if="state.expanded" class="space-team">
      <ul class="pt-1 pl-10 pr-6 flex flex-col gap-1">
        <template v-for="user in team" :key="user.id">
          <li class="flex items-center gap-2">
            <UserAvatar :user="user" :size="7" textCss="text-sm" hideIndicators />
            <span class="text-xs truncate">{{ user.name }}</span>
          </li>
        </template>
      </ul>
    </div>
  </li>
</template>

<script setup lang="ts">
import { computed, defineProps, PropType, reactive, ref, toRefs } from 'vue';
import { Icon } from '@iconify/vue';
import { useRoute } from 'vue-router';

import UserAvatar from '@/components/users/UserAvatar.vue';
import { ISpace } from '@/data/interfaces';
import { SpaceType } from '@/data/mock';

const route = useRoute();

const props = defineProps({
  type: {
    type: String as PropType<SpaceType>,
    default: SpaceType.TEXT,
  },
  isTeam: Boolean,
  space: {
    type: Object as PropType<ISpace>,
    default: {},
  },
  isNew: {
    type: Boolean,
    default: false,
  },
});

const { isTeam, space } = toRefs(props);

const spaceUrl = computed(() =>
  isTeam.value
    ? `/team/${space.value.type}/${space.value.id}`
    : `/d/${space.value.deck_id}/${space.value.type}/${space.value.id}`,
);

const team = ref();
const state = reactive({
  //team: undefined,
  loading: false,
  expanded: false,
  error: '',
});

// TODO: move this to utils
const pause = (ms: number) => new Promise((resolve) => setTimeout(resolve, ms));

async function fetchSpaceTeam(spaceId?: string) {
  if (state.expanded) return (state.expanded = false);

  state.loading = true;
  await pause(1500);
  //console.log('spaceId', spaceId);
  return fetch('https://jsonplaceholder.typicode.com/users?_limit=3')
    .then((res) => res.json())
    .then((json) => {
      state.loading = false;
      state.expanded = !state.expanded;
      return (team.value = [...json]);
    })
    .catch((e) => console.error(e)); // state.error = e
}

const isActive = (spaceId: string) => {
  return spaceId === route.params.spaceId;
};
</script>

<style scoped>
.space.active {
  @apply font-medium border-primary bg-gradient-to-r;
}

.space .actions {
  visibility: hidden;
}
.space.active .actions {
  visibility: visible;
}
.space:hover .actions {
  visibility: visible;
}
</style>
