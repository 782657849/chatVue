<template>
  <div class="container-fluid h-100 p-2 ">
    <div class="row justify-content-center h-100">
      <div class="col-md-8 col-xl-6 chat">
        <div class="card">
<!--          //聊天头部-->
          <div class="card-header msg_head">
            <div class="d-flex bd-highlight justify-content-center">
              ChatGpt By Go
            </div>
          </div>
<!--          //消息列表-->
          <div class="card-body msg_card_body" style="white-space: pre-wrap">
            <div
                v-for="message in messages"
                class="d-flex justify-content-start mb-2"
            >
              <div class="msg_container mb-2" style="white-space: pre-wrap">
                {{message}}
                <span class="msg_time"></span>
              </div>
            </div>
          </div>

<!--          //发送消息框和按钮-->
          <div class="card-footer">
            <div class="input-group">
             <input
                 v-model="msg"
                 name=""
                 class="form-control type_msg"
                 placeholder="Type your message..."
                 @keyup.enter.exact="send(this.msg)"
             >
              <button type="button" class="btn btn-primary" @click="send(this.msg)">发送</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>
<script>
import $ from 'jquery/dist/jquery.min'
export default {
  name:'home',
  data(){
    return{
      msg:'',
      messages:[
      ],
      websocket : new WebSocket('ws://localhost:1234')
    }
  },
  methods:{
    send(msg){
      this.messages.push(msg)
      this.websocket.send(msg)
      this.msg = '';
      $(".msg_card_body").scrollTop(99999, 600);
    }
  },
  mounted() {
    $(".msg_card_body").scrollTop(99999, 600);
    let msg = this.messages
    const websocket = this.websocket;
    websocket.onopen = function (evt) {
      console.log("Connected to WebSocket server.");
    };

    websocket.onclose = function (evt) {
      console.log("Disconnected");
    };

    websocket.onmessage = function (evt) {
      setTimeout(()=>{
        msg.push(`来自服务端的消息:`+evt.data)
      },400)

      console.log('从客户端收到消息: ' + evt.data);
    };

    websocket.onerror = function (evt, e) {
      console.log('Error occured: ' + evt.data);
    };

    // websocket.onmessage = function (event) {
    //   console.log(event);
    //   let msg = JSON.parse(event.data)
    //   console.log(msg)
    //   setMessageInnerHTML(msg);
    // }
  },
}
</script>
<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
