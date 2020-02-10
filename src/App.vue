<template>
  <div>
    <showcase />
  </div>
</template>

<script>
export default {
  components: {
    showcase: () => import('./components/ImageViewer.vue')
  }
}
</script>

<style lang="scss">
button {
  border: 2px solid rgb(85, 77, 163);
  border-radius: 5px;

  color: rgb(85, 77, 163);
  font-size: 16px;
  text-transform: uppercase;
  font-weight: bold;

  background: transparent;

  padding: 7px 14px;

  cursor: pointer;

  outline: none;

  transition: all .2s ease-in;

  &:hover {
    background-color: rgba(85, 77, 163, .2);
  }
}
</style>