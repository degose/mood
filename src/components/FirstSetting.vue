<template>
  <div class="first-setting container">
    <home-header></home-header>
    <div class="grid">
      <div class="box col col-d-6 col-d-offset-3 col-m-4">
        <div class="col">
          <h3>프로필 설정</h3>
        </div>
        <div class="user-img-wrapper col">
          <div class="info-wrapper">
            <div class="radius">
              <span class="user-img-icon" v-if="!isCurrentUser.photoURL"></span>
              <img class="user-img" alt="회원 이미지 등록" :src="isCurrentUser.photoURL">
            </div>
            <form class="file-input-wrapper" action="javascript:void(0);" id="uploadImg" name="uploadImg" method="PATCH" enctype="multipart/form-data">
              <input type="file" class="user-img-input" id="upload" ref="file_input" @change="setting_first_photo">
              <label for="upload"></label>
            </form>
          </div>
        </div>
        <div class="form col">
          <input class="nickTest" type="text" @input="setting_first_displayname" placeholder="유저 네임을 설정해주세요." v-focus="true" @keyup.enter="a_firstSetting">
          <p class="errmsg" id="pw_msg">{{ isSetting_err_msg }}</p>
        </div>
        <div class="buttons col">
          <button v-on:click="a_firstSetting" class="register">등록!</button>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import firebase from 'firebase'
import HomeHeader from './HomeHeader.vue'
import { state, mapGetters, mapMutations, mapActions } from 'vuex'
const focus = {
  inserted(el) {
    el.focus()
  },
}

export default {
  beforeRouteEnter (to, from, next) {
    let signup = window.localStorage.getItem('signup')
    if (signup === null) {
      next('/login');
    } else {
      next();
    }
  },
  name: 'firstSetting',
  directives: { focus },
  components: {
    HomeHeader
  },
  computed: {
    ...mapGetters(['isSetting_err_msg', 'isCurrentUser','isSignup_email']),
  },
  methods: {
    checkImage(file) {
      if (/.*\.(gif)|(jpeg)|(jpg)|(png)$/.test(file.name.toLowerCase()) && file.size < 1024 * 1024 * 3) {
        return true;
      }
    },
    ...mapActions(['a_firstSetting']),
    setting_first_photo(e) {
      let _this = this;
      let file = e.target.files[0];
      let reader = new FileReader();
      if (this.checkImage(file)) {
        this.file = file;
        reader.readAsDataURL(file);
        reader.onload = data => {
          this.$store.dispatch('a_setFirstPhoto', data.srcElement.result);
          this.$store.dispatch('a_setFirstErrMsg', '')
        }
      } else { this.$store.dispatch('a_setFirstErrMsg', '3MB 이하의 이미지 파일만 선택 가능합니다.') }
    },
    setting_first_displayname(e) {
      this.$store.dispatch('a_setFirstDisplayName', e.target.value)
    },
  }
}
</script>

<style lang="scss" scoped>
@import "~style";

h3 {
  text-align: center;
  color: $color-mood;
  font-size: 1.6rem;
  margin-bottom: 20px;
}

.user-img-wrapper {
  text-align: center;
}

.info-wrapper {
  position: relative;
  width: 155px;
  height: 128px;
  display: inline-block;
  margin-bottom: 20px;
}

.radius {
  background: $color-moregray;
  display: inline-block;
  width: 128px;
  height: 128px;
  overflow: hidden;
  border-radius: 50%;
}

.user-img-icon {
  width: 128px;
  height: 128px;
  display: block;
  background: url('../assets/mood-icon-profile.svg');
  background-repeat: no-repeat;
  background-size: 50px auto;
  background-position: 50% 40%;
}

.user-img {
  width: 100%;
  height: 100%;
}


input {
  @extend %input-style;
}

.form {
  text-align: center;
}

.user-img-input {
  width: 0.1px;
  height: 0.1px;
  opacity: 0;
  overflow: hidden;
  position: absolute;
  z-index: -1;
}

.user-img-input+label {
  font-size: 1.25em;
  font-weight: 700;
  display: inline-block;
  cursor: pointer;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  position: absolute;
  bottom: 0;
  right: 0;
  border: 4px solid $color-white;
  background: $color-moregray url('../assets/setting-02.svg');
  background-repeat: no-repeat;
  background-size: 20px auto;
  background-position: 50% 50%;
}

.user-img-input:focus+label,
.user-img-input+label:hover {
  background-color: $color-haha;
}

.box {
  @extend %box-style;
  display: block;
  padding: 60px 0;
  margin-bottom: 50px;
}

.buttons {
  margin-top: 50px;
  text-align: center;
}

.register {
  width: 40%;
  height: 50px;
  border: none;
  border-radius: 5px;
  background-color: $color-haha;
  color: $color-happy;
}

.errmsg {
  text-align: left;
  padding-left: 20%;
  margin-top: 10px;
  font-size: 0.8rem;
  color: $color-alert;
}

::-webkit-input-placeholder {
  /* Chrome */
  color: $color-moregray;
}

:-ms-input-placeholder {
  /* IE 10+ */
  color: $color-moregray;
}

::-moz-placeholder {
  /* Firefox 19+ */
  color: $color-moregray;
  opacity: 1;
}

:-moz-placeholder {
  /* Firefox 4 - 18 */
  color: $color-moregray;
  opacity: 1;
}
</style>