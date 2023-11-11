<script setup lang="ts">
import { ref } from 'vue';
import { RouterLink, RouterView } from 'vue-router';

const target = ref("");
const history = ref([
  {
    name: "名前",
    content: "表示のサンプル",
    time: `${new Date().getHours()}:${new Date().getMinutes()}`
  }
]) as any[];
let isOb = false;
let latest = "";

function start() {
  if (isOb) {
    alert("既に監視を開始しています。");
    return;
  }

  let ticket = "";

  try {
    new URL(target.value);
  } catch (_e) {
    alert("URLが不正です。");
    return;
  }

  ticket = new URL(target.value).pathname.split("/").pop() ?? "";

  history.value = [];

  const interval = setInterval(async () => {
    const resp = await fetch("https://lookup-msg-oc-amex2189.edame8080.workers.dev/" + ticket + "?key=" + Math.random().toString(16).slice(-4));
    const data = await resp.json();
    if (window.btoa(encodeURIComponent(data.name + data.content)) === latest) {
      return;
    }

    console.log(history.value);

    history.value.push({
      name: data.name,
      content: data.content,
      time: `${new Date().getHours()}:${new Date().getMinutes()}`
    });

    latest = window.btoa(encodeURIComponent(data.name + data.content));
  }, 666);

  isOb = true;
}
</script>

<template>
  <div id="root">
    <h1>OpenChat Observer</h1><br />
    <input type="text" v-model="target" placeholder=".../ti/g2/~~~" />
    <button @click="start">START</button>
    <div v-for="h in history" :key="h.time">
      {{`${h.time}  ${h.name} ${h.content}`}}
    </div><br />
    <p>by @amex2189 / ame_x</p>
    <RouterView />
  </div>
</template>

<style scoped>
#root {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 80vw;
}

input, button {
  font-size: 1.05rem;
}

input {
  border-radius: 5px 0 0 5px;
  background: #000;
  font-weight: bold;
  color: #fff;
}

button {
  border-radius: 0 5px 5px 0;
  background: #000;
  font-weight: bold;
  color: #fff;
}
</style>