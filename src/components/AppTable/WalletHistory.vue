<template>
  <div
    id="transaction-table"
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
        <col class="w-1/6" />
      </colgroup>

      <thead class="bg-white">
        <tr>
          <th class="px-6 py-3 text-left font-medium text-gray-500">To/From</th>
          <th class="px-6 py-3 text-left font-medium text-gray-500">Type</th>
          <th class="px-6 py-3 text-left font-medium text-gray-500">Time</th>
          <th class="px-6 py-3 text-right font-medium text-gray-500">Amount</th>
        </tr>
      </thead>

      <tbody class="bg-white divide-y divide-gray-200">
        <tr
          v-if="transactions && transactions.length"
          v-for="(transaction, index) in transactions"
          :key="transaction.user_id"
          :class="
            index % 2 !== 0 ? 'bg-light-gray-one bg-opacity-[25%]' : 'bg-white'
          "
        >
          <td class="px-6 py-4 whitespace-nowrap">
            <div class="flex items-center space-x-3">
              <app-icon :name="getIcon(transaction)" class="w-24" />

              <h3>
                {{ ` ${transaction.dr_or_cr === "credit" ? "From" : "To"}` }}
                <span class="font-medium text-black">
                  {{
                    `${transaction.user.first_name} ${transaction.user.last_name} `
                  }}
                </span>
              </h3>
            </div>
          </td>

          <td class="px-6 py-4 whitespace-nowrap text-very-light-gray">
            {{
              `${transaction.user.role.name} ${
                transaction.dr_or_cr === "credit" ? "Received" : "Sent"
              }`
            }}
          </td>

          <td class="px-6 py-4 whitespace-nowrap text-very-light-gray">
            {{ transaction.created_at.split(" ")[0] }}
            • {{ transaction.created_at.split(" ")[1] }}
          </td>

          <td
            class="px-6 py-4 whitespace-nowrap text-right text-sm font-semibold"
          >
            {{ `${transaction.amount} ${transaction.currency} ` }}
          </td>
        </tr>

        <tr v-else>
          <td
            colspan="3"
            class="px-6 py-4 text-black text-center font-semibold"
          >
            <app-empty-state
              title="No transactions"
              description="No transactions available "
            />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
  import { defineComponent } from "vue"
  import type { PropType } from "vue"
  import AppEmptyState from "../AppEmptyState"
  import AppIcon from "../AppIcon"
  import { Transaction } from "@greep/logic/src/gql/graphql"

  export default defineComponent({
    name: "AppWalletHistoryTable",
    components: { AppIcon, AppEmptyState },
    props: {
      transactions: {
        type: Array as PropType<Transaction[]>,
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
    emits: ["see-history", "freeze"],
    setup() {
      const getIcon = (transaction: Transaction) => {
        if (transaction.user.role.name === "Customer") {
          if (transaction.dr_or_cr === "credit") return "customer-received"
          else return "customer-sent"
        } else if (transaction.user.role.name === "Merchant") {
          if (transaction.dr_or_cr === "credit") return "merchant-received"
          else return "merchant-withdrawal"
        }

        return "customer-received"
      }
      return { getIcon }
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
