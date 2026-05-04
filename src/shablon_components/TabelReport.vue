<template>
  <div class="!flex h-full w-full flex-col gap-4 p-5">

    <!-- ════════════ PAGE HEADER ════════════ -->
    <div class="flex items-center justify-between flex-wrap gap-3">

      <!-- Title -->
      <div>
        <h1 class="text-xl font-bold text-slate-800 tracking-tight">
          <!-- [CURSOR] Название раздела -->
          Название раздела
        </h1>
        <!-- [CURSOR] Подзаголовок — убери если не нужен -->
        <p class="text-sm text-slate-400 mt-0.5">
          Управление записями
        </p>
      </div>

      <!-- Header actions -->
      <div class="flex items-center gap-1">

        <!-- [CURSOR] Кнопка Refresh — убери если не нужна -->
        <q-btn
          flat
          dense
          icon="refresh"
          size="md"
          class="bg-primary text-white rounded-lg py-1.5 px-2"
          :loading="loading"
          @click="getData({ filter: filter, pagination: pagination })"
        >
          <q-tooltip class="bg-primary text-xs">Обновить</q-tooltip>
        </q-btn>

        <!-- [CURSOR] Кнопка Экспорт — убери если не нужна -->
        <!-- <q-btn flat dense icon="download" label="Excel" size="sm"
          class="text-slate-500 hover:text-slate-800 border border-slate-200 hover:border-slate-300 rounded-xl px-3 transition-all duration-150"
          @click="onExport"
        /> -->

        <!-- [CURSOR] Кнопка Добавить -->
        <q-btn
          flat
          dense
          size="md"
          class="bg-primary text-white rounded-lg normal-case px-3 py-1.5"
          :loading="loading"
          @click="onAdd"
        >
          <div class="flex items-center gap-2">
            <q-icon size="sm" name="add"></q-icon>
            <span>Добавить</span>
          </div>
        </q-btn>

      </div>
    </div>

    <!-- ════════════ ФИЛЬТРЫ ════════════ -->
    <div class="bg-white rounded-lg border border-slate-200/80 shadow-sm p-3 flex flex-wrap items-center gap-2.5">

      <!-- [CURSOR] Поиск — убери если не нужен -->
      <q-input
        v-model="filter.search"
        dense
        outlined
        clearable
        placeholder="Поиск..."
        class="rounded-2xl min-w-[220px]"
        input-class="text-sm"
        @update:model-value="onFilterChange"
      >
        <template #prepend>
          <q-icon name="search" size="22px" class="text-slate-400" />
        </template>
      </q-input>

      <!-- [CURSOR] Select по статусу — убери если не нужен -->
      <!--
      <q-select
        v-model="filter.status"
        :options="statusOptions"
        dense
        outlined
        clearable
        emit-value
        map-options
        option-value="value"
        option-label="label"
        label="Статус"
        class="min-w-[160px]"
        options-dense
        @update:model-value="onFilterChange"
      />
      -->

      <!-- [CURSOR] Select по категории из API — убери если не нужен -->
      <!--
      <q-select
        v-model="filter.category_id"
        :options="categoryOptions"
        dense
        outlined
        clearable
        emit-value
        map-options
        option-value="id"
        option-label="name"
        label="Категория"
        class="min-w-[180px]"
        options-dense
        @update:model-value="onFilterChange"
      />
      -->

      <!-- [CURSOR] Диапазон дат — убери если не нужен -->
      <!--
      <q-input v-model="filter.date_from" dense outlined clearable type="date" label="От" class="min-w-[150px]" @update:model-value="onFilterChange" />
      <q-input v-model="filter.date_to"   dense outlined clearable type="date" label="До" class="min-w-[150px]" @update:model-value="onFilterChange" />
      -->

      <!-- Spacer -->
      <div class="flex-1" />

      <!-- Кнопка сброса -->
      <q-btn
        flat
        dense
        size="md"
        class="bg-cyan-600 text-white rounded-lg normal-case px-3 py-1.5"
        :loading="loading"
        @click="resetFilter"
      >
        <div class="flex items-center gap-2">
          <q-icon size="sm" name="filter_list_off"></q-icon>
          <span>Сбросить</span>
        </div>
      </q-btn>

    </div>

    <!-- ════════════ ТАБЛИЦА ════════════ -->
    <!--
      [CURSOR] Настройки таблицы:
        row-key     — уникальное поле (обычно "id")
        style       — высота: подбери calc(100vh - X) под свой layout
        selection   — "none" / "single" / "multiple"
    -->

    <div class="relative flex-1">
      <q-table
        flat
        ref="tableRef"
        row-key="id"
        :rows="rows"
        hide-no-data
        selection="none"
        :filter="filter"
        :columns="columns"
        @request="getData"
        :loading="loading"
        v-model:selected="selectedRows"
        v-model:pagination="pagination"
        :rows-per-page-options="[10, 20, 50, 100]"
        class="my-sticky-header-table-report w-full h-full overflow-hidden rounded-lg border border-slate-200/80 bg-white shadow-sm"
        table-header-class="bg-primary-100 text-slate-700 uppercase tracking-wider"
        style="height: calc(100vh - 270px);"
      >
        <!-- Лоадер -->
        <template #loading>
          <q-inner-loading showing>
            <q-spinner-dots size="36px" color="primary" />
          </q-inner-loading>
        </template>

        <!-- ════════════ КАСТОМНЫЕ СЛОТЫ КОЛОНОК ════════════ -->
        <!--
          [CURSOR] Добавляй слоты по образцу ниже.
          Имя слота = name из columns[] → body-cell-{name}
        -->


        <!-- ════════════ Структура заголовков ════════════ -->
        <template #header>
          <!--    уровень 1    -->
          <!--    colspan="[colspan]" rowspan="[rowspan]"    -->
          <!--    t('[name]')    -->

          <q-tr>
            <q-th colspan="3">
              {{ t('name') }}
            </q-th>
          </q-tr>

          <!--    уровень 2    -->

          <q-tr>
            <q-th>{{ t('name') }}</q-th>
          </q-tr>

          <!--    уровень 2    -->
          <q-tr>
            <q-th colspan="3">
              {{ t('captions.headerTons') }}
            </q-th>
          </q-tr>

          <!--    остальные уровени    -->
        </template>
      </q-table>

      <!--   Используй это вместо no-data   -->
      <div v-if="rows && rows.length > 0" class="flex w-full column no-wrap items-center gap-3 py-16 absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2">
        <div class="flex h-16 w-16 items-center justify-center rounded-2xl bg-slate-100">
          <q-icon name="inbox" size="32px" class="text-slate-300" />
        </div>
        <div class="text-center">
          <p class="text-sm font-medium text-slate-500">{{ t('system.noData') }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useComp } from '../composables/useComp'
