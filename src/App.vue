<template>
  <DataTable :value="cars" scrollable scrollHeight="400px" :virtualScrollerOptions="{ itemSize: 46 }"
    tableStyle="min-width: 50rem" v-model:filters="filters" :loading="loading" :globalFilterFields="['vin']"
    removableSort sortMode="multiple">
    <template #header>

      <div class="flex justify-between max-sm:block">
        <div class="flex gap-2 max-sm:block max-sm:grid max-sm:gap-2">
          <Button type="button" icon="pi pi-filter-slash" label="Clear" outlined @click="clearFilter" />
          <IconField>
            <InputIcon>
              <i class="pi pi-search" />
            </InputIcon>
            <InputText v-model="filters['global'].value" placeholder="Keyword Search" />
          </IconField>
        </div>
        <Settings :columns="columns" @toggle-column-visibility="toggleColumnVisibility" />
      </div>
    </template>
    <Column v-for="(col, index) of columns" :field="col.field" :style="col.visible === false ? 'display: none;' : ''"
      :header="col.header" sortable :key="col.field + '_' + index">
    </Column>

  </DataTable>
</template>


<script setup>
import { ref, onMounted } from 'vue';
import { CarService } from '@/service/CarService';
import { FilterMatchMode, FilterOperator } from '@primevue/core/api';
import Settings from '../src/components/ColumnSettings.vue';

const cars = ref([]);
const columns = ref([
  { field: 'id', header: 'Id', visible: true },
  { field: 'vin', header: 'Vin', visible: true },
  { field: 'year', header: 'Year', visible: true },
  { field: 'brand', header: 'Brand', visible: true },
  { field: 'color', header: 'Color', visible: true },
]);


const loading = ref(false);
const filters = ref({});

const initFilters = () => {
  filters.value = {
    global: { value: null, matchMode: FilterMatchMode.CONTAINS },
    vin: {
      operator: FilterOperator.AND,
      constraints: [{ value: null, matchMode: FilterMatchMode.STARTS_WITH }],
    },
  };
};

initFilters();

const clearFilter = () => {
  initFilters();
};

onMounted(async () => {
  loading.value = true;
  cars.value = await fetchCars();
  loading.value = false;
});

async function fetchCars() {
  return Array.from({ length: 10000 }).map((_, i) => CarService.generateCar(i + 1));
}

const onToggle = (val) => {
  columns.value = val;
};

const toggleColumnVisibility = (key) => {
  columns.value = columns.value.map((col) => {
    if (col.field === key) {
      col.visible = !col.visible
      return col
    }
    return col
  });
  console.log(columns)
};

</script>

<style scoped>
.draggable-column {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.draggable-column li {
  padding: 0.5rem;
  background-color: #f0f0f0;
  border: 1px solid #ccc;
  cursor: grab;
}

td:hover {
  background-color: black !important;
}

td:active {
  background-color: black !important;
}

td:visited {
  background-color: black !important;
}

td:focus {
  background-color: black !important;
}

td:focus-visible {
  background-color: black !important;
}
</style>
