<template>
  <input type="number"
    @change="event => changeProperty(event.target.value)"
    :value="storePropertyValue"
    @dblclick.stop>
</template>

<script>
import { mapState, mapActions, mapGetters } from "vuex";

export default {
  name: "propNumber",
  props: {
    baseProperty: { type: String, required: true },
    objKey: { type: String, required: true },
    property: { type: String, required: true }
  },
  methods: {
    ...mapActions(["setProperty"]),
    changeProperty(value) {
      const useProperty = this.useProperty;
      if (!useProperty) return;
      this.setProperty({
        property: useProperty,
        value: value,
        isNotice: true,
        logOff: true
      });
    }
  },
  computed: mapState({
    ...mapGetters(["getStateValue"]),
    useProperty() {
      const targetList = this.getStateValue(this.baseProperty);
      const index = targetList.findIndex(obj => obj.key === this.objKey);
      if (index === -1) return;
      return `${this.baseProperty}.${index}.${this.property}`;
    },
    storePropertyValue() {
      const useProperty = this.useProperty;
      if (!useProperty) return false;
      return this.getStateValue(useProperty);
    }
  })
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
input {
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  border-width: 1px;
  border-style: solid;
  border-color: gray;
  padding: 0;
  /* border: none; */
  background-color: transparent;
}
</style>
