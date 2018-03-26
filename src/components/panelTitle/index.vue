<template>
  <div class="panel-title">
    <span v-if="title" v-text="title"></span>
    <div class="fr">
      <slot></slot>
    </div>
  </div>
</template>
<script type="text/javascript">
  export default{
    props: {
      title: {
        type: String
      }
    }
  }
</script>
<style scoped>
    .panel-title{
        display:flex;
        flex-direction: row;
        align-items: center;
        justify-content: center;
    }
</style>
