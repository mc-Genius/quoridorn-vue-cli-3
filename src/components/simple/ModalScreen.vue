<template>
  <div v-if="isModal"></div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { Getter } from "vuex-class";

@Component({
  name: "modalScreen"
})
export default class ModalScreen extends Vue {
  @Getter("isModal") isModal: any;
}
</script>

<style scoped>
div {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.7);
  z-index: 99999;
}
</style>
