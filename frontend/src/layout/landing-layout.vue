<template>
  <center class="landing-layout">
    <Intro />
    <WhyWeCreate style="margin-top:15rem;"/>
    <WhatCanDo />
    <WhatWeChange />
    <Footer />
  </center>
</template>

<script>
import { useStore } from 'vuex';
import { computed, onBeforeUnmount, onMounted, ref } from 'vue';
import { onBeforeRouteUpdate } from 'vue-router';

import Intro from '@/pages/landing/intro/Intro.vue';
import WhyWeCreate from '@/pages/landing/why-we-create/WhyWeCreate.vue';
import WhatCanDo from '@/pages/landing/what-can-do/WhatCanDo.vue';
import WhatWeChange from '@/pages/landing/what-we-change/WhatWeChange.vue';
import Footer from '@/pages/landing/footer/Footer.vue';


export default {
  name: 'landing-layout',

  components: {
    Intro,
    WhyWeCreate,
    WhatCanDo,
    WhatWeChange,
    Footer,
  },

  setup() {
    const store = useStore()
    const mobileBreakPointPX = 640
    const tabletBreakPointPX = 768

    const sidebarWidth = ref('16rem')
    const sidebarMinimizedWidth = ref(undefined)

    const isMobile = ref(false)
    const isTablet = ref(false)
    const isSidebarMinimized = computed(() => store.state.isSidebarMinimized)
    const checkIsTablet = () => window.innerWidth <= tabletBreakPointPX
    const checkIsMobile = () => window.innerWidth <= mobileBreakPointPX

    const onResize = () => {
      store.commit('updateSidebarCollapsedState', checkIsTablet())

      isMobile.value = checkIsMobile()
      isTablet.value = checkIsTablet()
      sidebarMinimizedWidth.value = isMobile.value ? 0 : '4rem'
      sidebarWidth.value = isTablet.value ? '100%' : '16rem'
    }

    onMounted(() => {
      window.addEventListener('resize', onResize)
    })

    onBeforeUnmount(() => {
      window.removeEventListener('resize', onResize)
    })

    onBeforeRouteUpdate(() => {
      if (checkIsTablet()) {
        // Collapse sidebar after route change for Mobile
        store.commit('updateSidebarCollapsedState', true)
      }
    })

    onResize()

    const isFullScreenSidebar = computed(() => isTablet.value && !isSidebarMinimized.value)

    const onCloseSidebarButtonClick = () => {
      store.commit('updateSidebarCollapsedState', true)
    }

    return {
      isSidebarMinimized,
      sidebarWidth, sidebarMinimizedWidth,
      isFullScreenSidebar, onCloseSidebarButtonClick
    }
  }

}
</script>

<style lang="scss">
$mobileBreakPointPX: 640px;
$tabletBreakPointPX: 768px;

.landing-layout {
  height: 110vh;
  width: 80%;
  margin: auto;
}

.f4 {
  font-weight: bold;
  color: #212121;
  text-shadow: 1px 1px 1px gray;
  font-size: 3vw;
  margin-bottom: 3rem;
  margin-top: 10rem;
}
</style>
