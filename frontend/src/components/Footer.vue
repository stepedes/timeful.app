<template>
  <footer class="tw-bg-green tw-p-8 tw-text-white">
    <div class="tw-mx-auto tw-max-w-7xl">
      <div class="tw-flex tw-flex-col tw-gap-8 md:tw-grid md:tw-grid-cols-4">
        <!-- Logo and Info -->
        <div>
          <div class="tw-mb-3 tw-flex tw-items-center">
            <v-icon class="tw-mr-1 tw-text-white">mdi-calendar-blank</v-icon>
            <span class="tw-text-2xl tw-font-medium">timeful</span>
          </div>
          <div class="tw-mb-6 tw-leading-snug">
            Made with 💚 by
            <a
              href="https://x.com/jonathanzliu/"
              target="_blank"
              class="tw-text-white tw-underline"
              >@jonathanzliu</a
            >
          </div>
        </div>
      </div>
    </div>
  </footer>
</template>

<style scoped>
div:has(a) {
  @apply tw-leading-none;
}
a {
  @apply tw-text-white/80 !important;
}
a:hover {
  @apply tw-text-white !important;
}
</style>

<script>
import { mapActions } from "vuex"

export default {
  name: "Footer",
  data() {
    return {
      contractAddress: "2uHvhSasjzHm4PPAWaVpuU7yKtd4yUGYx6WK2oxutQnm",
    }
  },
  computed: {
    contractAddressTruncated() {
      return (
        this.contractAddress.slice(0, 6) +
        "..." +
        this.contractAddress.slice(-4)
      )
    },
  },
  methods: {
    ...mapActions(["showInfo"]),
    copyContractAddress() {
      navigator.clipboard
        .writeText(this.contractAddress)
        .then(() => {
          this.showInfo("Contract address copied to clipboard!")
        })
        .catch((err) => {
          console.error("Failed to copy contract address: ", err)
          // Optionally, show an error message to the user
        })
    },
  },
}
</script>
