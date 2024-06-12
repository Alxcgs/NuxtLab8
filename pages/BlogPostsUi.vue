<template>
  <div>
    <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
      <UInput v-model="searchQuery" placeholder="Фільтрувати товари..." />
    </div>
    <UTable :rows="paginatedRows" :columns="columns" v-model:sort="sort" sort-mode="manual">
      <template #thumbnail-data="{ row }">
        <img :src="row.thumbnail" alt="" class="w-[100px] h-[100px]" />
      </template>
      <template #description-data="{ row }">
        <div class="text-wrap">{{ row.description }}</div>
      </template>
      <template #rating-data="{ row }">
        <div :class="{ 'bg-red-500/40': row.rating < 4.5, 'bg-green-500/40': row.rating >= 4.5 }" class="px-3 py-2">
          {{ row.rating }}
        </div>
      </template>
    </UTable>
    <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="currentPage" :page-count="pageCount" :total="totalRows" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue';
import { UInput, UTable, UPagination } from '@nuxt/ui';

const searchQuery = ref('');
const currentPage = ref(1);
const itemsPerPage = 10;
const totalRows = ref(0);
const rows = ref([]);
const sort = ref({ column: 'title', direction: 'asc' });

const columns = [
  { key: 'id', label: '#', sortable: true },
  { key: 'user.name', label: 'Автор', sortable: true },
  { key: 'category.title', label: 'Категорія', sortable: true },
  { key: 'title', label: 'Заголовок', sortable: true },
  { key: 'published_at', label: 'Дата публікації', sortable: true }
];

const fetchPosts = async () => {
  try {
    const response = await fetch('/api/blog/posts');
    const data = await response.json();
    rows.value = data;
    totalRows.value = data.length;
  } catch (error) {
    console.error('Error fetching posts:', error);
  }
};

fetchPosts();

const filteredRows = computed(() => {
  if (!searchQuery.value) {
    return rows.value;
  }
  return rows.value.filter((post) =>
    Object.values(post).some((value) =>
      String(value).toLowerCase().includes(searchQuery.value.toLowerCase())
    )
  );
});

const paginatedRows = computed(() => {
    const start = (currentPage.value - 1) * itemsPerPage;
    const end = start + itemsPerPage;
    return filteredRows.value.slice(start, end);
});

watch(searchQuery, () => {
    currentPage.value = 1;
});
</script>
