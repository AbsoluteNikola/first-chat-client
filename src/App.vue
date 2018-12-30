<template>
  <div id="app">
    <nav class="navbar is-success" role="navigation">
      <div class="navbar-brand">
        <div class="navbar-item">
          Cool chat
        </div>
        <div class="navbar-burger burger" data-target="target">
          <span></span>
          <span></span>
          <span></span>
        </div>
      </div>

      <div class="navbar-menu" id="target">
        <div class="navbar-end">
          <div class="navbar-item">
            <div class="field is-horizontal">
              <div class="field-label">
                Nickname
              </div>
              <div class="field-body">
                <input type="text" class="input" id="name" v-model.lazy="name">
              </div>
            </div>
          </div>
          <div class="navbar-item">
            <div class="field is-horizontal">
              <div class="field-label">
                Room
              </div>
              <div class="field-body">
                <input type="text" class="input" id="room" v-model.lazy="room">
              </div>
            </div>
          </div>
          <div class="navbar-item has-text-right">
            <button class="button" @click="register">
              Log in
            </button>
          </div>
        </div>
      </div>
    </nav>
    <div class="section">
      <div class="columns">
        <div class="column is-3"></div>
        <div class="column is-6">
          <div class="box">
            <template v-for="msg in messages">
              <message :name="msg.name" :data="msg.data"></message>
            </template>
          </div>
          <div class="box">
            <div class="field is-grouped">
              <p class="control is-expanded">
                <input class="input" v-model="curMessage" type="text" placeholder="Your message">
              </p>
              <p class="control">
                <button class="button is-success" @click="send" @keyup.enter="send">
                  Send
                </button>
              </p>
            </div>
          </div>
        </div>
        <div class="column is-3"></div>
      </div>
    </div>
  </div>
</template>

<script>
  import Message from './components/Message.vue'

export default {
  name: 'app',
  data() {
    return {
      msg: 'Hello my dear vue',
      name: "",
      registeredName: "",
      room: "",
      curMessage: "",
      messages: [],
      ws: null
    }
  },
  methods: {
    register() {
      console.log(`name: ${this.name}`);
      console.log(`room: ${this.room}`);
      this.registeredName = this.name;
      this.ws = new WebSocket(`ws://${window.location.hostname}:${window.location.port}/chat?name=${this.name}&room=${this.room}`);
      this.ws.onopen = this.ws_onopen;
      this.ws.onclose = this.ws_onclose;
      this.ws.onerror = this.ws_onerror;
      this.ws.onmessage = this.ws_onmessage;
    },
    send() {
      if(!this.curMessage || !this.registeredName) {
        return null;
      }

      let data = {
        name: this.registeredName,
        data: this.curMessage
      };

      this.messages.push(data);
      this.ws.send(JSON.stringify(data));

    },
    ws_onopen() {
      console.log('connected');
    },
    ws_onmessage(event) {
      let data = JSON.parse(event.data);
      this.messages.push(data);
      console.log('new message', data);
    },
    ws_onerror(event) {
      console.error(event.message);
    },
    ws_onclose(event) {
      console.error('connection closed');
    }
  },
  components: {
    message: Message
  }
}
</script>
