<template>
  <!-- v-if="pagination.total > 0" -->
  <div
    :class="[
      'flex items-center h-full space-x-3 gap-1 overflow-hidden',
      customClass,
    ]"
  >
    <p
      class="flex items-center space-x-2 whitespace-nowrap text-sm text-very-light-gray leading-6"
    >
      <app-loading :loading="loading" />

      <span v-if="!loading">
        {{ pagination.firstItem }} - {{ pagination.lastItem }} of
        {{ pagination.total }}
      </span>
      <!-- {{ startItem }} - {{ endItem }} of {{ totalItems }} -->
    </p>

    <div class="flex items-center space-x-3">
      <span
        @click="handlePrevious"
        :class="[
          'flex items-center justify-center',
          {
            'cursor-pointer': pagination.currentPage > 1,
            'opacity-50 cursor-not-allowed': pagination.currentPage === 1,
          },
        ]"
        title="Previous page"
      >
        <!-- :disabled="currentPage === 1"
        class="flex items-center justify-center cursor-pointer disabled:opacity-50 disabled:cursor-not-allowed" -->
        <app-icon name="arrow-left" custom-class="h-5" />
      </span>

      <span
        @click="handleNext"
        :class="[
          'flex items-center justify-center',
          {
            'cursor-pointer': pagination.hasMorePages,
            'opacity-50 cursor-not-allowed': !pagination.hasMorePages,
          },
        ]"
      >
        <app-icon name="arrow-right" custom-class="h-5" />
      </span>
    </div>
  </div>
</template>

<script lang="ts">
  import { defineComponent, computed } from "vue"
  import AppIcon from "../AppIcon"
  import AppLoading from "../AppLoading"

  export default defineComponent({
    name: "AppPagination",
    components: {
      AppIcon,
      AppLoading,
    },
    props: {
      pagination: {
        type: Object as () => {
          currentPage: number
          firstItem: number
          hasMorePages: boolean
          lastItem: number
          lastPage: number
          perPage: number
          total: number
        },
        required: true,
        default: () => ({
          count: 0,
          currentPage: 1,
          firstItem: 0,
          hasMorePages: false,
          lastItem: 0,
          lastPage: 1,
          perPage: 10,
          total: 0,
        }),
      },
      customClass: {
        type: String,
        default: "",
      },
      loading: {
        type: Boolean,
        default: false,
      },
    },
    emits: ["update:page"],
    setup(props, { emit }) {
      const handlePrevious = () => {
        if (props.pagination.currentPage > 1) {
          emit("update:page", props.pagination.currentPage - 1)
        }
      }

      const handleNext = () => {
        if (props.pagination.hasMorePages) {
          emit("update:page", props.pagination.currentPage + 1)
        }
      }

      return {
        // totalPages,
        // startItem,
        // endItem,
        handlePrevious,
        handleNext,
      }
    },
  })
</script>

<style scoped>
  /* Optional: You can add more styles here if needed */
</style>
