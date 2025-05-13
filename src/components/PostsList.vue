<script>
import PostPreview from './PostPreview.vue';
import PostLoader from './PostLoader.vue';

export default {
  props: {
    posts: {
      type: Array,
      required: true,
    },
    loading: {
      type: Boolean,
      default: false,
    },
    selectedPost: {
      required: true,
    },
  },
  components: {
    PostPreview,
    PostLoader,
  },
  emits: ['edit', 'delete', 'add', 'select'],
};
</script>


<template>
  <div class="tile is-parent">
    <div class="tile is-child box is-success">
      <div class="block">
        <div class="block is-flex is-justify-content-space-between">
          <p class="title">Posts</p>
          <button type="button" class="button is-link">Add New Post</button>
        </div>

        <table class="table is-fullwidth is-striped is-hoverable is-narrow" v-if="posts.length">
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
                <button type="button" class="button is-link">Open</button>
              </td>
            </tr>
          </tbody>
        </table>
        <p class="help is-info" v-else>No posts yet</p>
      </div>
    </div>
  </div>
</template>
