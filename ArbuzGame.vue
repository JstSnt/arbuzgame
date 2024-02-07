<template>
  <v-container>
    <v-card elevation="24">
      <v-card-text>
        <v-data-table v-if="data" :headers="headers" :items="data" density="compact">
          <template v-slot:[`item.name`]="{ item }">
            <v-dialog width="20%">
              <template v-slot:activator="{ props }">
                <div v-bind="props">{{ item.name }}</div>
              </template>
              <template v-slot:default="{ isActive }">
                <v-card :title="item.name">
                  <v-card-text>
                    <v-text-field v-for="(k,v) in item" varitant="solo" :label="v" :key="k.id" class="end" @change="update(item)" density="compact" variant="plain" hide-details
                      v-model="item[v]" />
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text="Закрыть" @click="isActive.value = false"></v-btn>
                  </v-card-actions>
                </v-card>
              </template>
            </v-dialog>
          </template>
          <template v-slot:[`item.howMuch`]="{ item }">
            <v-text-field density="compact" variant="outlined" v-model="item.howMuch" hide-details />
          </template>
          <template v-slot:[`item.addOne`]="{ item }">
            <v-btn @click="addOne(item)" size="x-small" icon="$plus" />
          </template>
          <template v-slot:bottom />
        </v-data-table>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-chip>Общий доход: {{ total.toLocaleString('ru-RU', { maximumFractionDigits: 0 }) }} в сек.</v-chip>
        <v-btn @click="save" text="Сохранить" />
      </v-card-actions>
    </v-card>
    <v-card elevation="24">
      <v-card-text>
        <v-data-table v-if="clickdata" :headers="headers" :items="clickdata" density="compact">
          <template v-slot:[`item.name`]="{ item }">
            <v-dialog width="20%">
              <template v-slot:activator="{ props }">
                <div v-bind="props">{{ item.name }}</div>
              </template>
              <template v-slot:default="{ isActive }">
                <v-card :title="item.name">
                  <v-card-text>
                    <v-text-field v-for="(k,v) in item" varitant="solo" :label="v" :key="k.id" class="end" @change="update(item)" density="compact" variant="plain" hide-details
                      v-model="item[v]" />
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn text="Закрыть" @click="isActive.value = false"></v-btn>
                  </v-card-actions>
                </v-card>
              </template>
            </v-dialog>
          </template>
          <template v-slot:[`item.howMuch`]="{ item }">
            <v-text-field density="compact" variant="outlined" v-model="item.howMuch" hide-details />
          </template>
          <template v-slot:[`item.addOne`]="{ item }">
            <v-btn @click="addOne(item)" size="x-small" icon="$plus" />
          </template>
          <template v-slot:bottom />
        </v-data-table>
      </v-card-text>
      <v-card-actions>
        <v-spacer />
        <v-chip>Клик приносит {{ clicktotal.toLocaleString('ru-RU', { maximumFractionDigits: 0 }) }}.</v-chip>
        <v-btn @click="save" text="Сохранить" />
      </v-card-actions>
    </v-card>
  </v-container>
</template>
<script setup>
import { onMounted, ref, computed } from 'vue'
const headers = [
  { title: 'Название', value: 'name' },
  { title: 'Уровень', align: 'end', value: 'level' },
  { title: 'Добавляет', align: 'end', value: 'gives' },
  { title: 'Цена', align: 'end', key: 'price', value: item => Number(item.price)?.toLocaleString('ru-RU', { maximumFractionDigits: 0 }) },  { title: 'Стоимость +1', align: 'end', key: 'forOne', value: item => item.forOne?.toLocaleString('ru-RU', { maximumFractionDigits: 2 }) },
  { title: 'Сколько штук', value: 'howMuch' },
  { title: 'Сумма', align: 'end', key: 'sum', value: item => item.howMuch > 0 ? (item.howMuch * item.price + (item.howMuch - 1) * item.update).toLocaleString('ru-RU', { maximumFractionDigits: 0 }) : 0 },
  { title: 'Плюсег', key: 'addOne', sortable: false, align: 'end' },
]

