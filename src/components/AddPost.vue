<script setup>
import InputField from './InputField.vue';
import TextAreaField from './TextAreaField.vue';

const emit = defineEmits(['cancel', 'onAdd']);

const handleFormSubmit = () => {

}
</script>

<template>
  <div class="content">
    <h2>title</h2>

    <form @submit.prevent="handleFormSubmit">
      <InputField />
      <InputField />
      <TextAreaField />

      <div class="field is-grouped">
        <div class="control">
          <button type="submit" class="button is-link">Save</button>
        </div>
        <div class="control">
          <button type="reset" class="button is-link is-light" @click.prevent="$emit('cancel')">Cancel</button>
        </div>
      </div>
    </form>
  </div>
</template>