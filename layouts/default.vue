<template>
  <div id="layout_page">
        <div id="layout_navbar" >
            <Navbar v-if="icon === false"
              @controMenu="controMenu"
            />
             <NavberBack 
             @clickback="backTopath"
             v-if="icon === true"
             />
        </div>
        <div id="layout_content">
            <nuxt/>
            <div>
              <div v-if="statusClickMenu" class="my-menu" v-bind:class="{show: statusShow, hid: !statusShow}">
                <Menu @close="closeMenu"/>
              </div>
              <div v-if="statusClickMenu && statusShow" class="background-menu" @click="closeMenu"></div>
            </div>
            
        </div>
        <Popuplogout v-if="popupLogout"/>
    </div>
</template>

<script>
import { mapState } from 'vuex'
import NavberBack from "../components/NavberBack"
import Navbar from "../components/Navbar";
import Menu from "../components/Menu";
import Popuplogout from "../components/Popup/Popuplogout"
export default {
    components: {
        Navbar,
        Menu,
        Popuplogout,
        NavberBack
    },
     computed:{
        ...mapState({
            popupLogout: state => state.popup.popup_logout,
            icon: state => state.navbarBack.iconBack,
            pathDefult: state => state.pathDefult.path,
        }),
      },
    mounted(){
      let path = this.$route.name.split('-')
      let userLogin = JSON.parse(localStorage.getItem('userprofile'))
      if(userLogin.statusUser === 'super_admin'){
        if(path[0] !== 'superadmin'){
          this.checkStatusUserLogin()
        }else{
            this.updateStorePath(path)
          }
      }else if(userLogin.statusUser === 'admin'){
        if(path[0] !== 'admin'){
          this.checkStatusUserLogin()
        }else{
            this.updateStorePath(path)
          }
      }else if(userLogin.statusUser === 'employee'){
        if(userLogin.statusWorking === 'internship'){
          if(path[0] !== 'intern'){
            this.checkStatusUserLogin()
          }else{
            this.updateStorePath(path)
          }
        }else{
          if(path[0] !== 'employee'){
            this.checkStatusUserLogin()
          }else{
            this.updateStorePath(path)
          }
        }
      }else{
        this.checkStatusUserLogin()
      }
    },
    data(){
      return{
          statusClickMenu : false,
          statusShow: false
      }
    },
    methods:{
      checkStatusUserLogin(){
        document.getElementById('layout_page').hidden = true
        localStorage.setItem('userprofile',null)
        alert("ไม่มีสิทธิเข้าถึง")
        window.location.href = '/'
      },
      updateStorePath(path){
        this.$store.commit('pathDefult/setPath','/'+path[0]+'/')
        let model = {
          mainPath : path[0],
          subPath : path[1]
        }
        this.$store.commit('menu/setStatusSelectByLoadURL',model)
      },
      controMenu(){
          if(this.statusClickMenu === false){
            this.statusClickMenu = true
          }
          if(this.statusShow === true){
              this.statusShow = false
          }else{
              this.statusShow = true
          }
      },
      closeMenu(event){
        this.statusShow = false
      },
      backTopath(){
        this.$router.push(this.pathDefult+'search-history')
        this.$store.commit('navbarBack/showNavbarManu')
      }
    }
}
</script>

<style lang="scss">
@import "@/assets/style/StyleAllPage.scss";
@import "@/assets/style/Support.scss";
@import url('https://fonts.googleapis.com/css?family=Prompt:100,200,300,400,500,600,700,800,900&display=swap');
*{
  font-family: 'Prompt', sans-serif;
}
html {
  /* font-family: 'Source Sans Pro', -apple-system, BlinkMacSystemFont, 'Segoe UI',
    Roboto, 'Helvetica Neue', Arial, sans-serif; */
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
  position: sticky;
}

.my-menu{
    width: $widthMenu;
    height: $heightContent;
    position: fixed;
    top: $heightNav;
}

.background-menu{
  width: 100% - $widthMenu;
  position: fixed;
  top: $heightNav;
  right: 0px;
  height: $heightContent;
  background-color: rgba(255, 255, 255, 0);
}

.show{
    left: 0px;
    animation-name: myShow;
    animation-duration: 0.3s;
}
@keyframes myShow {
    0% {
        left: -$widthMenu;
    }
    100%{
        left: 0px;
    }
}

.hid{
    left: -$widthMenu;
    animation-name: myhid;
    animation-duration: 0.3s;
}
@keyframes myhid {
    0% {
        left: 0px;
    }
    100%{
        left: -$widthMenu;
    }
}

 .tooltip{
   width: auto;
   border-radius: 10px;
   background-color: white;
   color: black;
    border: 1px solid black;
   padding: 3px;
   margin-top: 5px;
  
 }
 .v--modal-overlay .v--modal-box {
  /* width: 250.2px; */
  border-radius: 30px;
}

.fc-unthemed td.fc-today {
  background-color: rgba(85, 172, 238, 0.2);
}

.fc-day-grid-event .fc-time {
  display: none;
}

.fc .fc-row .fc-content-skeleton table,
.fc .fc-row .fc-content-skeleton td,
.fc .fc-row .fc-mirror-skeleton td {
  background: none;
  border-color: transparent;
  font-size: 12px;
}
.fc-event{
  cursor: pointer;
}
</style>