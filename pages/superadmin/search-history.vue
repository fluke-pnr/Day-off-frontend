<template>
  <div class="form-request-wrapper">
    <div v-if="!statusLoad" class="content_form">
          <div class="search">
              <btnsearch @returnUserId="searchName" />
          </div>
          <div>
              <filterhistory @startdate="getStartDate" @enddate="getEndDate" />
          </div>
          <div v-if="showCard.length > 0">
            <div v-for="(year,indexyear) in showCard" :key="indexyear">
              <div v-for="(month,indexmonth) in showCard[indexyear].list_month" :key="indexmonth">
                <Month
                  :year="year.year"
                  :datamonth="month"
                  :indexmonth="indexmonth"
                  :indexyear="indexyear"
                  page="searchhistory"
                  @clickcard="eventClickCard"
                />
              </div>
            </div>
          </div>
          <div v-else class="No_Data">Data not found</div>
    </div>
    <div v-if="statusLoad" class="page_loading">
      <LoadingPage/>
    </div>
  </div>
</template>
<script>
import { mapState } from 'vuex'
import BarComfrim from "../../components/BarComfrim"
import LoadingPage from "../../components/LoadingPage"
import CardRequest from "../../components/CardRequest/CardRequest"
import filterhistory from "../../components/filterhistory"
import btnsearch from "../../components/BtnSearch"
import Month from "../../components/Month"
import Provider from "../../service/provider"
import moment from "moment"

const provider = new Provider();
export default {
  components: {
    CardRequest,
    BarComfrim,
    filterhistory,
    btnsearch,
    LoadingPage,
    Month,
  },
  data() {
    return {
      statusLoad: true,
      statusShow: false,
      search: {
        userid: '',
        startdate: '',
        enddate: ''
      },
      found: false,
      api: provider,
      rendered: true,
      loading: false,
    };
  },
  mounted() {
    this.$store.commit('menu/setStatusSelectByLoadURL',{mainPath : 'superadmin',subPath : 'search-history'})
    this.createNowMonth();
    this.allnameuser();
  },
  methods: {
    moment,
    testBtn(event) {
      if (this.statusShow === true) {
        this.statusShow = false;
      } else {
        this.statusShow = true;
      }
    },
    searchName(userId) {
      this.search.userid = userId
      this.searchAuto()
    },
    getStartDate(startdate) {
      this.search.startdate = startdate;
    },
    getEndDate(enddate) {
      this.search.enddate = enddate;
    },

    searchAuto() {
      this.statusLoad = true
      let result = this.api.historyall(this.search)
      result.then(re => {
        this.$store.commit('leaveHistory/setHistoryAllBySearch', re)
        this.statusLoad = false
      })
    },

    allnameuser(){
      let result = this.api.getqueryUsers()
      result.then(re => {
          this.$store.commit('leaveHistory/setSearchListName', re)
      })
    },
    
    createNowMonth() {
      let nowDate = new Date()
      let indexMonth = nowDate.getMonth()
      let year = nowDate.getFullYear()
      let fullDayOfMonth = this.getDaysInMonth(indexMonth, year)
     
      let startDate = new Date()
      startDate.setDate(1)
      let endDate = new Date()
      endDate.setDate(fullDayOfMonth)
      let textmonthnow = new Date()
      textmonthnow.setDate(fullDayOfMonth)

      this.search = {
        userid: "",
        startdate: moment(startDate).format("YYYY-MM-DD"),
        enddate: moment(endDate).format("YYYY-MM-DD"),
      }
      this.searchAuto()
    },
    getDaysInMonth(month, year) {
      let date = new Date(year, month, 1)
      let days = []
      while (date.getMonth() === month) {
        days.push(new Date(date));
        date.setDate(date.getDate() + 1)
      }
      return days.length
    },
    eventClickCard(user){
      this.$store.commit('leaveHistory/setUserIdBySelect', user)

      if (user) {
        this.$store.commit('navbarBack/showNavbarBack')
        this.$router.push(this.pathDefult+'history-select')
      }
    }
  },
  computed: {
      ...mapState({
          allUser: state => state.leaveHistory.listAllUser,
          showCard: state => state.leaveHistory.history_search,
          pathDefult: state => state.pathDefult.path
      })
  },
};
</script>
<style lang="scss" scoped>
.form-request-wrapper {
  width: 100%;
  height: 100%;
}
.search {
  padding: 20px;
  justify-content: center;
  display: flex;
  padding-top: 40px;
}

.BoxFilter {
  display: flex;
  justify-content: center;
}
.unstyled::-webkit-inner-spin-button {
  display: none;
  -webkit-appearance: none;
}
.No_Data {
  text-align: center;
  margin-top: 50px;
  font-size: 20px;
}
.page_loading{
  background-color: #fff;
  width: 100%;
  height: 100%;
}
</style>