<template>
  <div
    class="!w-full flex flex-col min-h-40 h-fit py-8 px-4 justify-center items-center"
  >
    <app-icon :name="icon" custom-class="!h-[54px]" />

    <div class="flex flex-col items-center justify-center px-5 pt-3">
      <app-normal-text class="!text-black !font-medium !text-base">
        {{ title }}
      </app-normal-text>
      <app-normal-text
        class="!text-very-light-gray !text-center pt-[4px] px-4 leading-5"
      >
        {{ description }}
      </app-normal-text>
    </div>
  </div>
</template>

<script lang="ts">
  import { defineComponent } from "vue"
  import AppIcon from "../AppIcon"
  import { AppNormalText } from "../AppTypography"
  /**
   * AppEmptyState Component
   *
   * Shows an empty state with an icon, title, and description
   */
  export default defineComponent({
    name: "AppEmptyState",
    components: {
      AppIcon,
      AppNormalText,
    },
    props: {
      icon: {
        type: String,
        default: "no-data",
      },
      title: {
        type: String,
        default: "",
      },
      description: {
        type: String,
        default: "",
      },
    },
    setup(props) {
      return {}
    },
  })
</script>
