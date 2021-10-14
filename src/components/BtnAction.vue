<template>
  <button
    class="btn-action w-100"
    @click= "$emit(`${evtName}`)"
  >{{btnName}}</button>
</template>

<script>
export default {
  props: {
    btnName: String,
    evtName: String
  }
}

</script>
