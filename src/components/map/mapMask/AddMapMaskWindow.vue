<template>
  <WindowFrame titleText="マスク作成" display-property="private.display.addMapMaskWindow" align="center" fixSize="285, 195">
    <table>
      <tbody>
        <tr>
          <th>文字：</th>
          <td><input type="text" v-model="name"></td>
          <td class="mapMaskGrid" rowspan="6">
            <div :style="mapMaskStyle" @dragstart="dragStart"
                 @mousedown.stop="windowActive({ property: 'private.display.addMapMaskWindow', isClose: false })"
                 class="mapMask" draggable="true">{{name}}
            </div>
          </td>
        </tr>
        <tr>
          <th>色：</th>
          <td><input type="color" v-model="color"></td>
        </tr>
        <tr>
          <th>高さ：</th>
          <td><input type="number" min="1" v-model="height"></td>
        </tr>
        <tr>
          <th>幅：</th>
          <td><input type="number" min="1" v-model="width"></td>
        </tr>
        <tr>
          <th>透明度：</th>
          <td><input type="range" v-model="transparency"></td>
        </tr>
        <tr>
          <td colspan="2" class="multi"><label><input type="checkbox" v-model="isMulti">複数作成</label></td>
        </tr>
      </tbody>
    </table>
  </WindowFrame>
</template>

<script>
import { mapActions, mapGetters, mapState } from "vuex";
import WindowFrame from "../../WindowFrame";
import WindowMixin from "../../WindowMixin";

export default {
  name: "addMapMaskWindow",
  mixins: [WindowMixin],
  components: {
    WindowFrame
  },
  data() {
    return {
      transparency: 0,
      width: 1,
      height: 1,
      name: "",
      isMulti: true,
      color: "#000000"
    };
  },
  methods: {
    ...mapActions(["windowActive"]),
    dragStart(event) {
      event.dataTransfer.setData("kind", "mapMask");
      event.dataTransfer.setData("name", this.name);
      event.dataTransfer.setData("color", this.rgba);
      event.dataTransfer.setData("fontColor", this.fontColor);
      event.dataTransfer.setData("columns", this.width);
      event.dataTransfer.setData("rows", this.height);
      window.console.log(
        `  [methods] drag start mapMask => {name:"${this.name}", color:${
          this.color
        }, size:(${this.width}, ${this.height}), transparency:${
          this.transparency
        }`
      );
    }
  },
  computed: mapState({
    ...mapGetters(["parseColor"]),
    mapMaskStyle() {
      let width = this.width * this.gridSize;
      let height = this.height * this.gridSize;
      let zoom = 1;
      if (Math.max(width, height) > 160) {
        zoom = 160 / Math.max(width, height);
        width *= zoom;
        height *= zoom;
      }
      let result = {
        width: width + "px",
        height: height + "px",
        "background-color": this.rgba,
        color: this.fontColor
      };
      return result;
    },
    rgba() {
      const colorObj = this.parseColor(this.color);
      colorObj.a = (100 - this.transparency) / 100;
      return colorObj.getRGBA();
    },
    fontColor() {
      return this.parseColor(this.color).getColorCodeReverse();
    },
    gridSize: state => state.public.map.grid.size
  })
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
table {
  font-size: 12px;
  white-space: nowrap;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  border-collapse: collapse;
}
th,
td {
  padding: 0;
  border: none;
}
th {
  text-align: right;
  font-weight: normal;
}
td.multi {
  text-align: center;
}
td.mapMaskGrid {
  width: 161px;
  height: 161px;
  max-width: 161px;
  max-height: 161px;
  text-align: center;
  border: none;
}
.mapMask {
  max-width: 157px;
  max-height: 157px;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 12px;
  border: solid yellow 2px;
}
input[type="number"] {
  width: 46px;
}
input[type="text"] {
  width: 60px;
}
input[type="range"] {
  width: 60px;
}
</style>
