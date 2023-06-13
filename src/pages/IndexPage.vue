<template>
  <q-page class="flex flex-center">
    <!-- <img
      alt="Quasar logo"
      src="~assets/quasar-logo-vertical.svg"
      style="width: 200px; height: 200px"
    > -->
    <q-btn color="primary" label="Get Picture" @click="captureImage" />

    <img :src="imageSrc" />

    <q-btn color="primary" label="Get Picture with plugin" @click="startScan" />

    <img :src="imageSrc" />
  </q-page>
</template>

<script>
import { ref } from 'vue'
import { Camera, CameraResultType } from '@capacitor/camera'
import { BarcodeScanner } from '@capacitor-community/barcode-scanner'

export default {
  setup () {
    const imageSrc = ref('')

    async function captureImage () {
      const image = await Camera.getPhoto({
        quality: 90,
        allowEditing: true,
        resultType: CameraResultType.DataUrl
      })

      // The result will vary on the value of the resultType option.
      // CameraResultType.Uri - Get the result from image.webPath
      // CameraResultType.Base64 - Get the result from image.base64String
      // CameraResultType.DataUrl - Get the result from image.dataUrl
      imageSrc.value = image.dataUrl
      console.log(image)
    }

    const startScan = async () => {
      // Check camera permission
      // This is just a simple example, check out the better checks below
      await BarcodeScanner.checkPermission({ force: true })

      // make background of WebView transparent
      // note: if you are using ionic this might not be enough, check below
      BarcodeScanner.hideBackground()

      const result = await BarcodeScanner.startScan() // start scanning and wait for a result

      // if the result has content
      if (result.hasContent) {
        console.log(result.content)
        alert(result.content) // log the raw scanned content
      }
    }

    return {
      imageSrc,
      captureImage,
      startScan
    }
  }
}
</script>
