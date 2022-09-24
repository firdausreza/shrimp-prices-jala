<template>
  <div>
    <c-flex width="100%" direction="row" align="start" justify="space-between" p="6" backgroundColor="white" borderY="1px" borderColor="gray.400">
      <c-heading fontSize="md" fontWeight="bold" color="blue.600" textAlign="left">
        {{ shrimpPrice ? shrimpPrice.region.province_name : '' }} <br />
        <span style="color: black; font-size: 14px; font-weight: normal;">
          {{ shrimpPrice ? shrimpPrice.region.name : '' }}
        </span>
      </c-heading>
      <c-button as="router-link" to="/" variant-color="cyan" size="xs" fontWeight="normal">
        KEMBALI
      </c-button>
    </c-flex>
    <c-flex width="100%" p="4" :direction="['column', null, 'row']" align="start" justify="center">
      <Card v-show="!isLoading" header-title="Detail Harga Udang" padding="6" custom-width="60%">
        <template v-slot:body>
          <c-flex direction="row" align="start" justify="space-between">
            <c-text fontSize="xs" color="black">
              {{ shrimpPrice ? moment(shrimpPrice && shrimpPrice.date).format('DD MMMM YYYY') : '' }} <br/>
              <span style="font-weight: bold">
                Udang Vannamei (Pennaeus Vannamei)
              </span>
            </c-text>
            <c-text fontSize="xs" color="black">
              Supplier: <br/>
              <span style="font-weight: bold">
                {{ shrimpPrice ? shrimpPrice.creator.name : '' }}
              </span>
            </c-text>
            <c-text fontSize="xs" color="black" mr="6">
              Kontak: <br/>
              {{ isShown ? shrimpPrice.creator.phone : '+62xxxxxxxxxxx' }} <br />
              <c-link @click="toggleContact" color="blue.600" ref="contactButton">
                {{ isShown ? 'Salin Kontak' : 'Lihat Kontak' }}
              </c-link>
            </c-text>
          </c-flex>
          <c-flex direction="column" justify="center" mt="4" p="4">
            <c-flex v-for="price in getSizeOnly" :key="price.labels" width="100%" align="center" borderTop="2px" borderColor="gray.200" p="2">
              <c-text width="50%" fontSize="sm" fontWeight="bold" color="black">
                Size {{ price.labels.split('_')[1] }}
              </c-text>
              <c-text width="50%" fontSize="sm" color="black">
                Rp {{ new Intl.NumberFormat(['ban', 'id']).format(price.value) }}
              </c-text>
            </c-flex>
          </c-flex>
          <c-flex width="100%" align="center" justify="space-between">
            <c-text fontSize="sm" fontWeight="bold" color="black" width="50%">
              Catatan <br>
              <span style="font-weight: normal">
                {{ shrimpPrice && shrimpPrice.remark ? shrimpPrice.remark : 'Tidak ada' }}
              </span>
            </c-text>
            <c-flex align="center" justify="space-between" border="1px" borderColor="gray.400" borderRadius="5px" p="2">
              <c-text fontSize="sm" textAlign="center" lineHeight="1">
                <span style="font-size: 16px;">
                  96
                </span>
                <br> shares
              </c-text>
              <font-awesome-icon icon="fa-brands fa-facebook" class="fa-2xl" style="color: #003182; margin-left: 16px;" />
              <font-awesome-icon icon="fa-brands fa-whatsapp" class="fa-2xl" style="color: #58C184; margin-left: 16px;" />
              <font-awesome-icon icon="fa-brands fa-twitter" class="fa-2xl" style="color: #0BC5EA; margin-left: 16px;" />
            </c-flex>
          </c-flex>
        </template>
      </Card>
      <Card v-show="!isLoading" :header-title="`Riwayat Harga Udang di Daerah ${shrimpPrice ? shrimpPrice.region.province_name : ''}`" padding="4" custom-width="40%" :margin-top="['4', '0']" :margin-left="['0', '4']">
        <template v-slot:body>
          <c-flex direction="column" align="center" justify="center">
            <c-flex align="center">
              <c-button @click="toggleHistoryPrediction" :fontWeight="historyPredictionView === 'history' ? 'bold' : 'normal'" border="1px" borderColor="gray.200" :backgroundColor="historyPredictionView === 'history' ? 'blue.50' : 'transparent'" roundedLeft="5px" roundedRight="0" px="6" size="sm" color="black">
                Riwayat
              </c-button>
              <c-button @click="toggleHistoryPrediction" :fontWeight="historyPredictionView === 'prediction' ? 'bold' : 'normal'" border="1px" borderColor="gray.200" :backgroundColor="historyPredictionView === 'prediction' ? 'blue.50' : 'transparent'" roundedRight="5px" roundedLeft="0" px="6" size="sm" color="black">
                Prediksi
              </c-button>
            </c-flex>
          </c-flex>
          <c-flex width="100%" direction="column" v-if="historyPredictionView === 'history'" justify="center">
            <c-flex width="100%" py="4">
              <c-input type="date" borderColor="gray.300" roundedRight="0" />
              <c-text borderY="1px" borderColor="gray.200" py="1" px="2">
                -
              </c-text>
              <c-input type="date" borderColor="gray.300" roundedLeft="0" />
            </c-flex>
            <c-stack spacing="5" is-inline alignSelf="center" mt="4" color="gray.700">
              <c-checkbox v-model="size100Selected" variant-color="blue" fontWeight="bold">
                Size 100
              </c-checkbox>
              <c-checkbox v-model="size70Selected" variant-color="green" fontWeight="bold">
                Size 70
              </c-checkbox>
              <c-checkbox v-model="size40Selected" variant-color="yellow" fontWeight="bold">
                Size 40
              </c-checkbox>
            </c-stack>
            <c-box position="relative">
              <line-chart
                :chart-options="chartOptions"
                :chart-data="chartData"
                dataset-id-key="label"
              />
            </c-box>
            <c-box px="4" py="2" backgroundColor="yellow.50" border="1px" borderColor="orange.300" rounded="md" mt="8">
              <c-heading as="h1" fontWeight="bold" fontSize="sm" color="gray.600">
                <font-awesome-icon icon="fa-solid fa-circle-info" />
                Informasi
              </c-heading>
              <c-text mt="2" color="gray.600" fontSize="sm">
                Hasil diatas merupakan sebuah prediksi dari berdasarkan data yang telah tercatat di Jala. Prediksi harga udang ini hanya sebagai referensi,
                bukan sebuah rujukan yang harus diikuti. Jala tidak bertanggung jawab atas kerugian yang ditimbulkan karena menggunakan prediksi
                harga udang
              </c-text>
            </c-box>
            <c-button color="blue.300" fontWeight="normal" variant="link" alignSelf="start" mt="2" size="sm" textDecoration="underline">
              Lihat detail trend
            </c-button>
            <c-flex direction="column" align="center" justify="center" py="4">
              <c-text fontSize="sm">
                Tertarik untuk berbagi info harga udang?
              </c-text>
              <c-button size="sm" variant-color="blue" fontSize="14px" fontWeight="semibold" mt="2">
                TAMBAH HARGA DI DAERAH SAYA
              </c-button>
            </c-flex>
          </c-flex>
          <c-flex direction="column" v-else-if="historyPredictionView === 'prediction'">
            <c-box p="6">
              <c-text fontSize="sm" color="gray.700" fontWeight="bold">
                Prediksi seminggu kedepan
              </c-text>
              <c-box mt="4">
                <c-flex align="start" justify="space-evenly" pt="2" pb="4" backgroundColor="gray.50">
                  <c-text fontSize="sm" fontWeight="bold" color="blue.300" width="30%" textAlign="center">
                    Size
                  </c-text>
                  <c-text fontSize="sm" fontWeight="bold" color="blue.300" width="40%" textAlign="center">
                    Harga Min
                  </c-text>
                  <c-text fontSize="sm" fontWeight="bold" color="blue.300" width="30%" textAlign="center">
                    Harga Max
                  </c-text>
                </c-flex>
                <c-flex align="start" justify="space-evenly" py="4" borderTop="2px" borderColor="gray.300">
                  <c-text fontSize="sm" fontWeight="bold" color="gray.600" width="30%" textAlign="center">
                    40
                  </c-text>
                  <c-text fontSize="sm" width="40%" textAlign="center">
                    Rp {{ shrimpPrice ? new Intl.NumberFormat(['ban', 'id']).format(Math.abs(shrimpPrice.shrimp_price_per_week_region_id?.min_size_40)) : '' }}
                  </c-text>
                  <c-text fontSize="sm" width="30%" textAlign="center">
                    Rp {{ shrimpPrice ? new Intl.NumberFormat(['ban', 'id']).format(Math.abs(shrimpPrice.shrimp_price_per_week_region_id?.max_size_40)) : '' }}
                  </c-text>
                </c-flex>
                <c-flex align="start" justify="space-evenly" py="4" borderTop="2px" borderColor="gray.300">
                  <c-text fontSize="sm" fontWeight="bold" color="gray.600" width="30%" textAlign="center">
                    70
                  </c-text>
                  <c-text fontSize="sm" width="40%" textAlign="center">
                    Rp {{ shrimpPrice ? new Intl.NumberFormat(['ban', 'id']).format(Math.abs(shrimpPrice.shrimp_price_per_week_region_id?.min_size_70)) : '' }}
                  </c-text>
                  <c-text fontSize="sm" width="30%" textAlign="center">
                    Rp {{ shrimpPrice ? new Intl.NumberFormat(['ban', 'id']).format(Math.abs(shrimpPrice.shrimp_price_per_week_region_id?.max_size_70)) : '' }}
                  </c-text>
                </c-flex>
                <c-flex align="start" justify="space-evenly" py="4" borderTop="2px" borderColor="gray.300">
                  <c-text fontSize="sm" fontWeight="bold" color="gray.600" width="30%" textAlign="center">
                    100
                  </c-text>
                  <c-text fontSize="sm" width="40%" textAlign="center">
                    Rp {{ shrimpPrice ? new Intl.NumberFormat(['ban', 'id']).format(Math.abs(shrimpPrice.shrimp_price_per_week_region_id?.min_size_100)) : '' }}
                  </c-text>
                  <c-text fontSize="sm" width="30%" textAlign="center">
                    Rp {{ shrimpPrice ? new Intl.NumberFormat(['ban', 'id']).format(Math.abs(shrimpPrice.shrimp_price_per_week_region_id?.max_size_100)) : '' }}
                  </c-text>
                </c-flex>
              </c-box>
            </c-box>
            <c-box px="4" py="2" backgroundColor="yellow.50" border="1px" borderColor="orange.300" rounded="md" mt="8">
              <c-heading as="h1" fontWeight="bold" fontSize="sm" color="gray.600">
                <font-awesome-icon icon="fa-solid fa-circle-info" />
                Informasi
              </c-heading>
              <c-text mt="2" color="gray.600" fontSize="sm">
                Hasil diatas merupakan sebuah prediksi dari berdasarkan data yang telah tercatat di Jala. Prediksi harga udang ini hanya sebagai referensi,
                bukan sebuah rujukan yang harus diikuti. Jala tidak bertanggung jawab atas kerugian yang ditimbulkan karena menggunakan prediksi
                harga udang
              </c-text>
            </c-box>
            <c-button color="blue.300" fontWeight="normal" variant="link" alignSelf="start" mt="2" size="sm" textDecoration="underline">
              Lihat detail trend
            </c-button>
            <c-flex direction="column" align="center" justify="center" py="4">
              <c-text fontSize="sm">
                Tertarik untuk berbagi info harga udang?
              </c-text>
              <c-button size="sm" variant-color="blue" fontSize="14px" fontWeight="semibold" mt="2">
                TAMBAH HARGA DI DAERAH SAYA
              </c-button>
            </c-flex>
          </c-flex>
        </template>
      </Card>
    </c-flex>
    <c-flex :direction="['column', 'row']" maxWidth="100%" align="center" justify="center" p="4">
      <c-box width="100%">
        <c-image width="100%" src="/banner-1.png" alt="Banner" />
      </c-box>
      <c-box width="100%" :marginLeft="['0', '6']" :marginTop="['4', '0']">
        <c-image width="100%" src="/banner-panen-udang.png" alt="Banner" />
      </c-box>
    </c-flex>
    <c-box width="100%" p="4">
      <Card v-show="!isLoading" header-title="Harga lainnya" padding="4" overflow-x="auto">
        <template v-slot:body>
          <c-grid :template-columns="`repeat(${listOtherPrices ? listOtherPrices.length : '6'}, 280px)`" gap="6">
            <price-card v-for="otherPrice in listOtherPrices" :key="otherPrice.id" :price="otherPrice" />
          </c-grid>
        </template>
      </Card>
    </c-box>
    <c-flex :direction="['column', 'row']" maxWidth="100%" align="center" justify="center" p="4">
      <c-box width="100%">
        <c-image width="100%" src="/banner-produk.png" alt="Banner" />
      </c-box>
      <c-box width="100%" :marginLeft="['0', '6']" :marginTop="['4', '0']">
        <c-image width="100%" src="/banner-smart-farm.jpeg" alt="Banner" />
      </c-box>
    </c-flex>
  </div>
