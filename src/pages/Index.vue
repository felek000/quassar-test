<template>
  <q-page class="flex flex-center">
    <button v-if="status==='electron'" @click="saveFile">Zapisz</button>
    <div>
      <video v-if="fileName" ref="file" width="320" height="240" controls>
        <source :src="fileName" type=video/mp4>
      </video>
    </div>
  </q-page>
</template>

<script>
  //node only
  const fs = require('fs');
  const http = require('http');
  const {pipeline} = require('stream');

  export default {
    name: 'PageIndex',
    data() {
      return {}
    },
    computed: {
      fileName() {
        let fileName = `http://techslides.com/demos/sample-videos/small.mp4`;
        if (process.env.MODE === 'electron' && !window.navigator.onLine) {
          fileName = `${this.$q.electron.remote.app.getPath('desktop')}\\video.mp4`;
          if(!fs.existsSync(fileName)){
            fileName = null;
          }
        }
        return fileName;
      },
      status() {
        let status = 'web';
        if (process.env.MODE === 'electron') {
          status = 'electron';
        }
        return status;
      }
    },
    methods: {

      //node
      saveFile() {
        let file = fs.createWriteStream(`${this.$q.electron.remote.app.getPath('desktop')}\\video.mp4`);
        http.get(this.fileName, (res) => {
          console.log(res);
          pipeline(res, file, err => {
            if (err)
              console.error('Pipeline failed.', err);
            else
              console.log('Pipeline succeeded.');
          });
        });
      }
    }
  }
</script>
