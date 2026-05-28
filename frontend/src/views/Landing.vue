<template>
  <div class="tw-bg-light-gray">
    <div
      class="tw-relative tw-m-auto tw-mb-12 tw-flex tw-max-w-6xl tw-flex-col tw-px-4 sm:tw-mb-20"
    >
      <!-- Header -->
      <div class="tw-mb-16 sm:tw-mb-28">
        <div class="tw-flex tw-items-center tw-pt-5">
          <Logo type="timeful" />

          <v-spacer />

          <LandingPageHeader>
            <div v-if="authUser" class="tw-ml-2">
              <AuthUserMenu />
            </div>
            <v-btn v-else text :to="{ name: 'sign-in' }">Sign in</v-btn>
          </LandingPageHeader>
        </div>
      </div>

      <div class="tw-flex tw-flex-col tw-items-center">
        <div
          class="tw-mb-6 tw-flex tw-max-w-[26rem] tw-flex-col tw-items-center sm:tw-w-[35rem] sm:tw-max-w-none"
        >
          <div
            id="header"
            class="tw-mb-4 tw-text-center tw-text-2xl tw-font-medium sm:tw-text-4xl lg:tw-text-4xl xl:tw-text-5xl"
          >
            <h1>Find a time to meet</h1>
          </div>

          <div
            class="lg:tw-text-md tw-text-left tw-text-center tw-text-sm tw-text-very-dark-gray sm:tw-text-lg md:tw-text-lg xl:tw-text-lg"
          >
            Coordinate group meetings without the back and forth.
          </div>
        </div>

        <div class="tw-mb-12 tw-space-y-2">
          <v-btn
            class="tw-block tw-self-center tw-rounded-lg tw-bg-green tw-px-10 tw-text-base sm:tw-px-10 lg:tw-px-12"
            dark
            @click="authUser ? openDashboard() : (newDialog = true)"
            large
            :x-large="$vuetify.breakpoint.mdAndUp"
          >
            {{ authUser ? "Open dashboard" : "Create event" }}
          </v-btn>
          <div
            v-if="!authUser"
            class="tw-text-center tw-text-xs tw-text-dark-gray sm:tw-text-sm"
          >
            It's free! No login required.
          </div>
        </div>
        <div class="tw-relative tw-w-full">
          <!-- Green background -->
          <div
            class="tw-absolute -tw-bottom-12 tw-left-1/2 tw-h-[85%] tw-w-screen -tw-translate-x-1/2 tw-bg-green sm:-tw-bottom-20"
          ></div>

          <!-- Hero section -->
          <div
            class="tw-relative tw-z-20 tw-w-full tw-rounded-lg tw-border tw-border-light-gray-stroke tw-bg-white tw-shadow-xl sm:tw-rounded-xl md:tw-mx-auto md:tw-w-fit"
          >
            <div
              class="tw-relative tw-mx-4 tw-aspect-square md:tw-size-[700px] lg:tw-size-[800px]"
            >
              <v-img
                class="tw-absolute tw-left-0 tw-top-0 tw-z-20 tw-size-full tw-transition-opacity tw-duration-300"
                src="@/assets/img/hero.jpg"
                transition="fade-transition"
                contain
              />
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- How it works -->
    <div
      id="how-it-works"
      class="tw-grid tw-place-content-center tw-px-4 tw-pt-12"
    >
      <div class="tw-mx-auto tw-flex tw-flex-col tw-gap-4">
        <div
          class="tw-mb-4 tw-text-center tw-text-2xl tw-font-medium sm:tw-text-3xl lg:tw-text-4xl"
        >
          How it works
        </div>
        <div
          v-for="(step, i) in howItWorksSteps"
          :key="i"
          class="tw-flex tw-items-center tw-gap-2"
        >
          <NumberBullet>{{ i + 1 }}</NumberBullet>
          <div class="tw-text-base tw-font-medium md:tw-text-xl">
            <div v-html="step"></div>
          </div>
        </div>
      </div>
      <div
        class="tw-mb-6 tw-mt-10 tw-text-center tw-text-3xl tw-font-medium md:tw-mb-12 md:tw-mt-20 md:tw-text-6xl"
      >
        It's that simple.
      </div>
      <v-img
        alt="schej character"
        src="@/assets/schej_character.png"
        :height="isPhone ? 200 : 300"
        transition="fade-transition"
        contain
        class="-tw-mb-12"
      />
    </div>

    <Footer />

    <!-- Sign in dialog -->
    <SignInDialog
      v-model="signInDialog"
      @signIn="_signIn"
      @emailSignIn="_emailSignIn"
    />

    <!-- New event dialog -->
    <NewDialog v-model="newDialog" no-tabs @signIn="signIn" />

    <!-- Add the dialog component -->
    <HowItWorksDialog
      v-if="showHowItWorksDialog"
      v-model="showHowItWorksDialog"
    />
  </div>
