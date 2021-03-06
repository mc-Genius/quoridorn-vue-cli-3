<template>
  <WindowFrame titleText="BGM追加画面" display-property="private.display.addBGMWindow" align="right-bottom" fixSize="300, 350" @open="initWindow">
    <div class="contents">
      <fieldset>
        <legend>読込</legend>
        <!-- URL -->
        <label class="url"><span>URL</span>
          <input type="text" v-model="url" ref="urlElm">
        </label>
      </fieldset>
      <fieldset>
        <legend>表示</legend>
        <div class="firstWide">
          <!-- 表示タイトル -->
          <label class="titleStr"><span>タイトル</span><input type="text" v-model="title"></label>
        </div>
        <div class="firstWide" v-if="!isYoutube">
          <!-- クレジットURL -->
          <label class="creditUrl"><span>CreditURL</span><input type="text" v-model="creditUrl"></label>
          <!-- クレジット取得 -->
          <button class="getCredit" @click="getCredit">取得</button>
        </div>
      </fieldset>
      <fieldset>
        <legend>再生</legend>
        <div>
          <!-- タグ -->
          <label class="tag"><span title="再生中のBGMはタグによって一意になります">タグ</span><input type="text" v-model="tag" list="bgmTagComboboxValues"></label>
          <datalist id="bgmTagComboboxValues">
            <option v-for="tag in tags" :key="tag" :value="tag">{{tag}}</option>
          </datalist>
          <!-- 音量 -->
          <VolumeComponent
            :initVolume="volume"
            @volume="setVolume"
            @mute="setIsMute"
            :mutable="false"
            ref="volumeComponent"/>
          <!-- プレビュー -->
          <button class="preview" @click="preview">プレビュー</button>
        </div>
        <div>
          <!-- 再生時間 -->
          <label class="playLength"><span title="0で全て再生">時間</span><input type="number" min="0" step="0.1" max="10000" v-model="playLength"></label>
          <!-- フェードイン -->
          <label class="fadeIn"><span>fadeIn</span><input type="number" min="0" step="0.1" max="10" v-model="fadeIn" placeholder="秒"></label>
          <!-- フェードアウト -->
          <label class="fadeOut"><span>fadeOut</span><input type="number" min="0" step="0.1" max="10" v-model="fadeOut" placeholder="秒"></label>
          <!-- 無限ループ -->
          <span class="icon loop" :class="{active: isLoop}"><i class="icon-loop" @click="change('isLoop')" :title="'リピート再生' + (isLoop ? 'あり' : 'なし')"></i></span>
        </div>
      </fieldset>
      <fieldset>
        <legend>チャット連動</legend>
        <div class="lastWide">
          <!-- チャット連動オプション -->
          <label class="option"><select v-model="chatLinkage">
            <option v-for="opt in options" :value="opt.value" :key="opt.value">{{opt.label}}</option>
          </select></label>
          <!-- 検索文字 -->
          <label
            class="search"
            v-show="chatLinkage === 1 || chatLinkage === 2">
            <input :placeholder="chatLinkage === 1 ? '' : 'Javascriptの正規表現'" type="text" v-model="chatLinkageSearch"></label>
        </div>
      </fieldset>
      <div class="buttonArea">
        <div>
          <button @click="commit">確定</button>
          <button @click="cancel">キャンセル</button>
        </div>
      </div>
    </div>
  </WindowFrame>
</template>

<script lang="ts">
import WindowFrame from "../WindowFrame.vue";
import WindowMixin from "../WindowMixin.vue";
import VolumeComponent from "./component/VolumeComponent.vue";

import { Component, Vue, Watch } from "vue-property-decorator";
import { Action, Getter } from "vuex-class";

@Component<AddBGMWindow>({
  name: "addBGMWindow",
  mixins: [WindowMixin],
  components: {
    WindowFrame,
    VolumeComponent
  }
})
export default class AddBGMWindow extends Vue {
  @Action("windowClose") windowClose: any;
  @Action("windowOpen") windowOpen: any;
  @Action("addBGM") addBGM: any;
  @Getter("bgmList") bgmList: any;
  @Getter("playerKey") playerKey: any;

  private isYoutube: boolean = false;
  private url: string = "";
  private title: string = "";
  private creditUrl: string = "";
  private tag: string = "";
  private isLoop: boolean = false;
  private fadeIn: number = 0;
  private fadeOut: number = 0;
  private start: number = 0;
  private end: number = 0;
  private playLength: number = 0;
  private isMute: boolean = false;
  private volume: number = 0.8;
  private options: any[] = [
    { value: 0, label: "なし" },
    { value: 1, label: "末尾文字" },
    { value: 2, label: "正規表現" }
  ];
  private tags: string[] = ["BGM", "SE"];
  private chatLinkage: number = 0;
  private chatLinkageSearch: string = "";

