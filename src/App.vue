<template>
  <div id="app" @click="onCallNativeInterface">
    <router-view/>
  </div>
</template>
<script>
export default {
  created() {
    window.getApp = this

    /**
     * 웹뷰 좌우 플리킹하여 컨텐츠 이동 기능 해제
     *
     * @param event
     */
    window.onCallNativeInterfaceFlickingStop = () => {
      var userAgent = navigator.userAgent;

      if (userAgent.match(/(\(iPod|\(iPhone|\(iPad)/)) {
        window.webkit.messageHandlers.iosHandler.postMessage("onWebViewFlickingStop")
      } else if (userAgent.match(/(Android)/)) {
        window.Jsinterface.onwebviewflickingstop()
      }
    }

    /**
     * 웹뷰 좌우 플리킹하여 컨텐츠 이동 기능 활성화
     *
     * @param event
     */
    window.onCallNativeInterfaceFlickingStart = () => {
      var userAgent = navigator.userAgent;

      if (userAgent.match(/(\(iPod|\(iPhone|\(iPad)/)) {
        window.webkit.messageHandlers.iosHandler.postMessage("onWebViewFlickingStart")
      } else if (userAgent.match(/(Android)/)) {
        window.Jsinterface.onwebviewflickingstart()
      }
    }
  },
    methods: {
      /**
     * 웹뷰 터치 했을때 상단/하단을 나오고 숨기는 토글 함수
     *
     * @param event
     */
    onCallNativeInterface(event) {
      const tagNames = [
        "button",
        "input",
        "a",
        "span",
        "label",
        "select",
        "textarea",
        "img"
      ];
      var userAgent = navigator.userAgent;
      if (
        !tagNames.includes(event.target.tagName.toLowerCase()) &&
        event.target.className.indexOf("noApp") < 0 &&
        event.target.className.indexOf("vue-slider") < 0
      ) {
        if (userAgent.match(/(\(iPod|\(iPhone|\(iPad)/)) {
          window.webkit.messageHandlers.iosHandler.postMessage("onWebViewTouch")
        } else if (userAgent.match(/(Android)/)) {
          window.Jsinterface.onwebviewtouch()
        } else {
          console.log("pc", event)
        }
      }
    }
  },
}
</script>