<template>
  <div class="container">
    <div class="flex justify-center">
        <div class="w-full">
            <nav class="navbar bg-gray-100">
                <a href="/admin/blog/posts/create" class="">Додати</a>
            </nav>
            <div class="card">
                <div class="card-body">
                    <table class="table table-auto">
                        <thead>
                            <tr>
                                <th>#</th>
                                <th>Автор</th>
                                <th>Категорія</th>
                                <th>Заголовок</th>
                                <th>Дата публікації</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="post in posts" :key="post.id">
                                <td>{{ post.id }}</td>
                                <td>{{ post.user.name }}</td>
                                <td>{{ post.category.title }}</td>
                                <td><a :href="'/admin/blog/posts/' + post.id + '/edit'">{{ post.title }}</a></td>
                                <td>{{ post.published_at }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const posts = ref([]);

const getPosts = async () => {
try {
    const response = await $fetch('/api/blog/posts');
    posts.value = response;
} catch (error) {
    console.error('Error fetching posts:', error);
}
};

getPosts();
</script>
