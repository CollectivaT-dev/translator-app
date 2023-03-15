<template>
  <div class="header">
    <img src="@/assets/logo.png">
    <div class="header-disclaimer" v-t="'header'">

    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
export default defineComponent({
  name: "WidgetHeader",
})
</script>

<style scoped>
img{
  height: 100px;
  margin: 0rem auto;
}
.header-disclaimer{
  margin-bottom: 2rem;
  font-size: 2em;
}
</style>