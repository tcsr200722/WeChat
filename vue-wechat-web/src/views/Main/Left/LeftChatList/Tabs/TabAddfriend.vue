<template>
  <div class="left-chat-list-tab-wrap">
    <div class="add-friend-wrap">
      <div
        class="add-friend-main"
        v-for="(article, index) in articles"
        :key="'article' + index"
      >
        <div class="add-friend-title">添加好友</div>
        <div class="add-friend-input">
          <input v-model="friendAccount" class="add-friend-friendAccount" type="text" placeholder="账号" />
          <button class="add-friend-btn" v-on:click="handlefindfriend">
            查 找 账 号
          </button>
        </div>
      </div>
    </div>
    
    <div class="add-group-wrap">  
      <div
        class="add-group-main"
        v-for="(article, index) in articles"
        :key="'article' + index"
      >
        <div class="add-group-title">添加群组</div>
        <div class="add-group-input">
          <input v-model="groupAccount" class="add-group-groupAccount" type="text" placeholder="群号" />
          <button class="add-group-btn" v-on:click="handlefindgroup">
            查 找 群 组
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import avatar from "@/assets/default.png";
// import img from "@/assets/background.png";
import axios from 'axios'
import Qs from 'qs'

export default {
  name: "TabChat",
  data() {
    return {
      articles: [],
      friendAccount: '',
      groupAccount: '',
      // currentArticle: -1
    };
  },
  mounted() {
    this.articles.push("");
  },
  methods: {
    //根据账号，查找好友
    handlefindfriend() {
      var that = this;
      var mydata={
        u_id: that.$store.state.myself.id,
        u_friendAccount:that.friendAccount,
      }

      that.axios({
        method: "post",
        url: 'http://127.0.0.1:8077/WeChat/friend/find?account='+mydata.u_friendAccount,
        data:Qs.stringify(mydata)
      })
      .then(function(res) {
        console.log(res);
        if (res.data.msg=="查找成功") {
          that.$store.state.linkOrTemp = 0;
          that.$store.state.tempLinkman.id = res.data.data.uid
          that.$store.state.tempLinkman.account = res.data.data.userAccount
          that.$store.state.tempLinkman.nickname = res.data.data.nickName
          that.$store.state.tempLinkman.name = res.data.data.name
          that.$store.state.tempLinkman.sex = res.data.data.sex
          that.$store.state.tempLinkman.birthday = res.data.data.birthday
          that.$store.state.tempLinkman.email = res.data.data.email
          that.$store.state.tempLinkman.schoolname = res.data.data.schoolName
          that.$store.state.tempLinkman.city = res.data.data.city
          that.$store.state.tempLinkman.bloodtype = res.data.data.bloodType
          that.$store.state.tempLinkman.avatar = res.data.data.gravatar
          that.$store.state.isgroup = false
          that.$store.state.isfriend = false
          for (let i=0; i<that.$store.state.linkmans.length; ++i) {
            if (that.$store.state.linkmans[i].id === that.$store.state.tempLinkman.id &&
            that.$store.state.linkmans[i].type != "group") {
              that.$store.state.isfriend = true
              break;
            }
          }
          that.$store.commit("getIndexById", {id:that.$store.state.tempLinkman.id, isgroup:false})
          that.$store.commit("setCurrentRight", 1);
        } else {
          alert(res.data.msg);
        }
      })
      .catch(function(err) {
        console.log(err);
      });
    },
    //根据群号，查找群组
    handlefindgroup() {
        var that = this;
        var mydata={
          g_name: that.groupAccount,
        }

        that.axios({
          method: "post",
          url: 'http://127.0.0.1:8077/WeChat/group/search',
          data:Qs.stringify(mydata)
        })
        .then(function(res) {
          if (res.data.msg=="查找成功") {
            that.$store.state.linkOrTemp = 0;
            that.$store.state.tempLinkman.id = res.data.data.id
            that.$store.state.tempLinkman.nickname = res.data.data.name
            that.$store.state.isgroup = true
            that.$store.state.isingroup = false
            for (let i=0; i<that.$store.state.linkmans.length; ++i) {
              if (that.$store.state.linkmans[i].id === that.$store.state.tempLinkman.id &&
              that.$store.state.linkmans[i].type == "group") {
                that.$store.state.isingroup = true
                break;
              }
            }
            that.$store.commit("getIndexById", {id:that.$store.state.tempLinkman.id, isgroup:true})
            that.$store.commit("setCurrentRight", 1);
          } else {
            alert(res.data.msg);
          }
        })
        .catch(function(err) {
          console.log(err);
        });
    },
  }
};
</script>

