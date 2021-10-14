<template>
  <input
    type="text"
    :value="newTitle"
    @input="$emit('update:newTitle', $event.target.value)"
    @keypress.enter="$emit('save-edit')"
    @keyup.27= "$emit('toggle-edit')"
  >
</template>

<script>
export default {
  props: {
    newTitle: String
  }
}

</script>
