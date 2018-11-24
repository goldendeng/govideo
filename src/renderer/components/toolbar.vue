<template>
    <div class="gotoolbar">
        <el-button class="back"
            type="primary"
            icon="el-icon-arrow-left"
            @click='onClick'
        >
            返回
        </el-button>
        <div class="title">{{ title }}</div>
        <el-select class ="select"
            v-model="value"
            placeholder="请选择线路"
            @change='switchInterface'
        >
            <el-option
                v-for="item in parseInterfaces"
                :key="item.name"
                :label="item.name"
                :value="item.url">
                <!-- {{ item.url }} {{ item.name }} -->
            </el-option>
        </el-select>
    </div>
</template>

<style>
    .gotoolbar {
        align-items: center;
        height: 60px;
        line-height: 60px;
        background-color: #D3DCE6;
        width: 100%;
        display: flex;
        justify-content: flex-start;
        /* padding: 0 20px; */
    }

    .back {
        margin-right: 20px;
    }

    .title {
        margin-right: 20px;
    }

    /* .back {
        float: left;
        margin: 10px;
    }

    .title {
        float: left;
        height: 50px;
        width: 500px;
        line-height: 50px;
        padding: 5px;
        font-size: 16px;
    }

    .select {
        float: right;
        height: 50;
        line-height: 50px;
        margin: 7px auto;
        margin-right: 60px;
    } */
</style>

<script>
  export default {
    props: ['parseInterfaces', 'title'],
    data() {
      return {
        value: "",
      }
    },
    /* watch: {
        value: function(val, oldval) {
            console.log("select val:%s, old val:%s", val, oldval);
        },
        title: function(val) {
            console.log("toolbar title update:%s", val);
        }
    }, */
    methods: {
        switchInterface() {
            this.$emit('switchInterface', this.value);
        },
        onClick() {
            console.log("onClick");
            window.history.go(-1);
            var webview = document.getElementById("video");
            webview.goBack();
        }
    }
  }
</script>