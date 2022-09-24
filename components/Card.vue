<template>
  <c-flex backgroundColor="white" direction="column" border="1px" borderColor="gray.400" borderRadius="10px" :width="['100%', null, customWidth]" color="gray.500" :mt="marginTop" :ml="marginLeft">
    <c-flex width="100%" p="4" borderBottom="1px" borderColor="gray.400" align="center" justify="space-between">
      <c-text fontWeight="bold">
        {{ headerTitle }}
      </c-text>
      <slot name="button"></slot>
    </c-flex>
    <c-box width="100%" :p="padding" :overflowX="overflowX">
      <slot name="body"></slot>
    </c-box>
    <slot name="footer"></slot>
  </c-flex>
</template>

<script>
import {
  CBox,
  CFlex,
  CText
} from "@chakra-ui/vue";

export default {
  name: "Card",
  components: {
    CBox,
    CFlex,
    CText
  },
  props: {
    headerTitle: String,
    padding: {
      type: String,
      default: '0'
    },
    customWidth: {
      type: String,
      default: '100%'
    },
    overflowX: {
      type: String,
      default: ''
    },
    marginTop: {
      type: Array,
      default: () => ([])
    },
    marginLeft: {
      type: Array,
      default: () => ([])
    }
  }
}
</script>

<style scoped>

</style>
