<template>
  <div>
    <div class="banner">
      <ul id="banner">
        <li><router-link :to="{ 'name': 'WallPaper', 'query': { 'cid': 0 }}">最新壁纸</router-link></li>
        <li class="tags" style="margin-right: 50px">
          分类壁纸
          <ul id="tags">
            <li  v-for="(tag,index) in categories" :key="index" :cid="tag.id">
              <router-link :to="{ 'name': 'WallPaper', 'query': { 'cid': tag.id }}">{{ tag.name }}</router-link>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <router-view id="pic_body"></router-view>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Banner",
  data(){
    return {
      categories:[]
    }
  },
  created() {
    var _this = this;
    axios.get("http://wallpaper.apc.360.cn/index.php?c=WallPaperAndroid&a=getAllCategories")
        .then(function (data) {
          if (data.data.data) {
            _this.categories = data.data.data;
          }
        }).catch(function (err) {
      console.log(err);
      alert("接口异常!");
    });
  }

}
</script>

<style scoped>

/* 页面导航栏 */
.banner {
  position: fixed;
  z-index: 99999;
  height: 50px;
  width: 100%;
  line-height: 50px;
  font-size: 16px;
  opacity: 0.77;
  -ms-filter: progid:DXImageTransform.Microsoft.Alpha(Opacity=77);
  filter: alpha(opacity=77);
  transition: all 0.25s ease;
  -webkit-transition: all 0.25s ease;
  -moz-transition: all 0.25s ease;
  -o-transition: all 0.25s ease;
  -ms-transition: all 0.25s ease;
}



#banner {
  float: right;
}

#banner a {
  text-decoration: none;
  color: #D6EBF2;
  font-weight: 600;
}

#banner li {
  height: 100%;
  display: inline-block;
  padding: 0 10px;
  cursor: pointer;
  font-size: 15px;
  list-style-type: none;
  border-radius:12px;
  color: #1a73e8;
  margin: 10px;
  font-weight: 800;

}

#banner li:hover, #banner li:hover a {
  color: #1a73e8;
  background-color: #e8f0fe;
  font-weight: 800;

}

.tags:hover #tags {
  display: block;
  text-align: center;
}

#tags {
  display: none;
  flex-wrap: wrap;
  border-radius: 0 0 24px 24px;
  left: 0;
  z-index: 9999;
  display: none;
  position: absolute;
  line-height: 35px;
  max-height: 270px;
  overflow: auto;
  background-color: #fff;
  border-top: 2px solid #9EAFFF;
  font-size: 14px;
}
#tags li {
  width: 90px;
  float: left;
  padding: 0 0 0 15px;
  color: #000;
}

#tags li:hover {
  background-color: #eee;
}

#pic_body{
  padding-top: 20px;
}



</style>