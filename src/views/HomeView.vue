<template>
  <div>
    <h1>PeerJS</h1>
    <input type="text" v-model="info.localPeerID" readonly />
    <input type="text" v-model="info.remotePeerID" />

    <video id="localVideo"></video>
    <video id="remoteVideo"></video>

    <textarea name="" v-model="info.text" cols="30" rows="10"></textarea>

    <!-- New messages -->
    <div v-for="message in info.messages" :key="message.id">
      <p>{{ message.text }}</p>
    </div>

    <button @click="connect">Connect</button>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import Peer from "peerjs";
const peer = new Peer();

type infoType = {
  localPeerID: string;
  remotePeerID: string;
  text: string;
  messages: any[];
};

const info = reactive<infoType>({
  localPeerID: "",
  remotePeerID: "",
  text: "",
  messages: [],
});

peer.on("open", function (id: string) {
  info.localPeerID = id;
});

peer.on("connection", function (conn: any) {
  conn.on("data", function (data: any) {
    console.log("Received", data);
    info.messages.push({ text: data });
  });
});

const connect = () => {
  var conn = peer.connect(info.remotePeerID);
  conn.on("open", function () {
    conn.send(info.text);
    info.text = "";
  });
};
</script>
