<template>
  <textarea
    :value="newDescription" @input="updateDescription"
    @keyup.27= "$emit('toggle-edit')"
  ></textarea>
</template>

<script>
export default {
  props: {
    newDescription: String
  },
  methods: {
    updateDescription (event) {
      // console.log('updateDescription')
      this.$emit('update:newDescription', event.target.value)
      const textarea = event.target
      textarea.style.height = 'auto'
      textarea.style.height = textarea.scrollHeight + 'px'
      // textarea.style.overflowY = 'hidden'
    }
  }
}

</script>