</template>

<script>
import {
  CBox, CFlex, CText, CImage, CButton, CHeading, CLink, CGrid, CInput, CStack, CCheckbox, CCheckboxGroup
} from "@chakra-ui/vue";
import Card from "~/components/Card.vue";
import PriceCard from "~/components/PriceCard.vue";
import shrimpPricesJson from '~/store/shrimp_prices.json';
import moment from 'moment'

export default {
  name: "id",
  components: {
    CBox, CFlex, CText, CImage, CButton, CHeading, CLink, CGrid, CInput, CStack, CCheckbox, Card, PriceCard
  },
  computed: {
    getSizeOnly() {
      if (this.shrimpPrice) {
        let arr = []
        for(const [key, value] of Object.entries(this.shrimpPrice)) {
          if (key.includes('size')) {
            arr.push({
              labels: key,
              value: value
            })
          }
        }
        return arr
      }
    },
  },
  watch: {
    size100Selected(newVal) {
      if (newVal) {
        this.chartData.datasets[0].data = this.size100Data
      } else {
        this.chartData.datasets[0].data = []
      }
    },
    size70Selected(newVal) {
      if (newVal) {
        this.chartData.datasets[1].data = this.size70Data
      } else {
        this.chartData.datasets[1].data = []
      }
    },
    size40Selected(newVal) {
      if (newVal) {
        this.chartData.datasets[2].data = this.size40Data
      } else {
        this.chartData.datasets[2].data = []
      }
    }
  },
  data() {
    return {
      moment: moment,
      shrimpPrice: null,
      shrimpId: this.$route.params.id,
      isShown: false,
      listOtherPrices: null,
      isLoading: true,
      historyPredictionView: 'history',
      size100Selected: true,
      size70Selected: true,
      size40Selected: true,
      size100Data: [],
      size70Data: [],
      size40Data: [],
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: {
          legend: {
            display: false
          },
          tooltip: {
            mode: 'index',
            intersect: false,
            position: 'nearest'
          },
        }
      },
      chartData: {
        labels: ['11 Sep 2022', '12 Sep 2022', '13 Sep 2022', '14 Sep 2022', '15 Sep 2022', '16 Sep 2022', '17 Sep 2022'],
        datasets: [
          {
            data: [],
            fill: true,
            borderColor: 'rgb(0, 88, 230)',
            backgroundColor: 'rgba(128, 176, 255, 0.2)',
            order: 0
          },
          {
            data: [],
            fill: true,
            borderColor: 'rgb(62, 167, 106)',
            backgroundColor: 'rgba(162, 221, 186, 0.2)',
            order: 1
          },
          {
            data: [],
            fill: true,
            borderColor: 'rgb(230, 172, 0)',
            backgroundColor: 'rgba(255, 223, 128, 0.2)',
            order: 2
          }
        ],
      },
    }
  },
  mounted() {
    this.shrimpPrice = shrimpPricesJson.data.filter((item) => item.id === Number(this.shrimpId))[0]
    this.listOtherPrices = shrimpPricesJson.data.filter((item) => item.id !== Number(this.shrimpId)).slice(0, 6)
    for (let i=0; i < 7; i++) {
      if (this.shrimpPrice) {
        if (i === 0) {
          this.size100Data.push(this.shrimpPrice.size_100)
          this.size70Data.push(this.shrimpPrice.size_70)
          this.size40Data.push(this.shrimpPrice.size_40)
        }
        else {
          this.size100Data.push(Math.round(Math.random() * (Math.floor(this.shrimpPrice.size_100 + 10000) - Math.ceil(this.shrimpPrice.size_100)) + Math.ceil(this.shrimpPrice.size_100)))
          this.size70Data.push(Math.round(Math.random() * (Math.floor(this.shrimpPrice.size_70 + 10000) - Math.ceil(this.shrimpPrice.size_70)) + Math.ceil(this.shrimpPrice.size_70)))
          this.size40Data.push(Math.round(Math.random() * (Math.floor(this.shrimpPrice.size_40 + 10000) - Math.ceil(this.shrimpPrice.size_40)) + Math.ceil(this.shrimpPrice.size_40)))
        }
      }
    }
    this.chartData.datasets[0].data = this.size100Data
    this.chartData.datasets[1].data = this.size70Data
    this.chartData.datasets[2].data = this.size40Data
    this.isLoading = false
  },
  methods: {
    toggleContact() {
      if (this.isShown) {
        this.$refs.contactButton.$el.innerText = 'Tersalin'
        setTimeout(() => {
          this.$refs.contactButton.$el.innerText = 'Salin Kontak'
        }, 3000)
      } else {
        this.isShown = true
      }
    },
    toggleHistoryPrediction() {
      if (this.historyPredictionView === 'history') {
        this.historyPredictionView = 'prediction'
      } else {
        this.historyPredictionView = 'history'
      }
    }
  }
}
</script>

<style scoped>

</style>