<style scoped>
.add-friend-wrap {
  width: 100%;
  height: 100px;
  border:1px solid #3d4d31;
}

.add-friend-main {
  flex-grow: 1;
}

.add-friend-title {
  text-align: center;
  line-height: 30px;
  font-size: 15px;
  color: #427e11;
}

input::-webkit-input-placeholder,
textarea::-webkit-input-placeholder {
  color: #d4d4d4;
}

.add-friend-input {
  display: flex;
  flex-direction: column;
  padding: 0 20px 10px;
  color: #d4d4d4;
}

.add-friend-friendAccount {
  margin-top: 5px;
  padding: 10px 10px;
  line-height: 10px;
  height: 10px;
  border: 1px solid #272c24;
  font-size: 15px;
  outline: none;
  transition: 0.4s;
  background-color: #272c24;
  color: rgb(253, 255, 247);
}

.add-friend-friendAccount:focus {
  border-color: #272c24;
  transition: 0.4s;
  color: rgb(253, 255, 247);
}

.add-friend-btn {
  border: none;
  outline: none;
  background-color: #427e11;
  color: #fff;
  font-size: 10px;
  line-height: 10px;
  height: 25px;
  cursor: pointer;
}

.add-group-wrap {
  width: 100%;
  height: 100px;
  border:1px solid #3d4d31;
}

.add-group-main {
  flex-grow: 1;
}

.add-group-title {
  text-align: center;
  line-height: 30px;
  font-size: 15px;
  color: #427e11;
}

.add-group-input {
  display: flex;
  flex-direction: column;
  padding: 0 20px 10px;
  color: #d4d4d4;
}

.add-group-groupAccount {
  margin-top: 5px;
  padding: 10px 10px;
  line-height: 10px;
  height: 10px;
  border: 1px solid #272c24;
  font-size: 15px;
  outline: none;
  transition: 0.4s;
  background-color: #272c24;
  color: rgb(253, 255, 247);
}

.add-group-groupAccount:focus {
  border-color: #272c24;
  transition: 0.4s;
  color: rgb(253, 255, 247);
}

.add-group-btn {
  border: none;
  outline: none;
  background-color: #427e11;
  color: #fff;
  font-size: 10px;
  line-height: 10px;
  height: 25px;
  cursor: pointer;
}

/* .article {
  cursor: pointer;
}

.article:nth-child(1n + 2) {
  border-top: 10px solid #292d32;
}

.info-wrap {
  display: flex;
  padding: 10px 20px;
  color: #888;
  font-size: 12px;
}

.avatar-wrap {
  margin-right: 10px;
}

.avatar {
  width: 20px;
  height: 20px;
  border-radius: 50%;
}

.username {
  flex-grow: 1;
  display: flex;
  align-items: center;
}

.time {
  display: flex;
  align-items: center;
}

.detail-wrap {
  padding: 10px 18px 9px;
  border-bottom: 1px solid rgb(41, 44, 51);
  display: flex;
  justify-content: space-between;
}

.detail-wrap-selected {
  background-color: #3a3f45;
}

.title {
  color: #fff;
  font-size: 13px;
  display: flex;
  align-items: center;
}

.img-wrap {
  margin-left: 10px;
}

.img {
  width: 40px;
  height: 40px;
} */
</style>