const base = [
  { id: 1, price: 0, level: 0, gives: 1, start: 450, update: 607.5, name: '🏡Бабушкин огород' },
  { id: 2, price: 0, level: 0, gives: 3, start: 1_000, update: 4_050, name: '🪟Теплица' },
  { id: 3, price: 0, level: 0, gives: 8, start: 0, update: 44_550, name: '🌾Плантация' },
  { id: 4, price: 0, level: 0, gives: 47, start: 360_000, update: 486_000, name: '🏢Вертикальная ферма' },
  { id: 5, price: 0, level: 0, gives: 260, start: 0, update: 5_265_000, name: '🚿Гидропонная система' },
  { id: 6, price: 0, level: 0, gives: 1400, start: 42_000_000, update: 56_700_000, name: '🛰️Космическая станция' },
  { id: 7, price: 0, level: 0, gives: 7800, start: 600_000_000, update: 1_020_000_000, name: '🪐Планета-сателлит' },
  { id: 8, price: 0, level: 0, gives: 44000, start: 9_900_000_000, update: 0, name: '🌌Арбузная галактика' },
]
const clickbase = [
  { id: 1, price: 0, level: 0, gives: 1, start: 200, update: 250, name: '💩Компост' },
  { id: 2, price: 0, level: 0, gives: 3, start: 1_100, update: 1_375, name: '🧪Удобрения' },
  { id: 3, price: 0, level: 0, gives: 9, start: 6_050, update: 7_562.5, name: '⚗️Азотовая подкормка' },
  { id: 4, price: 0, level: 0, gives: 27, start: 33_250, update: 41_562.5, name: '💡Инфракрасные излучатели' },
  { id: 5, price: 0, level: 0, gives: 81, start: 182_850, update: 5_265_000, name: '🌬️Рецеркулятор К100' },
  { id: 6, price: 0, level: 0, gives: 243, start: 1_005_000, update: 56_700_000, name: '🌱Ускоритель роста А1000' },
  { id: 7, price: 0, level: 0, gives: 729, start: 5_527_500, update: 1_020_000_000, name: '🛸Инопланетные технологии' },
  { id: 8, price: 0, level: 0, gives: 2187, start: 30_401_250, update: 38_001_562.5, name: '🌞Искусственное солнце' },
  { id: 9, price: 152_006_250, level: 0, gives: 6561, start: 152_006_250, update: 0, name: '⚛️Бета-частицы' },
]

const data = ref([])
const clickdata = ref([])
const total = computed(() => data.value.reduce((a, b) => a + b.gives * b.level, 0))
const clicktotal = computed(() => 1 + clickdata.value.reduce((a, b) => a + b.gives * b.level, 0))
const update = (item) => item.forOne = item.price / item.gives
const addOne = (item) => {
  const multiply = item.howMuch || 1
  item.level = Number(item.level) + Number(multiply)
  item.price = Number(item.price) + Number(item.update) * multiply
  item.forOne = item.price / item.gives
}
const save = () => {
  localStorage.setItem('arbuz', JSON.stringify(data.value))
  localStorage.setItem('clickarbuz', JSON.stringify(clickdata.value))
}
onMounted(() => {
  data.value = JSON.parse(localStorage.getItem('arbuz')) || base
  clickdata.value = JSON.parse(localStorage.getItem('clickarbuz')) || clickbase
  if (data.value.length < base.length) data.value = [...data.value, ...base.filter(o => !~data.value.map(e => e.id).indexOf(o.id))]
  if (clickdata.value.length < clickbase.length) clickdata.value = [...clickdata.value, ...clickbase.filter(o => !~clickdata.value.map(e => e.id).indexOf(o.id))]
})

</script>
<style scoped>
.end>>>input {
  text-align: end
}
</style>
