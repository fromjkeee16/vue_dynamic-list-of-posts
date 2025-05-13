<script setup>
import { defineProps, onMounted, ref } from 'vue';
import Loader from './loader/index.vue';
import ErrorMessage from './ErrorMessage.vue';
import { useFetch } from '@/composables/useFetch';
import { deletePost, getPosts } from '@/helpers/posts';
import Sidebar from './sidebar/index.vue';
import AddPost from './AddPost.vue';
import PostPreview from './PostPreview.vue';


const selectedPost = ref(null);
const sidebarOpen = ref(false);

const handleSidebar = (newVal) => {
  sidebarOpen.value = newVal;
  selectedPost.value = null;
}

const props = defineProps({
  user: {
    required: true,
  }
});

const fetchPosts = async () => {
  if (!props.user.id) return [];
  return await getPosts(props.user.id);
};

const {
  data: posts,
  isLoading: arePostsLoading,
  errorMessage: postsError,
  execute: loadPosts,
} = useFetch(fetchPosts, []);

onMounted(() => {
  loadPosts();
});

const handlePostDelete = async (post) => {
  const index = posts.value.indexOf(post);

  if (index !== -1) {

    try {
      if (selectedPost.value === post) {
        selectedPost.value = null;
      }

      await deletePost(post.id);
      posts.value.splice(index, 1);
    }
    catch (e) { }
  }
}
</script>

<template>
  <div class="tile is-parent">
    <div class="tile is-child box is-success">
      <div class="block">
        <div class="block is-flex is-justify-content-space-between">
          <p class="title">Posts</p>
          <button type="button" class="button" :class="{ 'is-link': !sidebarOpen }" @click="handleSidebar(true)">
            Add New Post
          </button>
        </div>

        <Loader v-if="arePostsLoading" />

        <table class="table is-fullwidth is-striped is-hoverable is-narrow" v-if="!arePostsLoading && posts.length">
          <thead>
            <tr class="has-background-link-light">
              <th>ID</th>
              <th>Title</th>
              <th class="has-text-right">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="post in posts" :key="post.id">
              <td>{{ post.id }}</td>
              <td>{{ post.title }}</td>
              <td class="has-text-right is-vcentered">

                <button v-if="selectedPost !== post" type="button" class="button" @click="selectedPost = post">
                  Open
                </button>
                <button v-else type="button" class="button is-link" @click="selectedPost = null">
                  Close
                </button>
              </td>
            </tr>
          </tbody>
        </table>

        <p class="help is-info" v-else-if="!arePostsLoading && posts.length === 0">No posts yet</p>

        <ErrorMessage v-if="postsError">{{ postsError }}</ErrorMessage>
      </div>
    </div>
  </div>

  <Sidebar :isOpen="sidebarOpen || selectedPost">
    <PostPreview v-if="selectedPost" :post="selectedPost" @delete="handlePostDelete" />
    <AddPost v-else @cancel="handleSidebar(false)" @onAdd="posts.push($event)" />
  </Sidebar>
</template>