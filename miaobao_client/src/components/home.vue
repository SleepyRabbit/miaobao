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
      token: "",
      imageData: ""
    }
  },
  created() {
    this.getToken();
  },
  methods: {
      getToken() {
        let url = "/token" + "?grant_type=client_credentials&client_id=9yo34fa44h5pXhhsfbuzllBe&client_secret=tZTEmxMTbBHZkyHKNBGERIoBfw9V20Ig"
        axios({
          method: "POST",
          url: url,
          // headers: {
          //     'Content-Type': 'application/json'
          // }
        }).then(res => {
            console.log("post successful!", res);
            if(res && res.data && res.data.access_token) {
              this.token = res.data.access_token;
              console.log("has token: ", this.token)
            }
          }, res => {
            console.log("post failed!", res);
          });
      },
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
          // console.log("Res: ", res);
          this.imageData = res.currentTarget.result;
        })

        console.log("this.imageData: ", this.imageData)

        let url = `/search?access_token=${this.token}`;
        console.log("post url: ", url);
        let data = {
          image_type: 'BASE64',
          image: this.imageData,
          group_id_list: "gropu001",
          user_id: "001",
        }
        axios.post(url, data, {
          headers: {
              'Content-Type': 'application/x-www-form-urlencoded',
          }
        }).then(res => {
            console.log("post successful!", res.data);
          }, res => {
            console.log("post failed!", res.data);
          });
        // axios({
        //   method: "POST",
        //   url: url,
        //   data: data,
        //   headers: {
        //       'Content-Type': 'application/x-www-form-urlencoded',
        //   }
        // }).then(res => {
        //     console.log("post successful!", res.data);
        //   }, res => {
        //     console.log("post failed!", res.data);
        //   });
      }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
