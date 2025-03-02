<template>
  <div>
    <v-card outlined rounded="lg" class="mb-4">
      <v-toolbar flat color="light-blue lighten-5">
        <v-toolbar-title>Slowest groups</v-toolbar-title>
        <v-spacer />
        <v-btn :to="groupRoute" small class="primary">View in explorer</v-btn>
      </v-toolbar>

      <v-card-text>
        <GroupsTable
          :date-range="dateRange"
          :systems="systems"
          :uql="uql"
          :loading="explore.loading"
          :items="explore.pageItems"
          :columns="explore.columns"
          :group-columns="explore.groupColumns"
          :plot-columns="activeColumns"
          :order="explore.order"
          :axios-params="axiosParams"
        />
      </v-card-text>
    </v-card>

    <XPagination :pager="explore.pager" />
  </div>
</template>

<script lang="ts">
import { defineComponent, shallowRef, watch, computed, PropType } from '@vue/composition-api'

// Composables
import { UseSystems } from '@/use/systems'
import { useRouter } from '@/use/router'
import { UseDateRange } from '@/use/date-range'
import { buildGroupBy } from '@/use/uql'
import { useSpanExplore } from '@/use/span-explore'
import { useUql } from '@/use/uql'

// Components
import GroupsTable from '@/components/GroupsTable.vue'

// Utilities
import { xkey, xsys } from '@/models/otelattr'

export default defineComponent({
  name: 'SlowestGroups',
  components: { GroupsTable },

  props: {
    dateRange: {
      type: Object as PropType<UseDateRange>,
      required: true,
    },
    systems: {
      type: Object as PropType<UseSystems>,
      required: true,
    },
  },

  setup(props) {
    const { route } = useRouter()
    const system = xsys.all
    const query = buildGroupBy(xkey.spanGroupId) + ' | where not span.is_event'

    const activeColumns = shallowRef<string[]>([])

    const uql = useUql({
      query: buildGroupBy(xkey.spanGroupId),
      syncQuery: true,
    })

    const axiosParams = computed(() => {
      return {
        ...props.dateRange.axiosParams(),
        system: system,
        query,
      }
    })

    const explore = useSpanExplore(
      () => {
        const { projectId } = route.value.params
        return {
          url: `/api/tracing/${projectId}/groups`,
          params: axiosParams.value,
        }
      },
      {
        order: {
          column: `p50(${xkey.spanDuration})`,
          desc: true,
        },
      },
    )

    const groupRoute = computed(() => {
      return {
        name: 'GroupList',
        query: {
          ...explore.order.axiosParams, // ?
          ...props.dateRange.queryParams(),
          query,
        },
      }
    })

    watch(
      () => explore.plotColumns,
      (allColumns) => {
        if (allColumns.length && !activeColumns.value.length) {
          activeColumns.value = [allColumns[0].name]
        }
      },
    )

    return {
      xkey,
      uql,

      axiosParams,
      system,
      explore,
      activeColumns,
      groupRoute,
    }
  },
})
</script>

<style lang="scss"></style>
