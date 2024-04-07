<script setup lang="ts">
import { ref, computed } from 'vue';

const columns = [{
  key: 'title',
  label: 'Назва',
  sortable: true
}, {
  key: 'description',
  label: 'Опис',
  sortable: true
}, {
  key: 'price',
  label: 'Ціна',
  sortable: true
}, {
  key: 'rating',
  label: 'Оцінка',
  sortable: true
}, {
  key: 'brand',
  label: 'Бренд',
  sortable: true
}, {
  key: 'category',
  label: 'Категорія',
  sortable: true
}, {
  key: 'thumbnail',
  label: 'Фото',
  width: '100px',
  align: 'center',
  render: (h: any, { row }: { row: Record<string, any> }) => h('img', {
    src: row.thumbnail,
    alt: row.title,
    style: { width: '100px', height: '100px' }
  })
}];

const q = ref('');
const page = ref(1);
const pageCount = 10;

const filteredRows = ref<any[]>([]);
const totalRows = ref(0);

const fetchData = async () => {
  try {
    const response = await fetch('https://dummyjson.com/products');
    const data = await response.json();
    filteredRows.value = data.products;
    totalRows.value = data.total;
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};

fetchData();

const filteredData = computed(() => {
  if (!q.value) {
    return filteredRows;
  }

  return filteredRows.filter((student) => {
    return Object.values(student).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase());
    });
  });
});



const rows = computed(() => {
  let filteredProducts = [...sortedRows.value]
  if (q.value) {
    filteredProducts = filteredProducts.filter(product => {
      return Object.values(product).some(value => {
        return String(value).toLowerCase().includes(q.value.toLowerCase())
      })
    })
  }
  const startIndex = (page.value - 1) * pageCount
  const endIndex = startIndex + pageCount
  return filteredProducts.slice(startIndex, endIndex)
});

const sort = ref({ column: 'title', direction: 'asc' as const })
const sortedRows = computed(() => {
  const sortedProducts = [...filteredRows.value]
  const {column, direction} = sort.value

  if (column && direction) {
    sortedProducts.sort((a, b) => {
      const aValue = a[column]
      const bValue = b[column]
      if (aValue < bValue) return direction === 'asc' ? -1 : 1
      if (aValue > bValue) return direction === 'asc' ? 1 : -1
      return 0
    })
  }
  return sortedProducts
});
watch(q,() => {
  page.value = 1;
});

</script>

<template>
  <div>
    <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
      <UInput v-model="q" placeholder="Фільтрувати товари..." />
    </div>
    <UTable :rows="rows" :columns="columns" v-model:sort="sort"
            sort-mode="manual">
      <template #thumbnail-data="{row}">
        <img :src="row.thumbnail" alt="" class="w-[100px] h-[100px]">
      </template>
      <template #description-data="{row}">
        <div class="text-wrap">{{ row.description }}</div>
      </template>
      <!-- Rating cell -->
      <template #rating-data="{row}">
        <div :class="{ 'bg-red-500/40': row.rating < 4.5, 'bg-green-500/40': row.rating >= 4.5 }" class="px-3 py-2">
          {{ row.rating }}
        </div>
      </template>
    </UTable>
    <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="page" :page-count="pageCount" :total="totalRows" />
    </div>
  </div>
</template>
