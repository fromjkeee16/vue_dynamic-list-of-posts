<script>
import Header from './components/header.vue';
import Login from './components/login.vue';
import PostsList from './components/postsList.vue';
import Loader from './components/loader/index.vue';
import PostLoader from './components/postLoader.vue';
import { getUserByEmail } from './helpers/users';
import { getPosts } from './helpers/posts';

export default {
  components: {
    Header,
    Login,
    PostsList,
    PostLoader,
    Loader
  },
  data() {
    return {
      user: null,
      posts: [],
      errorMessage: '',
      isUserLoading: true,
      arePostsLoading: false,
    }
  },
  watch: {
    user: {
      async handler() {
        localStorage.setItem('user', JSON.stringify(this.user));

        if (this.user) {
          try {
            this.arePostsLoading = true;
            this.posts = await getPosts(this.user.id);
          } catch (e) {
            this.errorMessage = e.message;
          } finally {
            this.arePostsLoading = false;
          }
        } else {
          this.handleLogout();
        }
      }
    }
  },
  methods: {
    handleLogout() {
      this.posts = [];
      this.user = null;
    },
    userLogin(newUser) {
      this.user = newUser;
    },
    async fetchUserFromLocalStorage() {
      this.isUserLoading = true;

      const usr = localStorage.getItem('user');

      if (!usr) {
        return;
      }

      const parsed = JSON.parse(usr);

      try {
        const user = await getUserByEmail(parsed.email);
        if (!user) {
          localStorage.removeItem('user');

          return;
        }

        this.userLogin(user);
      } catch (e) {
        this.errorMessage = e.message;
      } finally {
        this.isUserLoading = false;
      }
    }
  },
  async mounted() {
    await this.fetchUserFromLocalStorage();
  }
}
</script>

<template>
  <div class="page">
    <div class="page__loader" v-if="isUserLoading">
      <Loader />
    </div>
    <template v-else>
      <Login v-if="!user" @addUser="userLogin" />


      <template v-else>
        <Header :user="user" @logout="handleLogout" />

        <Loader v-if="arePostsLoading" />
        <PostsList :posts="posts" v-else v-if="posts.length" />
      </template>
    </template>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
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
