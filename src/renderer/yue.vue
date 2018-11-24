<template>
  <div style="overflow: hidden">
    <el-container>
      <el-aside width="auto"
        @mouseenter.native="collapseOpen"
        @mouseleave.native="collapseClose"
        :collapse="isCollapse"
      >
        <div class="isClossTab" @click="collapseStatus">
          <i :class="isCollapse?'el-icon-d-arrow-right':'el-icon-d-arrow-left'" ></i>
        </div>
        <el-menu default-active="https://www.douyu.com"
          class="el-menu-vertical-demo"
          @select="handleSelect"
          :collapse="isCollapse"
        >
          <el-menu-item
            v-for="channel in channels"
            :key=channel.name
            :index=channel.url
          >
            <span>{{ channel.name }}</span>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-container>
        <el-header>
          <gotoolbar
            :parseInterfaces="interfaces"
            :title="title"
            v-on:switchInterface='setInterfaceUrl'
          >          
          </gotoolbar>
        </el-header>
        <webview id="video"
                :title="title"
                :src="selectUrl"
                allowpopups="true"
                class="video"
        >
        </webview>
      </el-container>
    </el-container>
  </div>
</template>

<style>
    *{
      padding: 0;
      margin: 0;
    }

    .el-aside {
      background-color: #D3DCE6;
      color: #333;
      text-align: center;
      height: 100vh;
    }

    .isClossTab {
      width: 100%;
      height: 60px;
      cursor: pointer;
      font-size: 25px;
      text-align: center;
      line-height: 60px;
      font-weight: bold;
      /* border-right: 1px solid #D3DCE6; */
      box-sizing: border-box;
    }

    .el-menu-item {
      background-color: #D3DCE6;
    }
    .el-header {
        padding: 0;
        height: 60px;
    }

    .video {
      width: 100%;
      height: calc(100vh - 60px);
      display: flex;
      padding: 0;
      margin: 0;
      overflow: hidden;
    }
</style>

/* <style lang="scss" scoped>
    .el-dropdown-link{
      img{
        display:inline-block;
        width:50px;
        height: 50px;
        border-radius: 25px;
        background-color: green;
        margin-top: 5px;
      }
    }
</style> */

<script>
  import gotoolbar from './components/toolbar.vue';
  import * as api from './utils/fetch';
  export default {
    data() {
      return {
        collapseBtnClick: true,
        isCollapse: true,
        channels: [{"name": "斗鱼tv",
                    "url": "https://www.douyu.com"}],
        interfaces: /* [{name: '腾讯',
                      url: 'v.qq.com'},{
                      name: '优酷',
                      url: 'www.youku.com'
                    }] */[],
        title: "",
        interfacesUrl: "",
        selectUrl: "https://www.douyu.com",
        originUrl: ""
      };
    },
    components: {
        gotoolbar
    },
    methods: {
      handleSelect(key, keyPath) {
        console.log("select key:%s,keyPath:%s", key, keyPath);
        this.selectUrl = key;
      },
      collapseStatus() {
        this.isCollapse = !this.isCollapse;
        this.collapseBtnClick = this.isCollapse;
      },collapseOpen() {
        if (this.collapseBtnClick) return;
        this.isCollapse = false;
      },
      collapseClose() {
        if (this.collapseBtnClick) return;
        this.isCollapse = true;
      },
      getSources() {
        api.source.getAllVideoSource()
        .then(res => {
          this.channels = this.channels.concat(res.data.platformlist);
          //去重
          let newJson = {};
          for (var key in  res.data.list) {
            if (!newJson[res.data.list[key].url]) {
              this.interfaces.push(res.data.list[key]);
              newJson[res.data.list[key].url] = true;
            }
          }
          console.log(this.interfaces);
        })
      },
      setTitle(title) {
        this.title = title;
      },
      setSelectUrl(url) {
        this.selectUrl = url;
      },
      setInterfaceUrl(url) {
        this.interfacesUrl = url;
        this.selectUrl = `${url}${this.originUrl}`;
        console.log("解析地址：%s", this.selectUrl);
      },
      getOriginUrl(url) {/* 将含有解析地址的url转换成真实的url地址 */
        for (var interFace in this.interfaces ) {
          if (url.match(interFace.url)) {
            this.originUrl = url.replace(interFace.url, "");
            console.log("url:%s, orginUrl:%s", url, this.originUrl);
            break;
          }
          this.originUrl = url;
        }
      }
    },
    created() {
      console.log("created");
      this.getSources();
    },
    beforeUpdate() {
      console.log("beforeUpdate");
      var webview = document.getElementById("video");
      webview.addEventListener('dom-ready', () => {
        const title = webview.getTitle();
        console.log("update title:%s", title);
        this.setTitle(title);
      });
      webview.addEventListener('new-window', (obj) => {
        console.log(obj.url);
        this.setSelectUrl(obj.url);
        console.log("before getOriginUrl");
        this.getOriginUrl(obj.url);
        console.log("after getOriginUrl");
      });
      webview.addEventListener('will-navigate', (obj) => {
        console.log(obj.url);
        this.setSelectUrl(obj.url);
        console.log("before getOriginUrl");
        this.getOriginUrl(obj.url);
        console.log("after getOriginUrl");
      });
    }
  }
</script>