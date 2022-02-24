## webrtc 介绍
https://webrtc.org/getting-started/media-devices

https://vnzmi.com/2018/11/14/get-start-with-webrtc-peer-connection/


## 示例-clienA
```
const signaling = new SignalingChannel(); //第一步 创建信令连接（用户交换webRtc额外数据，操作），可以是websoket等通信
const constraints = {audio: true, video: true}; //生成获取媒体流配置
const configuration = {iceServers: [{urls: 'stuns:stun.example.org'}]}; //webtrc连接配置
const pc = new RTCPeerConnection(configuration); //第二部 生成webrtc对象

// send any ice candidates to the other peer
pc.onicecandidate = ({candidate}) => signaling.send({candidate}); //webrtc监听stun服务生成了ice候选（表示clienA的webrtc已准备好用于连接远端的ice）

// let the "negotiationneeded" event trigger offer generation 会话协商的更改时会触发此事件
pc.onnegotiationneeded = async () => {// 第一次连接会不会触发此事件
  try {
    await pc.setLocalDescription(await pc.createOffer()); //第三步 创建会发描述（sdp）并设置为本地描述
    // send the offer to the other peer
    signaling.send({desc: pc.localDescription}); //通过信令把会话描述发送给远端
  } catch (err) {
    console.error(err);
  }
};

// once remote track media arrives, show it in remote video element
pc.ontrack = (event) => { // 监听远端添加的通道 获取流数据
  // don't set srcObject again if it is already set.
  if (remoteView.srcObject) return;
  remoteView.srcObject = event.streams[0];
};

// call start() to initiate
async function start() {
  try {
    // get local stream, show it in self-view and add it to be sent
    const stream =
      await navigator.mediaDevices.getUserMedia(constraints);
    stream.getTracks().forEach((track) =>
      pc.addTrack(track, stream));
    selfView.srcObject = stream;
  } catch (err) {
    console.error(err);
  }
}

signaling.onmessage = async ({desc, candidate}) => { //信令消息处理
  try {    
    if (desc) {
      // if we get an offer, we need to reply with an answer
      if (desc.type === 'offer') {//如果是接收方
        await pc.setRemoteDescription(desc); //第四步 通过信令服务接收到来自远端的sdp应答，并设置为webrtc的远端会话sdp
        const stream =
          await navigator.mediaDevices.getUserMedia(constraints);
        stream.getTracks().forEach((track) =>
          pc.addTrack(track, stream));// 添加流通道
        await pc.setLocalDescription(await pc.createAnswer());
        signaling.send({desc: pc.localDescription});
      } else if (desc.type === 'answer') {//如果是触发放
        await pc.setRemoteDescription(desc);//第四步 通过信令服务接收到来自远端的sdp应答，并设置为webrtc的远端会话sdp
      } else {
        console.log('Unsupported SDP type.');
      }
    } else if (candidate) {
      await pc.addIceCandidate(candidate); //第五步 通过信令接收到远端ice候选添加的当前webrtc候选
    }
  } catch (err) {
    console.error(err);
  }
};
```
