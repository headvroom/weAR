<template>
<!-- IE versions < 11 do not support this feature -->
<div class="ar-container">
  <p v-if="!webcamAvailable" class="error-message">Can not play webcam</p>
  <video v-else playsinline="true" autoplay="true" class="ar-element" id="ar-component">
  
  </video>
</div>
</template>

<script>
'use strict'

export default {
  name: 'wear-arcabin',
  props: [ 'item' ],
  data() {
    return {
      webcamAvailable: true
    }
  },
  methods: {

  },
  mounted: function() {

      var video =  document.querySelector("#ar-component")
      var constraints = window.constraints = { audio: false, video: true }

      var handleSuccess = function (stream) {
          var videoTracks = stream.getVideoTracks();
          console.log('Got stream with constraints:', constraints);
          console.log('Using video device: ' + videoTracks[0].label);
          stream.oninactive = function() {
            console.log('Stream inactive');
          };
          window.stream = stream; // make variable available to browser console
          video.srcObject = stream;
      };

      var errorMsg = function(msg, error) {
        if (typeof error !== 'undefined') {
            console.error(error);
        }
      }

      let that = this
      var handleError = function(error) {
        that.$data.webcamAvailable = false
        if (error.name === 'ConstraintNotSatisfiedError') {
          errorMsg('The resolution ' + constraints.video.width.exact + 'x' +
              constraints.video.width.exact + ' px is not supported by your device.');
        } else if (error.name === 'PermissionDeniedError') {
          errorMsg('Permissions have not been granted to use your camera and ' +
            'microphone, you need to allow the page access to your devices in ' +
            'order for the demo to work.');
        }
        errorMsg('getUserMedia error: ' + error.name, error);
      }

      navigator.mediaDevices.getUserMedia(constraints).then(handleSuccess).catch(handleError);
  }
}
</script>

<style>
.ar-container {

}

.error-message {
  color: red;
  font-size: 3.5vw;
  font-weight: bold;
}

.ar-element {
  min-width: 100%;
  min-height: 100%;
  width: 100%;
  height: auto;
  z-index: -100;
  background-size: cover;
  overflow: hidden;
  background-color: black;
}
</style>
