<script setup>
import { ref, watch, computed, onMounted } from 'vue';
import Header from './components/Header.vue';
import Login from './components/Login.vue';
import PostsList from './components/PostsList.vue';
import Loader from './components/loader/index.vue';
import { getUserByEmail } from './helpers/users';
import { getPosts } from './helpers/posts';
import { useFetch } from './composables/useFetch';
import ErrorMessage from './components/ErrorMessage.vue';

const user = ref(null);
const errorMessage = ref('');
const selectedPost = ref(null);

const userLogin = (newUser) => {
  user.value = newUser;
};

const handleLogout = () => {
  user.value = null;
  localStorage.removeItem('user');
};

const fetchUserFromLocalStorage = async () => {
  const stored = localStorage.getItem('user');
  if (!stored) return;

  const parsed = JSON.parse(stored);

  const result = await getUserByEmail(parsed.email);
  if (result) {
    userLogin(result);
  } else {
    localStorage.removeItem('user');
  }
};

onMounted(fetchUserFromLocalStorage);

const fetchPosts = async () => {
  if (!user.value) return [];
  return await getPosts(user.value.id);
};

const {
  data: posts,
  isLoading: arePostsLoading,
  errorMessage: postsError,
  execute: loadPosts,
} = useFetch(fetchPosts, []);

watch(user, async (val) => {
  if (val) {
    localStorage.setItem('user', JSON.stringify(val));
    await loadPosts();
  } else {
    handleLogout();
  }
});
</script>

<template>
  <div class="page">
    <div class="page__loader" v-if="!user && !errorMessage && !posts.length">
      <Loader />
    </div>

    <template v-else>
      <Login v-if="!user" @addUser="userLogin" />

      <template v-else>
        <Header :user="user" @logout="handleLogout" />

        <Loader v-if="arePostsLoading" />
        <PostsList v-else :posts="posts" @select="selectedPost = $event" :selectedPost="selectedPost" />
      </template>
    </template>

    <ErrorMessage v-if="postsError || errorMessage" :message="postsError || errorMessage" />
  </div>
</template>

<style scoped>
.page {
  display: flex;
  flex-direction: column;
}

.page__loader {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
