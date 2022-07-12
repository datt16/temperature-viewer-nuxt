<template>
  <v-col justify="center">
    <h1>
      <v-icon large color="rgba(0,0,0)">
        mdi-pool
      </v-icon>
      校内プール
    </h1>

    <section>
      <h3>{{ convertDateToCustomString(new Date()) }}</h3>
      <h2>{{ convertDateToString(new Date()) }}</h2>
    </section>

    <v-row align="center" justify="center">
      <!--  PC画面用のUI -->
      <v-col v-if="$vuetify.breakpoint.smAndUp" cols="6">
        <v-card v-if="!loading" class="value-card py-6 mt-4" rounded="lg">
          <v-card-title>
            <span class="mx-2">プール内の水温</span>
          </v-card-title>

          <v-row class="mx-4">
            <v-col>
              <span class="text-h2 font-weight-bold">{{
                records[0].value.toFixed(1)
              }}</span>
              <span class="text-h4">℃</span>
            </v-col>

            <v-divider vertical class="mx-2"></v-divider>

            <v-col>
              <v-row no-gutters>
                <span class="text-body-2"> 30分前との差 </span>
              </v-row>
              <v-row no-gutters>
                <v-icon v-if="delta > 0" small class="mr-2" color="error"
                  >mdi-triangle
                </v-icon>
                <v-icon
                  v-if="delta < 0"
                  small
                  class="mr-2"
                  color="success"
                  style="transform: rotate(180deg)"
                  >mdi-triangle
                </v-icon>

                <span class="text-h6 font-weight-bold">
                  {{ delta.toFixed(1) }}℃
                </span>
              </v-row>
            </v-col>
          </v-row>
          <v-card-actions>
            <v-row class="mx-4" no-gutters align="center">
              <v-icon class="mr-1" small>mdi-cached</v-icon>
              <span class="text-caption">{{
                convertDateToString(new Date(records[0].datetime))
              }}</span>
            </v-row>
          </v-card-actions>
        </v-card>
      </v-col>

      <!--  モバイル画面用のUI -->
      <v-col v-if="$vuetify.breakpoint.xs" cols="11">
        <v-card v-if="!loading" class="value-card py-6 mt-4" rounded="lg">
          <v-card-title>
            <span class="mx-2">プール内の水温</span>
          </v-card-title>
          <v-col>
            <v-col>
              <span class="text-h2 font-weight-bold">{{
                records[0].value.toFixed(1)
              }}</span>
              <span class="text-h4">℃</span>
            </v-col>

            <v-divider class="my-3"></v-divider>

            <v-col>
              <v-row no-gutters>
                <span class="text-body-2"> 30分前との差 </span>
              </v-row>
              <v-row no-gutters>
                <v-icon v-if="delta > 0" small class="mr-2" color="error"
                  >mdi-triangle
                </v-icon>
                <v-icon
                  v-if="delta < 0"
                  small
                  class="mr-2"
                  color="success"
                  style="transform: rotate(180deg)"
                  >mdi-triangle
                </v-icon>

                <span class="text-h6 font-weight-bold">
                  {{ delta.toFixed(1) }}℃
                </span>
              </v-row>
            </v-col>
          </v-col>

          <v-card-actions>
            <v-row class="mx-4" no-gutters align="center">
              <v-icon class="mr-1" small>mdi-cached</v-icon>
              <span class="text-caption">{{
                convertDateToString(new Date(records[0].datetime))
              }}</span>
            </v-row>
          </v-card-actions>
        </v-card>
      </v-col>

      <!-- グラフ -->
      <!-- PC画面用のUI -->
      <v-col v-if="$vuetify.breakpoint.smAndUp" cols="5">
        <v-card v-if="!loading" class="value-card py-4 mt-1" rounded="lg">
          <v-card-title>
            <span class="mx-2">水温推移</span>
          </v-card-title>

          <v-row class="mx-4">
            <v-col>
              <v-sheet
                class="v-sheet--offset mx-auto mt-5"
                color="cyan"
                elevation="5"
                max-width="calc(100% - 32px)"
              >
                <v-sparkline
                  :labels="label"
                  :value="dots"
                  :show-labels="true"
                  color="white"
                  line-width="2"
                  padding="16"
                ></v-sparkline>
              </v-sheet>
            </v-col>
          </v-row>
          <v-card-actions>
            <v-row class="mx-4" no-gutters align="center">
              <v-icon class="mr-1" small>mdi-cached</v-icon>
              <span class="text-caption">{{
                convertDateToString(new Date(records[0].datetime))
              }}</span>
            </v-row>
          </v-card-actions>
        </v-card>
      </v-col>

      <!-- モバイル画面用のUI -->
      <v-col v-if="$vuetify.breakpoint.xs" cols="11">
        <v-card v-if="!loading" class="value-card py-4 mt-1" rounded="lg">
          <v-card-title>
            <span class="mx-2">水温推移</span>
          </v-card-title>
          <v-col>
            <v-sheet
              class="v-sheet--offset mx-auto mt-3"
              color="cyan"
              elevation="5"
              max-width="calc(100% - 32px)"
            >
              <v-sparkline
                :labels="label"
                :value="dots"
                color="white"
                line-width="2"
                padding="16"
              ></v-sparkline>
            </v-sheet>
          </v-col>

          <v-card-actions>
            <v-row class="mx-4" no-gutters align="center">
              <v-icon class="mr-1" small>mdi-cached</v-icon>
              <span class="text-caption">{{
                convertDateToString(new Date(records[0].datetime))
              }}</span>
            </v-row>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-col>