</template>

<style scoped>
@media screen and (min-width: 375px) and (max-width: 640px) {
  #header {
    font-size: 1.875rem !important; /* 30px */
    line-height: 2.25rem !important; /* 36px */
  }
}
</style>
<style>
.rdt-h {
  @apply tw-rounded tw-bg-light-green/20 tw-px-px tw-text-black;
}
</style>

<script>
import LandingPageCalendar from "@/components/landing/LandingPageCalendar.vue"
import { isPhone, signInGoogle } from "@/utils"
import FAQ from "@/components/FAQ.vue"
import Header from "@/components/Header.vue"
import NumberBullet from "@/components/NumberBullet.vue"
import NewEvent from "@/components/NewEvent.vue"
import NewDialog from "@/components/NewDialog.vue"
import LandingPageHeader from "@/components/landing/LandingPageHeader.vue"
import Logo from "@/components/Logo.vue"
import SignInDialog from "@/components/SignInDialog.vue"
import { calendarTypes } from "@/constants"
import Footer from "@/components/Footer.vue"
import PronunciationMenu from "@/components/PronunciationMenu.vue"
import { mapState, mapMutations } from "vuex"
import AuthUserMenu from "@/components/AuthUserMenu.vue"

export default {
  name: "Landing",

  metaInfo: {
    title: "Timeful",
  },

  components: {
    LandingPageCalendar,
    FAQ,
    Header,
    NumberBullet,
    NewEvent,
    NewDialog,
    LandingPageHeader,
    Logo,
    SignInDialog,
    Footer,
    PronunciationMenu,
    AuthUserMenu,
  },

  data: () => ({
    signInDialog: false,
    newDialog: false,
    githubSnackbar: true,
    howItWorksSteps: [
      "Create a Timeful event",
      "Share the Timeful link with your group for them to fill out",
      "See where everybody's availability overlaps!",
    ],
    faqs: [
    ],
    rive: null,
    showSchejy: false,
    showHowItWorksDialog: false,
  }),

  computed: {
    ...mapState(["authUser"]),
    isPhone() {
      return isPhone(this.$vuetify)
    },
  },

  methods: {
    ...mapMutations(["setAuthUser"]),
    loadRiveAnimation() {
      // if (!this.rive) {
      //   this.rive = new Rive({
      //     src: "/rive/schej.riv",
      //     canvas: document.querySelector("canvas"),
      //     autoplay: false,
      //     stateMachines: "wave",
      //     onLoad: () => {
      //       // r.resizeDrawingSurfaceToCanvas()
      //     },
      //   })
      //   setTimeout(() => {
      //     this.showSchejy = true
      //     setTimeout(() => {
      //       this.rive.play("wave")
      //     }, 1000)
      //   }, 4000)
      // } else {
      //   this.rive.play("wave")
      // }
    },
    _signIn(calendarType) {
      if (calendarType === calendarTypes.GOOGLE) {
        signInGoogle({ state: null, selectAccount: true })
      }
    },
    _emailSignIn(user) {
      this.setAuthUser(user)
      this.$posthog?.identify(user._id, {
        email: user.email,
        firstName: user.firstName,
        lastName: user.lastName,
      })
      this.$router.replace({ name: "home" })
    },
    signIn() {
      this.$router.push({ name: "sign-in" })
    },
    openHowItWorksDialog() {
      this.showHowItWorksDialog = true
      this.$posthog.capture("how_it_works_clicked")
    },
    openDashboard() {
      this.$router.push({ name: "home" })
    },
  },

  beforeDestroy() {
    this.rive?.cleanup()
  },

  watch: {
    [`$vuetify.breakpoint.name`]: {
      immediate: true,
      handler() {
        if (this.$vuetify.breakpoint.mdAndUp) {
          setTimeout(() => {
            this.loadRiveAnimation()
          }, 0)
        }
      },
    },
  },
}
</script>
