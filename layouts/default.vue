<template>
  <v-app dark>
    <v-main>
      <v-container class="fill-height">
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer absolute app dark>
      <span class="copy-"
        >&copy; {{ new Date().getFullYear() }} Umihime Developer Team</span
      >
      <img
        class="umihime-logo"
        :src="require('@/static/logo/Sea-Net[FullColor].svg')"
      />
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
}
</script>

<style lang="scss">
.umihime-logo {
  margin-right: 0;
  margin-left: auto;
  height: 24px;
  width: auto;
}
@media (min-width: 1000px) {
  .umihime-logo {
    height: 40px;
  }
}
</style>
