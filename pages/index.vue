<template>
  <div>
    <c-flex maxWidth="100%" direction="row" align="center" justify="center" p="6" m="auto" backgroundColor="white">
      <c-text :display="['none', null, 'block']" fontSize="md" fontWeight="bold" color="gray.600">Filter: </c-text>
      <c-flex :direction="['column', null, 'row']" align="center" justify="center" :marginLeft="['0', null, '16']" :width="['100%', null, '75%']">
        <c-box width="100%">
          <v-select
            class="select-input"
            placeholder="Pilih Lokasi"
            v-model="selectedRegion"
            :options="(regions || []).map((item) => {return {label: item.name, id: item.id, name: item.full_name}})"
            :multiple="false"
          />
        </c-box>
        <c-flex :direction="['column', 'row', 'row', 'row']" width="100%" align="center" :marginLeft="['0', null, '4']" :marginTop="['4', null, '0']">
          <c-box width="100%">
            <v-select
              class="select-input"
              v-model="selectedSize"
              :options="sizeOptions"
              :value="selectedSize.value"
              :multiple="false"
            />
          </c-box>
          <c-box width="100%" :marginLeft="['0', '4']" :marginTop="['2', '0']">
            <v-select
              class="select-input"
              v-model="shrimpSpecies"
              :options="[{label: 'Vannamei', value: 'vannamei'}]"
              :multiple="false"
              :value="shrimpSpecies"
            />
          </c-box>
        </c-flex>
      </c-flex>
    </c-flex>
    <c-flex :direction="['column', 'row']" maxWidth="100%" align="center" justify="center" px="8" py="4">
      <c-box width="100%">
        <c-image width="100%" src="/banner-1.png" alt="Banner" />
      </c-box>
      <c-box width="100%" :marginLeft="['0', '6']" :marginTop="['4', '0']">
        <c-image width="100%" src="/banner-panen-udang.png" alt="Banner" />
      </c-box>
    </c-flex>
    <c-flex :direction="['column', null, null, 'row']" width="100%" align="start" justify="center" px="8" py="4">
      <c-box :width="['100%', null, null, '60%']">
        <Card :header-title="`Persebaran Harga Udang ${selectedSize && selectedSize.label}`">
          <template v-slot:body>
            <div class="mapouter">
              <div class="gmap_canvas">
                <iframe class="gmap_iframe" width="100%" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" src="https://maps.google.com/maps?width=700&amp;height=400&amp;hl=en&amp;q=Jala Tech&amp;t=&amp;z=7&amp;ie=UTF8&amp;iwloc=B&amp;output=embed"></iframe>
              </div>
              <style>.mapouter{position:relative;text-align:right;width:100%;height:350px;}.gmap_canvas {overflow:hidden;background:none!important;width:100%;height:350px;}.gmap_iframe {height:350px!important;}</style>
            </div>
          </template>
          <template v-slot:footer>
            <c-flex m="auto" p="4" align="center" justify="space-between">
              <c-flex align="center">
                <div style="width: 20px; height: 20px; background-color: rgb(103, 105, 99)"></div>
                <c-text ml="1">
                  > 1 Bulan
                </c-text>
              </c-flex>
              <c-flex align="center" ml="4">
                <div style="width: 20px; height: 20px; background-color: rgb(19, 56, 120)"></div>
                <c-text ml="1">
                  > 1 Minggu
                </c-text>
              </c-flex>
              <c-flex align="center" ml="4">
                <div style="width: 20px; height: 20px; background-color: rgb(27, 119, 223)"></div>
                <c-text ml="1">
                  Baru
                </c-text>
              </c-flex>
            </c-flex>
          </template>
        </Card>
      </c-box>
      <c-flex direction="column" :width="['100%', null, null, '40%']" align="flex-start" :pl="['0', null, null, '4']" :mt="['4', null, null, '0']">
        <c-heading fontWeight="bold" textAlign="left" size="sm">
          Trend harga di berbagai daerah
        </c-heading>
        <c-simple-grid width="100%" min-child-width="250px" :spacing="6" mt="4">
          <home-card v-for="price in getPriceLocations" :key="price.id" :price="price" :size-selected="selectedSize.value" />
        </c-simple-grid>
      </c-flex>
    </c-flex>
    <c-flex :direction="['column', 'row']" maxWidth="100%" align="center" justify="center" px="8" py="4">
      <c-box width="100%">
        <c-image width="100%" src="/banner-produk.png" alt="Banner" />
      </c-box>
      <c-box width="100%" :marginLeft="['0', '6']" :marginTop="['4', '0']">
        <c-image width="100%" src="/banner-smart-farm.jpeg" alt="Banner" />
      </c-box>
    </c-flex>
    <c-flex :direction="['column', 'row']" maxWidth="100%" align="center" justify="center" px="8" py="4">
      <Card header-title="LIST HARGA UDANG" padding="4">
        <template v-slot:button>
          <c-button variant-color="blue" size="xs" fontWeight="normal">
            TAMBAHKAN HARGA
          </c-button>
        </template>
        <template v-slot:body>
          <client-only>
            <vue-good-table
              :columns="columnTable"
              :rows="selectedRegion ? filteredShrimpPrices : shrimpPrices"
              styleClass="vgt-table striped"
              max-height="400px"
              :rtl="false"
              :sort-options="{
                enabled: true,
                initialSortBy: {field: 'date', type: 'desc'}
              }"
              compact-mode
            >
              <template slot="table-column" slot-scope="props">
                <span v-if="props.column.label === 'Harga'">
                  {{props.column.label}} {{selectedSize.label}}
                </span>
              </template>
              <template slot="table-row" slot-scope="props">
                <span v-if="props.column.field === 'region'">
                  <CText color="blue.600" textAlign="left" fontWeight="bold">
                    {{ props.row.region.province_name }} <br/>
                    <span style="color: black; font-size: 14px; font-weight: normal;">
                      {{ props.row.region.name }}
                    </span>
                  </CText>
                </span>
                <span v-else-if="props.column.field === 'detail-action'">
                  <c-button as="router-link" variant-color="blue" size="sm" fontWeight="normal" :to="`/harga_udang/${props.row.id}`">
                    LIHAT DETAIL
                  </c-button>
                  <font-awesome-icon icon="fa-brands fa-facebook" class="fa-2xl" style="color: #003182; margin-left: 8px;" />
                  <font-awesome-icon icon="fa-brands fa-whatsapp" class="fa-2xl" style="color: #58C184; margin-left: 8px;" />
                  <font-awesome-icon icon="fa-brands fa-twitter" class="fa-2xl" style="color: #0BC5EA; margin-left: 8px;" />
                </span>
              </template>
            </vue-good-table>
          </client-only>
        </template>
      </Card>
    </c-flex>
  </div>
