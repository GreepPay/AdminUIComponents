<template>
  <div class="relative px-4 h-fit">
    <span
      role="button"
      class="cursor-pointer bg-white select-none py-1.5 pr-2 pl-3 flex items-center space-x-2 sm:text-sm/6'"
      @click="toggleDropdown"
      aria-haspopup="listbox"
      aria-expanded="true"
      aria-labelledby="listbox-label"
    >
      <app-loading :loading="loading" />
      <span :class="!selectedOption && '!bg-red-500'">
        {{ selectedOption?.label || placeholder }}
      </span>
      <app-icon name="arrow-down" custom-class="h-4" />
    </span>

    <ul
      v-if="isOpen"
      class="absolute right-0 z-[11] -mt-1 max-h-56 w-full overflow-auto rounded-md bg-white py-1 text-base box-shadow ring-1 ring-black/5 sm:text-sm"
      tabindex="-1"
      role="listbox"
      aria-labelledby="listbox-label"
      aria-activedescendant="listbox-option-3"
    >
      <li
        v-for="(option, index) in options"
        :key="index"
        @click="selectOption(option)"
        class="px-4 py-2 cursor-pointer hover:bg-gray-100 text-sm"
        :class="{
          'font-medium text-black': selectedOption?.value === option.value,
        }"
      >
        {{ option.label }}
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
  import { defineComponent, ref, watch, PropType } from "vue"
  import AppIcon from "../AppIcon"
  import AppLoading from "../AppLoading"

  export default defineComponent({
    name: "CustomDropdown",
    components: { AppIcon, AppLoading },
    props: {
      options: {
        type: Array as PropType<Array<{ label: string; value: string }>>,
        required: true,
      },
      modelValue: {
        type: String,
        default: "",
      },
      placeholder: {
        type: String,
        default: "Select an option",
      },
      loading: {
        type: Boolean,
        default: false,
      },
    },
    emits: ["update:modelValue"],
    setup(props, { emit }) {
      const isOpen = ref(false)
      const selectedOption = ref(
        props.options.find((opt) => opt.value === props.modelValue) || null
      )

      watch(
        () => props.modelValue,
        (newVal) => {
          selectedOption.value =
            props.options.find((opt) => opt.value === newVal) || null
        }
      )

      const toggleDropdown = () => {
        isOpen.value = !isOpen.value
      }

      const selectOption = (option: { label: string; value: string }) => {
        selectedOption.value = option
        emit("update:modelValue", option.value)
        isOpen.value = false
      }

      return { isOpen, selectedOption, toggleDropdown, selectOption }
    },
  })
</script>

<style scoped>
  button:focus {
    outline: none;
    box-shadow: none;
  }
</style>
