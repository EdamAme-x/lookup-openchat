<script setup lang="ts">
import { doesNotThrow } from 'assert';
import { ref } from 'vue';
import { RouterLink, RouterView } from 'vue-router';
import * as Swal from "sweetalert2";

const target = ref("");
const history = ref([
  {
    name: "名前",
    content: "表示のサンプル こんにちは",
    time: `${new Date().getHours().toString().padStart(2, '0')}:${new Date().getMinutes().toString().padStart(2, '0')}`
  }
]);
let isOb = false;
let latest = "";

function start() {
  if (isOb) {
    alert("既に監視を開始しています。");
    return;
  }

  let ticket = "";

  try {
    new URL(target.value.split(" ").pop() as string);
  } catch (_e) {
    alert("URLが不正です。");
    return;
  }

  ticket = new URL(target.value.split(" ").pop() as string).pathname.split("/").pop() ?? "";

  history.value = [];

  const interval = setInterval(async () => {
    const resp = await fetch("https://lookup-msg-oc-amex2189.edame8080.workers.dev/" + ticket + "?key=" + Math.random().toString(16).slice(-4));
    const data = await resp.json();
    if (window.btoa(encodeURIComponent(data.name + data.content)) === latest) {
      return;
    }

    if (resp.status === 500) {
      alert("重大なエラーが発生しました。")
      window.location.reload()
    }

    console.log(history.value);

    history.value.push({
      name: data.name,
      content: data.content,
      time: `${new Date().getHours().toString().padStart(2, '0')}:${new Date().getMinutes().toString().padStart(2, '0')}`
    });

    latest = window.btoa(encodeURIComponent(data.name + data.content));
  }, 600);

  isOb = true;
}

function down(event: Event) {
  const element = document.getElementById('chats') as Element; 
  element.scrollTop = element.scrollHeight;
}

function exporter(event: Event) {
  let result = "";;
  history.value.forEach(c => {
    result += ` ${c.name} ${c.time} ${c.content} \n`
    result += `--------------------------------- \n`
  })

  console.log(result)
  Swal.fire({
  title: "<strong>LOG EXPORT</strong>",
  icon: "info",
  html: `
  <div class="r">
    <textarea id="target">${result}</textarea>
    <button onclick="copyTargetValue()">Copy</button>
  </div>
  `,
});
}

function support(event: Event) {
  Swal.fire({
    title: "説明書",
    html: `このツールはオプを外部から覗くことが可能です。
またエクスポート等便利な機能も有ります。
詳細は https://honmono.ame-x.net
<br />
開発者: @amex2189 / スポンサー: Calensk`
  })
}
</script>

<template>
  <div id="root">
    <h1>OpenChat Observer</h1><br />
    <input type="text" v-model="target" placeholder=".../ti/g2/~~~" />
    <button @click="start">START</button>
    <div class="chats" id="chats">
    <div v-for="h in history" :key="h.time" class="onchat">
      <div class="chat">
        <div class="name">{{ h.name }}</div>
        <div class="content">
          <div class="message">{{ h.content }}</div>
          <div class="time">{{ h.time }}</div>
        </div>
      </div>
    </div></div><br />
    <div>
      <button @click="down">Down</button>
      <button @click="exporter">Export</button>
      <button @click="support">Support</button>
    </div>
    <p>サポートOC: <a href="https://honmono.ame-x.net">https://honmono.ame-x.net</a></p>
    <p>by <a href="https://twitter.com/amex2189">@amex2189 / ame_x</a></p>
    <RouterView />
  </div>
</template>

<style scoped>

.chats {
  height: 50vh;
  width: 350px;
  display: flex;
  flex-direction: column;
  align-items: left;
  background: #0d0d0d;
  margin: 10px 0;
  padding: 10px 30px;
  border-radius: 5px;
  overflow-y: scroll;
  overflow-x: hidden;
}

.onchat {
  margin-top: 5px;
}

.name {
  width: 150px;
  overflow: hidden;
  text-wrap: nowrap;
  margin-bottom: 5px;
}

.message {
  width: 300px;
  overflow: hidden;
  background-color: #cecece;
  color: #0d0d0d;
  padding: 5px;
  border-radius: 10px;
}

.time {
  display: flex;
  align-items: end;
  padding-left: 10px;
}

.content {
  display: flex;
  width: 290px;
}

/* Root */

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