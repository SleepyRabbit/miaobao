<template>
  <div class="home">
  <p>Please select the photos you want to upload!</p>
  <input type="file" @change="onFileChange" accept="image/*" multiple>
  <button @click="uploadFile">Submit</button>
  <h4>{{ tip }}</h4>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'home',
  data () {
    return {
      filelength: 0,
      filelist: [],
      tip: "",
      imageData: ""
    }
  },
  methods: {
      fileReader(file, cb) {
        if (file){
        var reader = new FileReader();
    		reader.readAsDataURL(file);
        	reader.onload = function (res) {
           		cb && cb(res);
        	}
        }
      },
      onFileChange: function (e) {
        this.filelist  = e.target.files || e.dataTransfer.files;
        console.log("File change!");
        this.tip = "";
      },
      uploadFile: function () {
        let file_num = this.filelist.length;
        // 检查用户是否选择了文件
        if (!file_num) {
            this.tip = "No file selected!";
            return;
        }
        console.log("this.filelist:", this.filelist);

        let file = this.filelist[0];
        this.fileReader(file, res => {
          console.log("Res: ", res);
          this.imageData = res.currentTarget.result;
        })

        let url = "/handwriting";
        axios({
          method: "POST",
          url: url,
          data: this.imageData,
          headers: {
              'Content-Type': 'application/x-www-form-urlencoded'
          }
        }).then(res => {
            console.log("post successful!", res.data);
          }, res => {
            console.log("post failed!", res.data);
          });
      }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
