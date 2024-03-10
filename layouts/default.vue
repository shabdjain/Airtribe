<template>
    <div>
      <AppHeader />
      <Nuxt />
      <AppFooter />
    </div>
  </template>
  <script>
import AppHeader from '../components/inc/AppHeader.vue';
import AppFooter from '../components/inc/AppFooter.vue';

export default{
    name: 'Default',
    components: {
        AppHeader,
        AppFooter,

    },
}
</script>
