<template>
  <v-card flat :max-height="maxHeight">
    <v-list dense>
      <template v-for="item in systems.tree">
        <v-menu
          v-if="item.children"
          :key="item.system"
          open-on-hover
          offset-x
          transition="slide-x-transition"
        >
          <template #activator="{ on, attrs }">
            <v-list-item :to="systemRoute(item.system)" exact v-bind="attrs" v-on="on">
              <v-list-item-content>
                <v-list-item-title>{{ item.system }}</v-list-item-title>
              </v-list-item-content>
              <v-list-item-icon class="align-self-center">
                <v-icon>mdi-menu-right</v-icon>
              </v-list-item-icon>
            </v-list-item>
          </template>

          <v-card flat :max-height="maxHeight">
            <v-list dense>
              <v-list-item
                v-for="item in item.children"
                :key="item.system"
                :to="systemRoute(item.system)"
                exact
                @click="$emit('click:item')"
              >
                <v-list-item-content>
                  <v-list-item-title>{{ item.system }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-card>
        </v-menu>

        <v-list-item v-else :key="item.system" :to="systemRoute(item.system)" exact>
          <v-list-item-content>
            <v-list-item-title>{{ item.system }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </template>
    </v-list>
  </v-card>
</template>

<script lang="ts">
import { defineComponent, PropType } from '@vue/composition-api'

// Composables
import { useRouter } from '@/use/router'
import { UseDateRange } from '@/use/date-range'
import { UseSystems } from '@/use/systems'

export default defineComponent({
  name: 'SystemList',

  props: {
    dateRange: {
      type: Object as PropType<UseDateRange>,
      required: true,
    },
    systems: {
      type: Object as PropType<UseSystems>,
      required: true,
    },
    maxHeight: {
      type: Number,
      default: 420,
    },
  },

  setup(props) {
    const { route } = useRouter()

    function systemRoute(system: string) {
      return {
        query: {
          ...props.dateRange.queryParams(),
          system,
          query: route.value.query.query,
        },
      }
    }

    return { systemRoute }
  },
})
</script>

<style lang="scss" scoped></style>
