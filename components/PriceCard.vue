<template>
  <c-box minWidth="280px" width="max-content" backgroundColor="gray.100" border="1px" borderColor="gray.300" rounded="5px" p="4">
    <c-flex align="start" justify="space-between" color="black">
      <div style="width: 50%">
        <c-heading color="blue.600" as="h1" fontSize="md">
          {{ price.region.province_name }}
        </c-heading>
        <c-text color="blue.600" fontSize="sm">
          {{ price.region.name }}
        </c-text>
        <c-text fontSize="sm">
          Size 100:
        </c-text>
        <c-text fontSize="lg" fontWeight="bold">
          Rp {{ new Intl.NumberFormat(['ban', 'id']).format(price.size_100) }}
        </c-text>
      </div>
      <div style="text-align: right; width: 50%">
        <c-text fontSize="sm">
          {{ moment(price && price.date).format('DD MMMM YYYY') }}
        </c-text>
        <c-text fontSize="sm">
          Supplier:
        </c-text>
        <c-text fontSize="sm" fontWeight="bold">
          {{ price.creator.name }}
        </c-text>
        <c-text fontSize="sm">
          {{ isShown ? price.creator.phone : '+62xxxxxxxxxxx' }}
        </c-text>
      </div>
    </c-flex>
    <c-flex width="100%" align="center" justify="space-between" mt="4">
      <c-button variant-color="blue" size="sm" fontWeight="normal" fontSize="sm">
        LIHAT DETAIL
      </c-button>
      <c-button @click="toggleContact" variant-color="blue" variant="outline" size="sm" fontWeight="normal" ref="seeContact" fontSize="sm">
        {{ isShown ? 'SALIN KONTAK' : 'LIHAT KONTAK' }}
      </c-button>
    </c-flex>
    <c-flex align="center" justify="center" mt="4">
      <c-text fontSize="sm" mr="4">
        Bagikan:
      </c-text>
      <c-text fontSize="sm" textAlign="center" lineHeight="1">
        <span style="font-size: 16px;">
          96
        </span>
        <br> shares
      </c-text>
      <font-awesome-icon icon="fa-brands fa-facebook" class="fa-xl" style="color: #003182; margin-left: 16px;" />
      <font-awesome-icon icon="fa-brands fa-whatsapp" class="fa-xl" style="color: #58C184; margin-left: 16px;" />
      <font-awesome-icon icon="fa-brands fa-twitter" class="fa-xl" style="color: #0BC5EA; margin-left: 16px;" />
    </c-flex>
  </c-box>
</template>

<script>
import {
  CBox, CFlex, CText, CImage, CButton, CHeading, CLink
} from "@chakra-ui/vue";
import moment from 'moment'

export default {
  name: "PriceCard",
  components: {
    CBox, CFlex, CText, CImage, CButton, CHeading, CLink
  },
  props: {
    price: {
      type: Object,
      default: {}
    }
  },
  data() {
    return {
      moment: moment,
      isShown: false,
    }
  },
  methods: {
    toggleContact() {
      if (this.isShown) {
        this.$refs.seeContact.$el.innerText = 'TERSALIN'
        setTimeout(() => {
          this.$refs.seeContact.$el.innerText = 'SALIN KONTAK'
        }, 3000)
      } else {
        this.isShown = true
      }
    }
  }
}
</script>

<style scoped>

</style>
