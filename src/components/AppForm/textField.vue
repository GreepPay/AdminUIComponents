<template>
  <div class="flex w-full flex-col relative space-y-[2px]">
    <div
      v-if="useFloatingLabel && content.length > 0"
      class="h-[10px] w-full"
    ></div>
    <div
      class="w-full flex flex-row items-center"
      :tabindex="tabIndex"
      @focus="isFocused = true"
      @blur="isFocused = false"
    >
      <!--
        @slot outer-prefix
        Use this slot to add content before the input field container.
      -->
      <slot name="outer-prefix" />
      <label
        v-if="label"
        class="absolute left-4 px-1 text-base top-[19px] bg-white transition-all duration-300"
        :class="{
          '!top-[-14px] text-very-light-gray font-medium':
            isFocused || placeholder || content,
          'text-red': errorMessage,
          'text-green': successMessage && !errorMessage,
        }"
      >
        {{ label }}
      </label>

      <div
        :class="`flew-grow w-full space-x-1 flex-row flex items-center justify-between px-4 py-4 bg-white rounded-[10px] border-[1.5px] border-[#E0E2E4] relative

          ${errorMessage ? '!border-red' : ''}

          ${customClass}`"
        @click.stop="
          action
            ? action()
            : fieldType == 'date'
            ? (ShowCalendarModal = true)
            : ''
        "
      >
        <!--
          @slot inner-prefix
          Use this slot to add content before the input field.
        -->
        <slot name="inner-prefix" />
        <!-- Floating label -->
        <template v-if="useFloatingLabel && content.length > 0">
          <app-normal-text
            class="absolute left-4 top-[-24%] px-1 py-[2px] bg-white !text-very-light-gray z-10"
          >
            {{ placeholder }}
          </app-normal-text>
        </template>
        <input
          v-model="content"
          :placeholder="placeholder"
          @focus="isFocused = true"
          @blur=";(isFocused = false), checkValidation()"
          @keypress="isNumber"
          :disabled="fieldType == 'date' ? true : disabled"
          :type="fieldType == 'date' ? 'text' : fieldType"
          :class="` text-black grow bg-transparent placeholder-gray-400 focus input w-full focus:outline-none ${inputStyle} `"
          @click.stop="
            action
              ? action()
              : fieldType == 'date'
              ? (ShowCalendarModal = true)
              : ''
          "
        />
        <!--
          @slot inner-suffix
          Use this slot to add content after the input field.
        -->
        <slot name="inner-suffix" />
        <app-icon
          :name="`${fieldType == 'password' ? 'eye' : 'eye-slash'}`"
          :customClass="`${fieldType == 'password' ? 'h-[12px]' : 'h-[14px]'}`"
          v-if="type == 'password'"
          @click.stop="
            fieldType == 'password'
              ? (fieldType = 'text')
              : (fieldType = 'password')
          "
        />
      </div>
      <!--
        @slot outer-suffix
        Use this slot to add content after the input field container.
      -->
      <slot name="outer-suffix" />
    </div>
    <div
      v-if="showValidationMessage && (errorMessage || successMessage)"
      class="w-full flex flex-row pt-1 justify-start items-center gap-1"
    >
      <!-- <img
        v-if="errorMessage"
        src="@/assets/svg/All/linear/info-circle.svg"
        class="w-4 h-4"
      />
      <img
        v-if="successMessage && !errorMessage"
        src="@/assets/svg/All/linear/tick-circle.svg"
        class="w-4 h-4"
      /> -->
      <app-normal-text
        :customClass="'text-left'"
        :color="errorMessage ? 'text-red' : 'text-green'"
      >
        {{ errorMessage || successMessage }}
      </app-normal-text>
    </div>

    <Teleport to="body">
      <div
        v-if="ShowCalendarModal"
        class="fixed top-0 left-0 z-[99999999999999999] bg-black !bg-opacity-30 dark:!bg-opacity-50 flex w-full h-full flex-row items-end justify-end mdlg:!items-center mdlg:!justify-center md:!justify-center md:!items-center"
        @click="ShowCalendarModal = false"
      >
        <div
          class="w-full mdlg:!w-[60%] md:!w-[80%] grid grid-cols-12 h-full relative"
          id="modalContent"
        >
          <!-- Left side -->
          <div
            class="hidden col-span-3 md:!col-span-2 mdlg:!col-span-3 mdlg:!flex md:!flex flex-col sticky top-0"
          ></div>

          <!-- Main section -->
          <div
            class="col-span-12 mdlg:!col-span-6 md:!col-span-8 relative h-full flex flex-col items-end justify-end mdlg:!items-center mdlg:!justify-center md:!justify-center md:!items-center"
          >
            <div
              @click.stop="true"
              class="rounded-t-2xl mdlg:!rounded-[10px] md:!rounded-[10px] flex flex-col space-y-2 !bg-white dark:border-[1px] dark:border-gray-100 w-full absolute mdlg:!relative md:!relative overflow-y-auto h-auto max-h-auto bottom-0 left-0 pb-3 px-3 mdlg:!pb-4 md:!pb-4 lg:!text-sm mdlg:!text-[12px] text-xs"
            >
              <div
                class="flex items-center justify-center sticky top-0 !bg-white w-full pt-3"
              >
                <span
                  class="bg-gray-500 dark:bg-gray-200 rounded-full w-[30px] h-[4px]"
                ></span>
              </div>

              <div
                class="flex items-center justify-center sticky top-0 flex-col bg-white w-full"
              >
                <app-normal-text
                  custom-class="!text-xs font-normal w-full text-left py-2"
                >
                  {{ placeholder }}
                </app-normal-text>
              </div>
              <app-calendar
                v-model="content"
                :closeModal="
                  () => {
                    ShowCalendarModal = false
                  }
                "
                :defaultDate="content"
                :preventBackDate="preventBackDate"
                :miminumDate="miminumDate"
              />

              <div class="pt-2 sticky bottom-0 bg-white w-full"></div>
            </div>
          </div>

          <!-- Right side -->
          <div
            class="hidden col-span-3 md:!col-span-2 mdlg:!col-span-3 mdlg:!flex md:!flex flex-col sticky top-0"
          ></div>
        </div>
      </div>
    </Teleport>
  </div>
</template>
<script lang="ts">
  import AppNormalText from "../AppTypography/normalText.vue"
  import { defineComponent, onMounted, ref, toRef, watch } from "vue"
  import { Logic } from "../../composable"
  import { FormRule } from "../../types"
  import AppIcon from "../AppIcon"
  import AppCalendar from "./calendar.vue"

  /**
   * AppTextField Component
   *
   * A reusable text field component with validation and customizable slots.
   */
  export default defineComponent({
    components: {
      AppNormalText,
      AppIcon,
      AppCalendar,
    },
    props: {
      /**
       * Placeholder text for the input field.
       */
      placeholder: {
        type: String,
        default: "",
        required: false,
      },
      /**
       * label text for the input field.
       */
      label: {
        type: String,
        default: "",
        required: false,
      },
      /**
       * Custom CSS classes to apply to the input field container.
       */
      customClass: {
        type: String,
        default: "",
        required: false,
      },
      /**
       *  An array of validation rules to apply to the input field.
       */
      rules: {
        type: Object as () => FormRule[],
        required: false,
      },
      /**
       * The v-model value of the input field.
       */
      modelValue: {
        default: "",
        required: false,
      },
      /**
       *  The type of the input field (e.g., "text", "password", "email").
       */
      type: {
        type: String,
        default: "text",
        required: false,
      },
      /**
       * The name of the input field (used for validation messages).
       */
      name: {
        type: String,
        default: "",
        required: false,
      },
      /**
       *  Determines whether the input field is disabled.
       */
      disabled: {
        type: Boolean,
        default: false,
        required: false,
      },
      /**
       *  A value to update the input field with (used for external updates).
       */
      updateValue: {
        type: String,
        default: "",
        required: false,
      },
      /**
       * Determines whether the input is formatted as money.
       */
      isFormatted: {
        type: Boolean,
        default: false,
        required: false,
      },
      /**
       * Error message to display below the input field.
       */
      errorMessage: {
        type: String,
        default: "",
        required: false,
      },
      /**
       * Success message to display below the input field.
       */
      successMessage: {
        type: String,
        default: "",
        required: false,
      },
      /**
       * Determines whether the input is floating label.
       */
      useFloatingLabel: {
        type: Boolean,
        default: false,
        required: false,
      },

      /**
       * Determines whether the input is floating label.
       */
      showValidationMessage: {
        type: Boolean,
        default: true,
      },
      /**
       * Determines whether to prevent back date
       */
      preventBackDate: {
        type: Boolean,
        default: false,
      },
      /**
       * Determines the minimum date
       */
      miminumDate: {
        type: String,
        default: "",
      },

      /**
       * An action to perform on click
       */
      action: {
        type: Function,
        required: false,
      },

      /**
       * Input style
       */
      inputStyle: {
        type: String,
        default: "",
      },
    },
    name: "AppTextField",
    emits: ["update:modelValue"],
    setup(props: any, context: any) {
      const content = ref("")

      const fieldType = ref("text")

      const updateValueRef = toRef(props, "updateValue")

      const ShowCalendarModal = ref(false)

      watch(content, () => {
        context.emit("update:modelValue", content.value)
        setTimeout(() => {
          checkValidation()
        }, 500)
      })

      onMounted(() => {
        if (props.updateValue) {
          content.value = props.updateValue
        }

        if (props.modelValue) {
          content.value = props.modelValue
        }
        if (props.type) {
          fieldType.value = props.type
        }
        if (props.isFormatted) {
          content.value = Logic.Common.convertToMoney(
            content.value ? content.value.toString().replace(/,/g, "") : 0,
            false,
            "",
            false
          )
        }
      })
      const validationStatus = ref(true)
      const errorMessage = ref("")

      const isRequired = () => {
        if (content.value) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value
              ? `${props.name} is required`
              : errorMessage.value,
          }
        }
      }

      const isGreaterThan = (count: number) => {
        if (content.value.length > count) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value
              ? `${props.name} must be more than ${count} characters`
              : errorMessage.value,
          }
        }
      }

      const isLessThan = (count: number) => {
        if (content.value.length < count) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value
              ? `${props.name} must be less than ${count} characters`
              : errorMessage.value,
          }
        }
      }

      const isEqualsTo = (count: number) => {
        if (content.value.length == count) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value
              ? `${props.name} must be ${count} characters`
              : errorMessage.value,
          }
        }
      }

      const isCondition = (condition: any, errMsg: any) => {
        if (condition) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value ? errMsg : errorMessage.value,
          }
        }
      }

      const isGreaterThanOrEqualsTo = (count: number) => {
        if (content.value.length >= count) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value
              ? `${props.name} must be more than ${count - 1} characters`
              : errorMessage.value,
          }
        }
      }

      const isLessThanOrEqualsTo = (count: number) => {
        if (content.value.length <= count) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value
              ? `${props.name} must be less than ${count + 1} characters`
              : errorMessage.value,
          }
        }
      }

      const isRegex = (regex: any, errMsg: any) => {
        if (content.value.match(regex)) {
          errorMessage.value = ""
          return {
            status: true,
            message: "",
          }
        } else {
          return {
            status: false,
            message: !errorMessage.value ? errMsg : errorMessage.value,
          }
        }
      }

      const contentCharacterStop = ref(0)

      const applyContentRule = (evt: any) => {
        if (props.contentRule) {
          evt = evt ? evt : window.event
          var charCode = evt.which ? evt.which : evt.keyCode
          if (charCode != 46 && charCode != 8) {
            if (content.value.length >= props.contentRule.max) {
              evt.preventDefault()
              return
            }

            if (
              content.value.length != 0 &&
              content.value.length % props.contentRule.addAfterCount ==
                contentCharacterStop.value
            ) {
              content.value += props.contentRule.characterToAdd
              if (
                contentCharacterStop.value >=
                props.contentRule.addAfterCount - 1
              ) {
                contentCharacterStop.value = 0
              } else {
                contentCharacterStop.value++
              }
            }
          }
        }

        return true
      }

      const handlePasteAction = (event: any) => {
        if (props.contentRule) {
          event.preventDefault()
          // Getting copy text
          const clipboardData =
            event.clipboardData || event.originalEvent.clipboardData
          const pastedData = clipboardData.getData("Text")
          const arrayOfNumbers = pastedData.split("")

          // set the length to the max
          if (arrayOfNumbers.length > props.contentRule.max)
            arrayOfNumbers.slice(0, props.contentRule.max)

          if (props.contentRule.addAfterCount > 0) {
            // group content array
            const contentGroup: any = []

            const chunkSize = props.contentRule.addAfterCount
            for (let i = 0; i < arrayOfNumbers.length; i += chunkSize) {
              const chunk = arrayOfNumbers.slice(i, i + chunkSize)
              contentGroup.push(chunk)
            }

            const contentString = contentGroup.map((eachGroup: any) => {
              return eachGroup.join("")
            })

            content.value = contentString.join(props.contentRule.characterToAdd)
          } else {
            content.value = pastedData
          }
        }
      }

      const checkValidation = () => {
        if (props.rules) {
          const allValidationStates: {
            status: boolean
            message: string
          }[] = []
          for (let index = 0; index < props.rules.length; index++) {
            const rule = props.rules[index]
            if (rule.type == "isRequired") {
              const status = isRequired()
              allValidationStates.push(status)
            }

            if (rule.type == "isGreaterThan") {
              const status = isGreaterThan(rule.value)
              allValidationStates.push(status)
            }

            if (rule.type == "isLessThan") {
              const status = isLessThan(rule.value)
              allValidationStates.push(status)
            }

            if (rule.type == "isEqualsTo") {
              const status = isEqualsTo(rule.value)
              allValidationStates.push(status)
            }

            if (rule.type == "isGreaterThanOrEqualsTo") {
              const status = isGreaterThanOrEqualsTo(rule.value)
              allValidationStates.push(status)
            }

            if (rule.type == "isLessThanOrEqualsTo") {
              const status = isLessThanOrEqualsTo(rule.value)
              allValidationStates.push(status)
            }

            if (rule.type == "isRegex") {
              const status = isRegex(rule.value, rule.errorMessage)
              allValidationStates.push(status)
            }

            if (rule.type == "isCondition") {
              const status = isCondition(rule.value, rule.errorMessage)
              allValidationStates.push(status)
            }
          }

          validationStatus.value = allValidationStates.every(
            (status) => status.status === true
          )
          errorMessage.value = allValidationStates.filter(
            (status) => status.status === false
          )[0]?.message
        }
      }

      watch(content, () => {
        checkValidation()
        if (props.isFormatted) {
          content.value = Logic.Common.convertToMoney(
            content.value ? content.value.toString().replace(/,/g, "") : 0,
            false,
            "",
            false
          )
        }
      })

      watch(props, () => {
        if (props.updateValue && props.watchUpdates) {
          content.value = props.updateValue
        }
      })

      watch(updateValueRef, () => {
        if (props.watchUpdates) {
          content.value = props.updateValue
        }
      })

      const isNumber = (evt: any) => {
        if (props.type != "tel" && props.type != "number") return true

        evt = evt ? evt : window.event
        var charCode = evt.which ? evt.which : evt.keyCode
        if (
          charCode > 31 &&
          (charCode < 48 || charCode > 57) &&
          charCode !== 46
        ) {
          evt.preventDefault()
        } else {
          return true
        }
      }

      const showError = (message: string) => {
        validationStatus.value = false
        errorMessage.value = message
      }

      const isFocused = ref(false)

      const tabIndex = Math.random()

      return {
        content,
        errorMessage,
        validationStatus,
        console,
        isFocused,
        tabIndex,
        fieldType,
        ShowCalendarModal,
        Logic,
        applyContentRule,
        handlePasteAction,
        checkValidation,
        isNumber,
        showError,
      }
    },
  })
</script>
<style scoped>
  input:disabled::placeholder,
  textarea:disabled::placeholder {
    -webkit-text-fill-color: var(--placeholder-color);
    -webkit-opacity: 1;
    opacity: 1;
    color: var(--placeholder-color);
  }

  input:disabled,
  textarea:disabled {
    -webkit-text-fill-color: var(--text-color);
    -webkit-opacity: 1;
    opacity: 1;
    color: var(--text-color);
  }
</style>
