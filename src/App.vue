<template>

  <div id="app"  @mouseenter.stop="winShow" @mouseleave.stop="winHide">

    <div class="mask"></div>
    <div class="drag-nav">
      <b>{{ appName }}</b>
    </div>
    <div class="nav">
      <div class="link">
        <router-link draggable="false" to="/">Todo</router-link>
        <router-link draggable="false" to="/done">Done</router-link>
        <router-link draggable="false" to="/shedule">Schedule</router-link>
        <router-link draggable="false" to="/wallfall/wallpaper?cid=0">WallPaper</router-link>

      </div>
      <div class="tools">
        <transition-group name="fade" mode="out-in">
          <i class="iconfont icon-export" key="export" @click="exportData"></i>
          <i class="iconfont icon-eye-close" key="hide" @click="hideWindow"></i>
          <i
            :class="['iconfont', ignoreMouse ? 'icon-lock' : 'icon-unlock']"
            key="lock"
            @click="ignoreMouse = !ignoreMouse"></i>
<!--            @mouseenter="setIgnoreMouseEvents(false)"-->
<!--            @mouseleave="setIgnoreMouseEvents(ignoreMouse)"-->
<!--            @click="ignoreMouse = !ignoreMouse"-->
<!--          ></i>-->
        </transition-group>
      </div>
    </div>
    <div class="main scrollbar scrollbar-y">
      <transition name="fade-transform" mode="out-in">
        <!-- <keep-alive> -->
        <router-view />
        <!-- </keep-alive> -->
      </transition>
    </div>


  </div>
</template>

<script>
import pkg from "../package.json";
import { ipcRenderer } from "electron";
import moment from "moment";
import schedule from "node-schedule";
import DB from "./utils/db";
export default {
  components: {},
  data() {
    return {
      appName: pkg.name,
      ignoreMouse: true,
    };
  },

  methods: {
    winShow(){
      if (this.ignoreMouse)return;
      ipcRenderer.invoke("winShow");
    },
    winHide(){
      if (this.ignoreMouse) return;
      ipcRenderer.invoke("winHide");
    },
    // setIgnoreMouseEvents(ignore) {
    //   ipcRenderer.invoke("setIgnoreMouseEvents", ignore);
    // },
    exportData() {
      ipcRenderer.invoke("exportData");
    },
    hideWindow() {
      ipcRenderer.invoke("hideWindow");
    },
  },
  mounted() {
    let sortschedulelistByTime;
    //设置每分钟运行一次，检测时间是否满足要求
    schedule.scheduleJob('*/1 * * * *', function() {
      sortschedulelistByTime = DB.sortschedulelistByTime();
      for(let i of sortschedulelistByTime) {
        if(i.time == moment().format('HH:mm')) {
          // 发出通知并弹窗
          new Notification('定时任务提醒', {
            body: i.content
          })
          alert(i.content);
        }
      }
    });
  },
};
</script>

<style lang="scss" scoped>
#app {
  display: flex;
  flex-direction: column;
  width: 100%;
  height: calc(100vh - 151px);;
  background-color: rgba($color: #000000, $alpha: 0.6);
  border-radius: 5px;
  .mask {
    display: none;
    position: absolute;
    z-index: 999;
    width: 100%;
    height: 100%;
  }
  .drag-nav {
    -webkit-app-region: drag;
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    height: 20px;
    padding: 0 20px;
    box-sizing: border-box;
    font-size: 12px;
    b,
    i {
      color: rgba($color: #ffffff, $alpha: 0.3);
    }
  }
  .nav {
    display: flex;
    justify-content: space-between;
    padding: 0 20px;
    color: #cccccc;
    user-select: none;
    .link {
     margin-top: 10px;
      a {

          border-radius:12px;
        
          padding: 10px;
        font-size: 15px;
        font-weight: bold;
        color: #cccccc;
        text-decoration: none;
        &.router-link-exact-active {
          font-size: 20px;
          color: #ffffff;
        }
        &:hover {
        color: #ffffff;
        background-color: #383838;

        }
      }
    }
    .tools {
      display: flex;
      i {
        font-size: 20px;
        line-height: 26px;
        padding: 0 5px;
        cursor: pointer;
      }
    }
  }
  .main {
    flex: 1;
    margin: 10px 0;
    overflow-y: auto;
    &:hover::-webkit-scrollbar-thumb {
      display: block;
    }
  }
}
#app.unfocused {
  opacity: 0.8;
  .mask {
    display: block;
  }
  .tools {
    z-index: 1000;
  }
}

::-webkit-scrollbar {
    width: 4px!important;
}

</style>
