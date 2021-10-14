<template>
  <label>
    <input
      type="checkbox"
      @change="$emit(`${evtName}`)"
      :disabled="editingEnabled"
      :checked="status"
    >
    <span class="custom-checkbox"></span>
    {{labelContent}}
  </label>
</template>

<script>
export default {
  props: {
    labelContent: String,
    evtName: String,
    editingEnabled: Boolean,
    status: Boolean
  }
}

</script>
