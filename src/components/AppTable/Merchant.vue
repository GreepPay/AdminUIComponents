<template>
  <div
    id="merchant-table"
    :class="`${customClass} blend-in overflow-x-auto bg-white box-shadow`"
    style="
      background-size: cover;
      background-repeat: no-repeat;
      background-position: center;
    "
    :style="customStyle"
  >
    <table class="min-w-full divide-y divide-gray-200">
      <colgroup>
        <col class="w-1/2" />
        <col class="w-1/6" />
        <col class="w-1/6" />
      </colgroup>

      <thead class="bg-white">
        <tr>
          <th class="px-6 py-3 text-left font-medium text-gray-500">Name</th>
          <th class="px-6 py-3 text-left font-medium text-gray-500">Joined</th>
          <th class="px-6 py-3 text-right font-medium text-gray-500">
            Actions
          </th>
        </tr>
      </thead>

      <tbody class="bg-white divide-y divide-gray-200">
        <tr
          v-if="merchants && merchants.length"
          v-for="(merchant, index) in merchants"
          :key="merchant.auth_user_id"
          :class="
            index % 2 !== 0 ? 'bg-light-gray-one bg-opacity-[25%]' : 'bg-white'
          "
        >
          <td class="px-6 py-4 whitespace-nowrap">
            <div class="flex items-center space-x-3">
              <app-avatar
                :src="merchant?.user?.profile?.profile_picture || ''"
                :name="`${merchant?.user?.first_name} ${merchant?.user?.last_name}`"
                class="w-10 h-10 rounded-full"
                alt="Admin avatar"
              />
              <div class="flex items-center space-x-2">
                <div class="font-medium text-black">
                  {{
                    `${merchant?.user?.first_name} ${merchant?.user?.last_name}`
                  }}
                </div>
                <!-- <div
                  v-if="merchant.status === 'suspended'"
                  class="text-very-light-gray"
                >
                  (Suspended)
                </div> -->
              </div>
            </div>
          </td>

          <td class="px-6 py-4 whitespace-nowrap text-very-light-gray">
            {{ merchant.updated_at.split(" ")[0] }}
            • {{ merchant.updated_at.split(" ")[1] }}
          </td>

          <td class="px-6 py-4 whitespace-nowrap text-right text-sm">
            <span
              role="button"
              class="text-orange hover:opacity-80 cursor-pointer"
            >
              Suspend
            </span>
            <!-- <div class="flex justify-end space-x-3">
              <span
                role="button"
                v-else
                @click="$emit('restore', merchant.id)"
                class="text-green hover:opacity-80 cursor-pointer"
              >
                Restore
              </span>
              <span
                role="button"
                @click="$emit('delete', merchant.id)"
                class="text-red hover:opacity-80 cursor-pointer"
              >
                Delete
              </span>
            </div> -->
          </td>
        </tr>

        <tr v-else>
          <td
            colspan="3"
            class="px-6 py-4 text-black text-center font-semibold"
          >
            <app-empty-state
              title="No merchants"
              description="No merchants available "
            />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
  import { defineComponent, computed } from "vue"
  import type { PropType } from "vue"
  import AppAvatar from "../AppAvatar"
  import AppEmptyState from "../AppEmptyState"
  import { Profile } from "@greep/logic/src/gql/graphql"

  export default defineComponent({
    name: "AppMerchantTable",
    components: { AppAvatar, AppEmptyState },
    props: {
      merchants: {
        type: Array as PropType<Profile[]>,
        required: true,
      },
      customClass: {
        type: String,
        default: "",
      },
      customStyle: {
        type: String,
        default: "",
      },
    },
    emits: ["suspend", "restore", "delete"],
    setup() {
      return {}
    },
  })
</script>

<style scoped>
  .blend-in {
    animation: fadein 0.15s;
  }
  @keyframes fadein {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
</style>
