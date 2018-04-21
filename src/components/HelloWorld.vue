<template>
  <div id="world_root">
    <mu-appbar title="Lottery System">
      <mu-icon-button icon="all_inclusive" slot="left"/>
    <!-- <mu-flat-button label="expand_more" slot="right"/>
    <mu-icon-button icon="expand_more" slot="right"/> -->
    </mu-appbar>
    <div class="add-wrapper">
      <mu-float-button icon="add" :class="{'rotate-90':trigger}" class="add-float-button" ref="add_icon" @click="open"/>
    </div>
    <div class="input_wrapper" v-show="showInput">
      <mu-text-field hintText="请输入抽奖者姓名 以逗号隔开" fullWidth v-model='userString' class="inp"/><br/>
      <mu-raised-button label="确认" primary fullWidth @click="start_roll"/>
      <mu-snackbar v-if="snackbar" message="抽奖者数量不能为空！" action="关闭" actionColor="#7e57c2" @actionClick="hideSnackbar" @close="hideSnackbar"/>
    </div>
    <div class="show-lucky" v-show="!showInput">
      <div class="avatar">
        <img :src="avatar" alt="" :class="{'no-blur':roll_success}">
      </div>
      <h1>{{random_name}}</h1>
      <p class="con" :style="{opacity:roll_success?1:0}">Congratulations!</p>
    </div>
  </div>
</template>
<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      trigger: false,
      showInput: false,
      userString: "",
      snackbar: false,
      random_name: "",
      avatar: "",
      roll_success: false,
      // avatar_img_arr:[],
    };
  },
  computed: {
    userArr() {
      if (this.userString.trim() === "") return [];
      return this.userString.split(/[,|，]/);
    }
  },
  mounted() {
    this.preloadAvatarImages();
  },
  methods: {
    preloadAvatarImages(){
      var promises = new Array(10).map((ele,index)=>{
        return new Promise((res,rej)=>{
          const image = new Image();
          image.onload=()=>res;
          image.onerror=()=>rej(index);
          image.src = `./static/avatar/a${index}.png`;
        });
      });

      Promise.all(promises)
      .then(()=>console.log('all images preload!'))
      .catch((index)=>console.log(`cannot load ./static/avatar/a${index}.png`));
    },
    open() {
      this.trigger = !this.trigger;
      this.showInput = !this.showInput;
    },
    showSnackbar() {
      this.snackbar = true;
      if (this.snackTimer) clearTimeout(this.snackTimer);
      this.snackTimer = setTimeout(() => {
        this.snackbar = false;
      }, 2000);
    },
    hideSnackbar() {
      this.snackbar = false;
      if (this.snackTimer) clearTimeout(this.snackTimer);
    },
    start_roll() {
      let userArr = this.userArr,
        timer,
        now_time = new Date(),
        len = this.userArr.length;
      if (userArr.length < 1) {
        return this.showSnackbar();
      }
      this.roll_success = false;
      this.showInput = false;
      timer = setInterval(() => {
        if (new Date() - now_time > 4000) {
          clearInterval(timer);
          this.roll_success = true;
        }
        let ranIndex = Math.floor(Math.random() * len);
        this.random_name = this.userArr[ranIndex];

        this.avatar = `./static/avatar/a${Math.floor(Math.random() * 10)}.png`;
      }, 100);
    }
  }
};
</script>
<style scoped>
.inp {
  margin-bottom: 20px;
}
.add-float-button {
  position: fixed;
  right: 20px;
  bottom: 20px;
  transition: all 0.2 ease-in-out;
}
.rotate-90 {
  transform: rotate(45deg);
}
.mu-text-field {
  font-size: 14px;
}
.input_wrapper {
  padding: 0px 20px;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
  width: 100%;
}
.show-lucky {
  text-align: center;
  color: #ff5722;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
}
.avatar {
  width: 45vw;
  height: 45vw;
  border-radius: 50%;
  margin: 10px auto;
  position: relative;
  text-align: center;
}
.avatar p {
  position: absolute;
  bottom: 20px;
  width: 100%;
  color: #666;
  font-size: 16px;
}
.avatar img {
  width: 100%;
  height: 100%;
  filter: blur(10px);
  border-radius: 50%;
  transition: all 0.3s ease-in-out;
  box-shadow: 0px 3px 10px 0 rgba(0, 0, 0, 0.23921568627450981);
}
.no-blur {
  filter: none !important;
}
body,
html {
  overflow-x: hidden;
  overflow-y: auto;
  height: 100%;
  background: #fafafa;
}
.con {
  color: #7e57c2;
  font-size: 17px;
  transition: all 0.3s ease-in-out;
}
</style>
