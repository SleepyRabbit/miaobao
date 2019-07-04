<template>
  <div class="home">
  <p>Please select the photos you want to upload!</p>
  <input type="file" @change="onFileChange" accept="image/*" multiple>
  <button @click="onUpload">Submit</button>
  <h4>{{ tip }}</h4>
  </div>
</template>

<script>
import request from "@/utils/request";

let option = {
  client_id: "9yo34fa44h5pXhhsfbuzllBe",
  client_secret: "tZTEmxMTbBHZkyHKNBGERIoBfw9V20Ig",
}

export default {
  name: 'home',
  data () {
    return {
      filelength: 0,
      filelist: [],
      tip: "",
      token: "",
      imageData: ""
    }
  },
  created() {
    this.getToken();
  },
  methods: {
      async getToken() {
        let url_param = `?grant_type=client_credentials&client_id=${option.client_id}&client_secret=${option.client_secret}`
        let url = "/token" + url_param;
        let opt = {
          contentType: "application/json"
        }
        let res = await request.fetchData(url, null, opt);
        // console.log("Res ", res);
        if(res && res.access_token) {
          this.token = res.access_token;
          // console.log("has token: ", this.token)
          console.log("get access_token successful!");
        } else {
          console.log("has no access_token!");
        }
      },
      fileReader(file, cb) {
        if (file){
          let reader = new FileReader();
          let imgUrlBase64 = reader.readAsDataURL(file);
          reader.onload = function (e) {
            // let ImgFileSize = reader.result.substring(reader.result.indexOf(",") + 1).length;//截取base64码部分（可选可不选，需要与后台沟通)
            // console.log("ImgFileSize: ", ImgFileSize); 
            cb && cb(reader.result);
          }
        }
      },
      onFileChange(e) {
        this.filelist  = e.target.files || e.dataTransfer.files;
        // console.log("File change!");
        this.tip = "";
      },
      async onUpload() {
        let file_num = this.filelist.length;
        // 检查用户是否选择了文件
        if (!file_num) {
            this.tip = "No file selected!";
            return;
        }
        // console.log("this.filelist:", this.filelist);

        let file = this.filelist[0];
        this.fileReader(file, res => {
          // console.log("file res:", res);
          this.imageData = res.replace(/^data:image\/\w+;base64,/, '');
          this.uploadFile()
        })
      },
      async uploadFile() {
        // console.log("this.imageData: ", this.imageData)
        let url = `/detect?access_token=${this.token}`;
        let opt = {
          contentType: "application/json;charset=UTF-8"
        }
        let data = {
          image_type: 'BASE64',
          image: this.imageData,
          group_id_list : "gropu001",
          user_id: "001",
        }
        let res = await request.fetchData(url, data, opt);
        if(res && res.error_code) {
          console.log("err code:", res.error_code, "\nerr msg:", res.error_msg)
        }
        else {
          console.log("ai response data: ", res);
        }
      }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