</template>

<script>
import {
  CBox, CFlex, CText, CImage, CButton, CHeading, CSimpleGrid, CStack,
} from "@chakra-ui/vue";
import VueSelect from 'vue-select';
import shrimpPricesJson from '~/store/shrimp_prices.json';
import Card from "~/components/Card.vue";
import HomeCard from '~/components/home/Card.vue';

export default {
  name: "Home",
  components: {
    CBox, CFlex, CText, CImage, CButton, CHeading, CSimpleGrid, CStack,
    'v-select': VueSelect,
    Card,
    HomeCard
  },
  data() {
    return {
      regions: null,
      selectedRegion: null,
      shrimpPrices: null,
      filteredShrimpPrices: null,
      isLoading: false,
      sizeOptions: [],
      selectedSize: '',
      shrimpSpecies: 'Vannamei',
      markers: [],
      center: {
        lat: -7.797603,
        lng: 110.36518
      },
      columnTable: [
        {
          label: 'TANGGAL',
          field: 'date',
          type: 'date',
          dateInputFormat: 'yyyy-MM-dd',
          dateOutputFormat: 'dd MMMM yyyy',
          width: '200px',
          thClass: 'vgt-left-align',
          tdClass: 'vgt-left-align'
        },
        {
          label: 'LOKASI',
          field: 'region',
          html: true,
          width: '250px'
        },
        {
          label: 'SUPPLIER',
          field: 'creator.name',
          width: '250px'
        },
        {
          label: 'Harga',
          field: this.sizeShrimpFormat,
          formatFn: (value) => {
            if (value === null) {
              value = 0;
            }
            return `Rp ${new Intl.NumberFormat(['ban', 'id']).format(value)}`
          },
          width: '150px'
        },
        {
          label: '',
          field: 'detail-action',
          width: '280px',
          sortable: false
        }
      ]
    }
  },
  created() {
    this.isLoading = true;
  },
  computed: {
    getPriceLocations() {
      if (this.shrimpPrices) {
        return this.shrimpPrices.slice(0, 4);
      } else {
        return []
      }
    }
  },
  watch: {
    selectedRegion(newVal) {
      if (newVal !== null) {
        this.filteredShrimpPrices = shrimpPricesJson.data.filter((item) => item.region_id === this.selectedRegion.id)
      }
    }
  },
  methods: {
    setSizeOptions() {
      for (let key of Object.keys((this.shrimpPrices || [])[0])) {
        if (key.includes('size_')) {
          this.sizeOptions.push({
            label: `Size ${key.split('_')[1]}`,
            value: key
          })
        }
      }
      this.selectedSize = this.sizeOptions[3]
    },
    sizeShrimpFormat(rowData) {
      return rowData[this.selectedSize.value]
    },
  },
  async mounted() {
    this.shrimpPrices = shrimpPricesJson ? shrimpPricesJson.data : null;
    this.regions = (this.shrimpPrices || []).map((item) => item.region)
    await this.setSizeOptions();

    this.markers = (this.regions || []).map((item) => {
      return {
        position: {
          lat: Number(item.latitude),
          lng: Number(item.longitude)
        }
      }
    });
    this.isLoading = false;
  }
}
</script>

<style scoped>
  .select-input {
    width: 100%;
  }
  .vue-map-container, .vue-map-container .vue-map {
    width: 100%;
    height: 100%;
  }
</style>
