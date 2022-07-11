<template>
  <v-col justify="center">
    <h1>プールの水温</h1>

    <section>
      <h3>{{ convertDateToCustomString(new Date()) }}</h3>
      <h2>{{ convertDateToString(new Date()) }}</h2>
    </section>

    <v-row align="center" justify="center">
      <v-col cols="8">
        <v-card v-if="!state.loading" class="value-card py-6 mt-4" rounded="lg">
          <v-card-title>
            <span class="mx-2">水温センサー１</span>
          </v-card-title>

          <!--  PC画面用のUI -->
          <v-row v-if="$vuetify.breakpoint.smAndUp" class="mx-4">
            <v-col>
              <span class="text-h2 font-weight-bold">{{
                state.records[0].value.toFixed()
              }}</span>
              <span class="text-h4">℃</span>
            </v-col>

            <v-divider vertical class="mx-2"></v-divider>

            <v-col>
              <v-row no-gutters>
                <span class="text-body-2"> 30分前との差 </span>
              </v-row>
              <v-row no-gutters>
                <v-icon v-if="state.delta > 0" small class="mr-2" color="error"
                  >mdi-triangle
                </v-icon>
                <v-icon
                  v-if="state.delta < 0"
                  small
                  class="mr-2"
                  color="success"
                  style="transform: rotate(180deg)"
                  >mdi-triangle
                </v-icon>

                <span class="text-h6 font-weight-bold">
                  {{ state.delta.toFixed(1) }}℃
                </span>
              </v-row>
            </v-col>
          </v-row>

          <!--  モバイル画面用のUI -->
          <v-col v-if="$vuetify.breakpoint.xs">
            <v-col>
              <span class="text-h2 font-weight-bold">{{
                state.records[0].value.toFixed()
              }}</span>
              <span class="text-h4">℃</span>
            </v-col>

            <v-divider class="my-3" />

            <v-col>
              <v-row no-gutters>
                <span class="text-body-2"> 30分前との差 </span>
              </v-row>
              <v-row no-gutters>
                <v-icon v-if="state.delta > 0" small class="mr-2" color="error"
                  >mdi-triangle
                </v-icon>
                <v-icon
                  v-if="state.delta < 0"
                  small
                  class="mr-2"
                  color="success"
                  style="transform: rotate(180deg)"
                  >mdi-triangle
                </v-icon>

                <span class="text-h6 font-weight-bold">
                  {{ state.delta.toFixed(1) }}℃
                </span>
              </v-row>
            </v-col>
          </v-col>

          <v-card-actions>
            <v-row class="mx-4" no-gutters align="center">
              <v-icon class="mr-1" small>mdi-cached</v-icon>
              <span class="text-caption">{{
                convertDateToString(new Date(state.records[0].datetime))
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
  reactive,
} from '@nuxtjs/composition-api'

type Record = {
  channel: number
  datetime: string
  id: string
  module: string
  type: string
  value: number
}

export default defineComponent({
  name: 'RecordPage',
  setup() {
    const app = useContext()

    // state(データの状態)を保存しておく場所を定義
    const state = reactive({
      records: [] as Record[],
      loading: true,
      delta: 0,
    })

    // axios使ってデータをAPIから取得 -> stateに代入
    useAsync(async () => {
      state.records = await app.$axios.get(app.$config.API_URL).then((res) => {
        return res.data.results
      })
      state.delta = state.records[9].value - state.records[0].value
      state.loading = false
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
      state,
      convertDateToString,
      convertDateToCustomString,
    }
  },
})
</script>

<style lang="scss">
.value-card {
  height: 100%;
}
</style>
