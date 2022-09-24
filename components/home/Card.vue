<template>
  <c-flex width="100%" direction="column" justify="start" p="4" backgroundColor="white" border="1px" borderColor="gray.400" rounded="10">
    <c-heading as="h1" size="sm" color="gray.600">
      {{ price.region.name }}
    </c-heading>
    <c-flex width="100%" align="center" justify="start">
      <c-text fontSize="sm" color="gray.600" fontWeight="bold">
        Rp {{ new Intl.NumberFormat(['ban', 'id']).format(currentPrice) }}
      </c-text>
      <c-text fontSize="sm" ml="4" :backgroundColor="renderDiff" color="white" px="2" py="1" rounded="14px">
        {{ Number(priceDiff) < 0 ? '-' : '+' }} Rp {{ new Intl.NumberFormat(['ban', 'id']).format(Math.abs(priceDiff)) }}
      </c-text>
    </c-flex>
    <c-text fontSize="sm" color="gray.600" mb="2">
      {{ moment(price.date).format('DD MMM YYYY') }}
    </c-text>
    <c-box position="relative">
      <line-chart
        :chart-options="chartOptions"
        :chart-data="chartData"
        :width="100"
        :height="100"
        dataset-id-key="label"
      />
    </c-box>
  </c-flex>
</template>

<script>
import {
  CBox, CHeading, CText, CFlex
} from "@chakra-ui/vue";
import moment from 'moment';

export default {
  name: "HomeCard",
  components: {
    CBox, CHeading, CText, CFlex
  },
  props: {
    price: {
      type: Object,
      default: {}
    },
    sizeSelected: {
      type: String,
      default: 'size_50'
    }
  },
  data() {
    return {
      moment: moment,
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        hover: {
          mode: 'point',
          intersect: false
        },
        elements: {
          line: {
            borderColor: 'red',
            backgroundColor: 'rgb(255, 110, 110)'
          }
        },
        scales: {
          x: {
            grid: {
              display: false,
              drawBorder: false
            },
            ticks: {
              display: false,
              beginAtZero: false,
            }
          },
          y: {
            grid: {
              display: false,
              drawBorder: false
            },
            ticks: {
              display: false,
              beginAtZero: false,
            }
          }
        },
        plugins: {
          legend: {
            display: false
          }
        }
      },
      chartData: {
        labels: [],
        datasets: [
          {
            label: 'Size',
            data: [],
            fill: true,
            hoverBorderColor: ctx => this.up(ctx, 'rgb(48, 240, 125)') || this.down(ctx, 'rgb(245, 34, 34)'),
            hoverBackgroundColor: ctx => this.up(ctx, 'rgb(48, 240, 125)') || this.down(ctx, 'rgb(245, 34, 34)')
          }
        ],
      },
      currentPrice: 0,
      priceDiff: 0
    }
  },
  computed: {
    renderDiff() {
      if (this.priceDiff < 0) {
        return 'red.400'
      } else {
        return 'green.400'
      }
    },
  },
  methods: {
    getAllSize() {
      for(const [key, value] of Object.entries(this.price)) {
        if (key.includes('size')) {
          if (key === 'size_110') {
            break;
          } else if (key === 'size_20') {
            continue;
          } else {
            this.chartData.labels.push(key)
            this.chartData.datasets[0].data?.push(value)
          }
        }
      }
      this.currentPrice = this.chartData.datasets[0].data[this.chartData.datasets[0].data.length - 1]
      this.priceDiff = this.chartData.datasets[0].data[this.chartData.datasets[0].data.length - 1] - this.chartData.datasets[0].data[this.chartData.datasets[0].data.length - 2]
    },
    up(ctx, value) {
      const index = ctx.dataIndex;
      this.currentPrice = ctx.raw;
      if (index === 0) {
        this.priceDiff = 0;
      } else {
        this.priceDiff = ctx.raw - ctx.dataset.data[index - 1];
      }
      if (ctx.dataset.data[index - 1] < ctx.dataset.data[index]) {
        this.chartOptions.elements.line.borderColor = 'rgb(2, 196, 103)'
        this.chartOptions.elements.line.backgroundColor = value
        return value
      } else {
        return undefined
      }
    },
    down(ctx, value) {
      const index = ctx.dataIndex;
      this.currentPrice = ctx.raw;
      if (index === 0) {
        this.priceDiff = 0;
      } else {
        this.priceDiff = ctx.raw - ctx.dataset.data[index - 1];
      }
      if (ctx.dataset.data[index - 1] > ctx.dataset.data[index]) {
        this.chartOptions.elements.line.borderColor = value
        this.chartOptions.elements.line.backgroundColor = 'rgb(255, 110, 110)'
        return value
      } else {
        return undefined
      }
    }
  },
  mounted() {
    this.getAllSize()
  }
}
</script>

<style>
  .line-chart {
    width: 100%;
  }
</style>
