<template>
  <WindowFrame titleText="バージョン" display-property="private.display.versionWindow" align="center" fixSize="300, 158">
    <div class="contents">
      <div class="logo-container">
        <Logo></Logo>
      </div>
      <div class="footer">
        <span>Quoridorn Ver.{{version}}</span>
        <button @click="cancel">閉じる</button>
      </div>
    </div>
  </WindowFrame>
</template>

<script lang="ts">
import WindowFrame from "../WindowFrame.vue";
import WindowMixin from "../WindowMixin.vue";
import Logo from "./Logo.vue";

import { Component, Vue } from "vue-property-decorator";
import { Action, Getter } from "vuex-class";

@Component<VersionWindow>({
  name: "versionWindow",
  mixins: [WindowMixin],
  components: {
    WindowFrame,
    Logo
  }
})
export default class VersionWindow extends Vue {
  @Action("windowClose") windowClose: any;
  @Getter("version") version: any;

  cancel(): void {
    this.windowClose("private.display.versionWindow");
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.contents {
  position: absolute;
  height: 100%;
  width: 100%;
  font-size: 12px;
}
.logo-container {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1.3em;
  font-size: 16px;
  pointer-events: none;
  user-select: none;
  -ms-user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
}
.footer {
  display: flex;
  justify-content: space-between;
}
</style>