  initWindow(this: any): void {
    this.isYoutube = false;
    this.url = "";
    this.title = "";
    this.creditUrl = "";
    this.tag = "";
    this.isLoop = false;
    this.fadeIn = 0;
    this.fadeOut = 0;
    this.start = 0;
    this.end = 0;
    this.playLength = 0;
    this.isMute = false;
    this.volume = 0.8;
    this.chatLinkage = 0;
    this.chatLinkageSearch = "";

    const urlElm: HTMLElement = this.$refs.urlElm;
    setTimeout(() => urlElm.focus(), 0);
  }
  commit(): void {
    const bgmObj = {
      url: this.url,
      title: this.title,
      creditUrl: this.creditUrl,
      tag: this.tag,
      isLoop: this.isLoop,
      fadeIn: Math.floor(this.fadeIn * 10) / 10,
      fadeOut: Math.floor(this.fadeOut * 10) / 10,
      playLength: Math.floor(this.playLength * 10) / 10,
      isMute: this.isMute,
      volume: this.volume,
      chatLinkage: this.chatLinkage,
      chatLinkageSearch: this.chatLinkageSearch,
      owner: this.playerKey
    };
    this.addBGM(bgmObj);

    this.windowClose("private.display.addBGMWindow");
  }
  cancel(): void {
    this.windowClose("private.display.addBGMWindow");
  }
  getCredit(): void {
    this.creditUrl = this.url.replace(/^(https?:\/\/[^/]+).+$/, "$1");
  }
  preview() {
    alert("未実装の機能です");
  }
  change(this: any, param: string): void {
    this[param] = !this[param];
  }
  setIsMute(isMute: boolean): void {
    this.isMute = isMute;
  }
  setVolume(volume: string): void {
    this.volume = Math.floor(parseFloat(volume) * 100) / 100;
  }

  @Watch("url")
  onChangeUrl(url: string): void {
    this.isYoutube = /www\.youtube\.com/.test(url);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.contents {
  position: absolute;
  height: 100%;
  width: 100%;
  font-size: 12px;
  display: flex;
  flex-direction: column;
  overflow-y: auto;
}
fieldset {
  padding-left: 5px;
  padding-right: 5px;
  padding-bottom: 5px;
}
fieldset:not(:last-child) {
  margin-bottom: 5px;
}
fieldset > div {
  display: flex;
  justify-content: left;
  align-content: center;
}
fieldset > div:not(:last-child) {
  margin-bottom: 5px;
}
.icon {
  display: flex;
}
.icon i {
  position: relative;
  color: black;
  border-radius: 50%;
  font-size: 12px;
  width: 1.4em;
  height: 1.4em;
  border: 2px solid black;
  display: flex;
  justify-content: center;
  align-content: start;
}
.icon i:before {
  display: flex;
  justify-content: center;
  align-content: start;
  position: absolute;
  top: 50%;
  margin-top: calc(-12px / 2);
}
.icon:not(.active) i:hover {
  background-color: lightyellow;
}
.icon.active.loop i {
  font-weight: bold;
  background-color: deepskyblue;
}
.icon.active.fadeIn i {
  font-weight: bold;
  background-color: deepskyblue;
}
.icon.active.fadeOut i {
  font-weight: bold;
  background-color: deepskyblue;
}

fieldset > div i:not(:first-child) {
  margin-left: 7px;
}
.firstWide > :first-child {
  flex: 1;
}
.lastWide > :last-child {
  flex: 1;
}
input[type="text"] {
  width: 50px;
}
legend,
label,
button {
  font-size: 10px;
  user-select: none;
  -ms-user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
}
select,
input[type="text"],
input[type="number"] {
  font-size: 11px;
}
label {
  display: flex;
  justify-content: left;
  align-content: center;
  vertical-align: middle;
  white-space: nowrap;
  padding: auto;
}
label span {
  display: flex;
  justify-content: center;
  align-content: center;
  margin: auto;
}
label input,
label .mask {
  flex: 1;
  padding: 2.5px 0;
}
label .mask {
  border: 1px solid black;
  background-color: lightgray;
  padding: 2px 0;
}
.volumeComponent {
  flex: 1;
}
.tag input {
  width: 52px;
}
.playLength,
.fadeIn,
.fadeOut {
  flex: 1;
  margin-right: 5px;
}
.playLength input {
  width: 45px;
}
.fadeIn input,
.fadeOut input {
  width: 32px;
}
.regexp input {
  width: 50px;
}
.buttonArea {
  display: flex;
  justify-content: center;
  align-content: start;
}
</style>
