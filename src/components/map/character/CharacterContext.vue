<template>
  <ContextFrame displayProperty="private.display.characterContext">
    <div class="item" @click.left.prevent="viewEditCharacter">変更</div>
    <hr>
    <div class="item" v-if="place !== 'field'" @click.left.prevent="moveToField">マップに移動</div>
    <div class="item" v-if="place !== 'waiting'" @click.left.prevent="moveToWaitRoom">キャラクター待合室に移動</div>
    <div class="item" v-if="place !== 'graveyard'" @click.left.prevent="moveToGraveyard">墓場に移動（削除）</div>
    <hr>
    <div class="item" @click.left.prevent="copyCharacter">複製</div>
    <template v-if="characterContextObjKey !== null && getObj(characterContextObjKey).url">
      <hr>
      <div class="item" @click.left.prevent="openRefURL">データ参照先URLを開く</div>
    </template>
  </ContextFrame>
</template>

<script lang="ts">
import ContextFrame from "../../ContextFrame.vue";
import WindowMixin from "../../WindowMixin.vue";

import { Component, Vue } from "vue-property-decorator";
import { Action, Getter } from "vuex-class";

@Component<CharacterContext>({
  name: "characterContext",
  mixins: [WindowMixin],
  components: {
    ContextFrame
  }
})
export default class CharacterContext extends Vue {
  @Action("windowOpen") windowOpen: any;
  @Action("setProperty") setProperty: any;
  @Action("changeListInfo") changeListInfo: any;
  @Action("windowClose") windowClose: any;
  @Getter("getObj") getObj: any;
  @Getter("characterContextObjKey") characterContextObjKey: any;
  @Getter("playerKey") playerKey: any;
  @Getter("mapMaskIsLock") mapMaskIsLock: any;

  viewEditCharacter(): void {
    window.console.log(
      `  [methods] select context => item: Character(${
        this.characterContextObjKey
      }).viewEditCharacter`
    );
    this.setProperty({
      property: "private.display.editCharacterWindow.key",
      value: this.characterContextObjKey
    });
    this.windowOpen("private.display.editCharacterWindow");
    this.windowClose("private.display.characterContext");
  }
  moveToField(): void {
    this.moveTo("field");
  }
  moveToWaitRoom(): void {
    this.moveTo("waiting");
  }
  moveToGraveyard(): void {
    this.moveTo("graveyard");
  }
  private moveTo(place: string): void {
    this.changeListInfo({
      key: this.characterContextObjKey,
      place: place,
      isNotice: true
    });
    this.windowClose("private.display.characterContext");
  }
  copyCharacter(): void {
    window.console.log(
      `  [methods] select context => item: Character(${
        this.characterContextObjKey
      }).copyCharacter`
    );
    this.windowClose("private.display.characterContext");
    alert("未実装の機能です。");
  }
  openRefURL(): void {
    window.open(this.getObj(this.characterContextObjKey).url, "_blank");
    this.windowClose("private.display.characterContext");
  }
  get place(): string {
    const character = this.getObj(this.characterContextObjKey);
    return character ? character.place : null;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