</template>

<script lang="ts">
import {
  defineComponent,
  useAsync,
  useContext,
  ref,
} from '@nuxtjs/composition-api'

interface Record {
  channel: number
  datetime: string
  id: string
  module: string
  type: string
  value: number
}

// type GraphRecord  = {
//   dots: number
// }

export default defineComponent({
  name: 'RecordPage',
  setup() {
    const app = useContext()

    // state(データの状態)を保存しておく場所を定義
    const records = ref<Record[]>([])
    const loading = ref<boolean>(true)
    const delta = ref<number>(0)
    const dots = ref<number[]>([])
    const label = ref<string[]>([])

    // axios使ってデータをAPIから取得 -> stateに代入
    useAsync(async () => {
      records.value = await app.$axios.get(app.$config.API_URL).then((res) => {
        return res.data.results
      })
      delta.value = records.value[9].value - records.value[0].value
      loading.value = false

      for (let d = 9; d >= 0; d--) {
        // dots.value[d] = records.value[d].value
        dots.value.push(records.value[d].value)
        label.value.push(convertDateToString(new Date(records.value[d].datetime)))
      }
    })


    // date型 -> 時間を表示される形式に変換(0埋め済)
    const convertDateToString = (date: Date) => {
      const hour = date.getHours()
      const minutes = date.getMinutes()
      return `${hour}:${minutes < 10 ? '0' + minutes : minutes}`
    }

    // date型 -> yyyy/mm/dd (曜日)のフォーマット
    const convertDateToCustomString = (date: Date) => {
      const year = date.getFullYear()
      const month =
        date.getMonth() + 1 < 10
          ? '0' + (date.getMonth() + 1)
          : date.getMonth() + 1
      const day = date.getDate() < 10 ? '0' + date.getDate() : date.getDate()
      const dayIndex = date.getDay()

      const week = ['日', '月', '火', '水', '木', '金', '土']

      return ` ${year}年 ${month}月 ${day}日 (${week[dayIndex]})`
    }

    return {
      convertDateToString,
      convertDateToCustomString,
      records,
      delta,
      dots,
      loading,
      label,
    }
  },
})
</script>

<style>
.v-sheet--offset {
  top: -24px;
  position: relative;
}
</style>
