<template>
  <button
    class="btn"
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