import { $axios } from 'src/boot/axios'
import {piniaActions} from "src/stores/piniaActions";
import {useI18n} from "vue-i18n";
import {useQuasar} from "quasar";
// import { useRouter } from 'vue-router'

const { t } = useI18n()
// const router = useRouter()
const { tableFilterQuery, parsePaginationQuery, showError, formatDate, formatPrice, ask } = useComp()

// ════════════ API ════════════
// [CURSOR] Замени на реальный endpoint
const apiUrl = '/api/your-endpoint'

// ════════════ TABLE STATE ════════════
const tableRef = ref()
const rows = ref([])
const selectedRows = ref([])
const loading = ref(true)

// ════════════ PAGINATION ════════════
const pagination = ref({
  sortBy: 'id',        // [CURSOR] поле сортировки по умолчанию
  descending: true,
  page: 1,
  rowsPerPage: 20,
  rowsNumber: 0,
})
// ════════════ ADD ROW ════════════
const defaultFormBean = {
  name: '',
}
const formBean = ref({...defaultFormBean})


// ════════════ FILTER ════════════
// [CURSOR] Добавь поля которые используешь в шаблоне фильтров выше
const defaultFilterData = {
  search: '',
  // status: null,
  // category_id: null,
  // date_from: null,
  // date_to: null,
}
const filter = ref({ ...defaultFilterData })

