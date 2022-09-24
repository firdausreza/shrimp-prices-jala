<template>
  <div class="container">
    <CThemeProvider>
      <CBox font-family="body" as="main" backgroundColor="gray.200">
        <CReset />
        <Nuxt />
      </CBox>
    </CThemeProvider>
  </div>
</template>
<script>
import {
  CThemeProvider,
  CColorModeProvider,
  CReset,
  CBox
} from '@chakra-ui/vue'
import 'vue-select/dist/vue-select.css';

export default {
  name: 'DefaultLayout',
  components: {
    CThemeProvider,
    CColorModeProvider,
    CReset,
    CBox
  }
}
</script>