// ════════════ COLUMNS ════════════
// [CURSOR] Замени на свои колонки
// name     — ключ поля (используется в #body-cell-{name} слотах)
// label    — заголовок
// align    — 'left' | 'center' | 'right'
// field    — поле из данных или fn: (row) => row.nested.value
// sortable — разрешить сортировку
// style    — ширина колонки
const columns = ref([
  {
    name: 'id',
    label: '#',
    align: 'left',
    field: 'id',
    sortable: true,
    style: 'width: 60px; color: #94a3b8; font-size: 12px;',
  },
  {
    name: 'name',
    label: 'Название',   // [CURSOR] заголовок
    align: 'left',
    field: 'name',       // [CURSOR] поле из API
    sortable: true,
  },
  // [CURSOR] Добавляй колонки по образцу:
  // { name: 'status',     label: 'Статус',   align: 'center', field: 'status',     sortable: false },
  // { name: 'amount',     label: 'Сумма',    align: 'right',  field: 'amount',     sortable: true },
  // { name: 'created_at', label: 'Создан',   align: 'left',   field: 'created_at', sortable: true },
  // { name: 'full_name',  label: 'ФИО',      align: 'left',   field: 'full_name',  sortable: true },
  {
    name: 'actions',
    label: '',
    align: 'right',
    field: 'actions',
    sortable: false,
    style: 'width: 110px',
  },
])

// ════════════ СПРАВОЧНИКИ (для select фильтров) ════════════
// [CURSOR] Раскомментируй и замени на свои справочники
// const statusOptions = ref([
//   { value: 'active',   label: 'Активный' },
//   { value: 'inactive', label: 'Неактивный' },
//   { value: 'banned',   label: 'Заблокирован' },
// ])
// const categoryOptions = ref([])
// async function fetchCategories() {
//   const res = await $axios.get('/api/categories?perPage=999')
//   categoryOptions.value = res.data.rows
// }

// ════════════ BADGE HELPERS ════════════
// [CURSOR] Настрой под свои статусы
// function statusClass(status) {
//   const map = {
//     active:   'bg-emerald-100 text-emerald-700',
//     inactive: 'bg-slate-100 text-slate-500',
//     pending:  'bg-amber-100 text-amber-600',
//     banned:   'bg-red-100 text-red-600',
//   }
//   return map[status] ?? 'bg-slate-100 text-slate-500'
// }
// function statusLabel(status) {
//   const map = {
//     active:   'Активный',
//     inactive: 'Неактивный',
//     pending:  'Ожидает',
//     banned:   'Заблокирован',
//   }
//   return map[status] ?? status
// }

// ════════════ LOAD DATA ════════════
async function getData(params: any) {
  loading.value = true
  pagination.value = params.pagination

  await $axios
    .get(apiUrl + tableFilterQuery(params.filter) + parsePaginationQuery(params.pagination))
    .then((res) => {
      // [CURSOR] Адаптируй под структуру ответа:
      // Вариант 1 (стандарт): { rows: [], total: 0 }
      rows.value = res.data.rows
      pagination.value.rowsNumber = res.data.total
      // Вариант 2: { data: [], count: 0 }
      // rows.value = res.data.data
      // pagination.value.rowsNumber = res.data.count
    })
    .catch((err) => showError(err))
    .finally(() => (loading.value = false))
}

// ════════════ FILTER HANDLERS ════════════
function onFilterChange() {
  pagination.value.page = 1
  getData({ filter: filter.value, pagination: pagination.value })
}

function resetFilter() {
  filter.value = { ...defaultFilterData }
  onFilterChange()
}

// ════════════ ROW ACTIONS ════════════
// [CURSOR] Реализуй: диалог / роутинг / API

function onAdd() {
  // Вариант 1: диалог
  // dialogRef.value = true
  // Вариант 2: роутинг
  // router.push('/section/create')
  console.log('Add')
}

function onView(row: any) {
  console.log('View', row)
}

function onEdit(row: any) {
  console.log('Edit', row)
}

function onDelete(row: any) {
  // [CURSOR] Подтверждение и удаление:
  // ask(t("app_name"), t("system.confirm"), () => {
  //   $axios.delete(`${apiUrl}/${row.id}`).catch(showError)
  //   getData({ filter: filter.value, pagination: pagination.value })
  // })
  console.log('Delete', row)
}

// ════════════ EXPORT (раскомментируй если нужен) ════════════
// async function onExport() {
//   const res = await $axios.get(apiUrl + '/export' + tableFilterQuery(filter.value), {
//     responseType: 'blob'
//   })
//   const url = URL.createObjectURL(res.data)
//   const a = document.createElement('a')
//   a.href = url; a.download = 'export.xlsx'; a.click()
//   URL.revokeObjectURL(url)
// }

// ════════════ INIT ════════════
onMounted(async () => {
  // [CURSOR] Параллельная загрузка справочников:
  await Promise.all([
    // fetchCategories(),
    // fetchStatuses(),
  ]).finally(() => {
    getData({ filter: filter.value, pagination: pagination.value })
  })
})
</script>

<style></style>
