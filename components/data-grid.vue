<template>
  <div>

    <table>
        <thead>
          <tr>
            <th>#</th>
            <th v-for="key in columns"
              @click="sortBy(key)"
              :class="{ active: sortKey == key }" :key="key">
              {{ key | capitalize }}
              <span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'">
              </span>
            </th>
          </tr>
        </thead>
        <tbody>
           <!-- <div class="meeting" v-for="(m, i)  in filteredMeetings" :key="m._id">
            {{i+pages.to}}.{{ m.name }} {{time_formatted}}
          </div> -->
          <!-- adding new lunch dates -->
          <tr><td @click="saveNewLunch">Save</td>
            <td><input type="text" v-model="new_lunch.name"></td>
            <td><input type="text" v-model="new_lunch.owner"></td>
            <td><input type="text" v-model="new_lunch.date"></td>
            <td><input type="text" v-model="new_lunch.addr"></td>
          </tr>
          <tr class="meeting" v-for="(entry, i) in filteredData" :key='entry._id'>
            <td>{{i+pages.from}}</td>
            <td v-for="(key, i) in columns" :key='i'>
              {{ entry[key]}}
              <!-- {{ entry[key].substr(0,20)}} -->
              <!-- <div class="data-info" v-if="key == 'name'">
                {{entry["address"]}}
              </div> -->
            </td>
          </tr>
        </tbody>
      </table>
  <!-- Pagination******************** -->
  <!-- Pagination******************** -->
  <!-- Pagination******************** -->
   <pagination :pages='pages'
    @prev="changePage(-1)"  @next="changePage(1)"
  ></pagination>
  <!-- <ul>
      <li v-for="(pageNumber, i) in totalPages" v-if="visable" @click="currentPage = pageNumber"
      :class="{current: currentPage === pageNumber, last: (pageNumber == totalPages - 1 && Math.abs(pageNumber - currentPage) > 3), first:(pageNumber == 0 && Math.abs(pageNumber - currentPage) > 3)}" :key='i'>
      {{ i+1 }}
      </li>
  </ul> -->
   <!-- Pagination******************** -->
  <!-- Pagination******************** -->
  <!-- Pagination******************** -->
  <!-- <hr/>
  <div>Result Count= {{resultCount}} Total Pages = {{totalPages}}</div>
  <div class="current-page">  Page {{currentPage}}</div> -->
  </div>
</template>

<script>
 module.exports = {
  name: 'data-grid',
  props: {
    db: Object,
    new_lunch: Object,
    columns: Array,
    selectDay: String,
    filterKey: String,
  },
    components: {
     'pagination': httpVueLoader('./pagination.vue'),
    //  'fitall': Fitall,
  },
  data: function () {
    var sortOrders = {}
    this.columns.forEach(function (key) {
      sortOrders[key] = 1
    })
    return {
      sortKey: '',
      sortOrders: sortOrders,
      searchKey: '',
      currentPage: 0,
      itemsPerPage: 25,
      resultCount: 0,
        meetingsPerPage: 15,
      maxPages: 0,
      currentItem: 0,
      pages: {
        total: 0,
        prevPage: 0,
        nextPage: 0,
        from: 0,
        to: 0
      }       
    }
  },
  firebase: function () {
    return {
       lunches: db.ref().child('web/data/lunches')
    }
  },
  computed: {
    visable: function(){
     // if (this.resultCount == 0) return false;
      if (this.resultCount < this.itemsPerPage) return false;
      return true;
    },
    totalPages: function() {
          return Math.ceil(this.resultCount / this.itemsPerPage)
        },
    filteredData: function () {
    // debugger
     // if ((this.filterKey.length < 3) && (this.filterKey.length > 0)) return;
     
     //  this.setPagination(this.data.length);
      days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
      var sortKey = this.sortKey
      var filterKey = this.filterKey && this.filterKey.toLowerCase()
      var filterDay = this.selectDay
      var order = this.sortOrders[sortKey] || 1
      var data = this.lunches
      // var data = this.data
      if (data.length == 0) return;

      if (filterKey.length > 3) {
        data = data.filter(function (row) {
          return Object.keys(row).some(function (key) {
            return String(row[key]).toLowerCase().indexOf(filterKey) > -1
          })
        })
              
      }
     //  this.setPagination(data)
      if (sortKey) {
        data = data.slice().sort(function (a, b) {
          a = a[sortKey]
          b = b[sortKey]
          return (a === b ? 0 : a > b ? 1 : -1) * order
        })
      }
      if (filterDay){
        // debugger
        data = data.filter(function(m){
          return m.day == filterDay
        })
      }
      if ((data.length > 0) && (data[0].day) && (data[0].day.length == 1)){
        // console.log(`data map day = ${m.day} in ${m.name}`)
        data = data.map(function(m){
          m.day = days[m.day].substr(0,3)
          return m
          })
      }
     if (this.pages.total != data.length){
       this.pages.from = 0
       this.pages.to = this.pages.from + this.meetingsPerPage
        if (this.pages.to > this.pages.total) {
          this.pages.to = this.pages.total
        }
       this.pages.total = data.length
     }
      // this.resultMeetings = data
      return this.paginate(data)
      // return data
    }
  },
  filters: {
    capitalize: function (str) {
      return str.charAt(0).toUpperCase() + str.slice(1)
    },
   
  },
  methods: {
    validateLunch: function(l){
      var valid = true
      Object.keys(l).forEach(function(key,index) {
    // key: the name of the object key
    // index: the ordinal position of the key within the object 
    // debugger
        if (l[key].length < 1) {// any zero key makes all invalid
          valid = false 
        }
        });
      return valid
    },
    saveNewLunch: function(e){
     // console.log("save new lunch: event=" + e.target)
     // validate new lunch fields
    //  debugger
     if (this.validateLunch(this.new_lunch)){
       console.log("valid lunch")
       this.$emit('save_lunch')
     } else {
       console.log('not valid')
     }

    },
     setPagination(list){
       // debugger
        this.pages.total = list.length
        this.pages.prevPage = null;
        this.pages.nextPage = this.meetingsPerPage + 1
        this.pages.from = 0
        this.pages.to = this.meetingsPerPage
      },
     changePage(inc){
        this.pages.from += this.meetingsPerPage * inc;
        if (this.pages.from < 0) this.pages.from = 0
        if (this.pages.from > this.pages.total) this.pages.from = this.pages.total
        this.pages.to = this.pages.from + this.meetingsPerPage
        if (this.pages.to > this.pages.total) {
          this.pages.to = this.pages.total
        }
      },
    paginate: function(data) {
     //  var data = this.list
     //  this.pages.total = data.length
      //  this.pages.from = 0
        var page = this.pages.from//- 1//) * this.meetingsPerPage
        if (data.length > this.meetingsPerPage){
          data = data.slice(page,page + this.meetingsPerPage)
        }
        return data



        //    // if (list.length == 0) return []
        //     this.resultCount = list.length
        //     if (this.currentPage >= this.totalPages) {

        //       this.currentPage = this.totalPages - 1
        //       if (this.currentPage < 0) this.currentPage = 1
        //     } 
        //     var index = this.currentPage * this.itemsPerPage
          
        //     return list.slice(index, index + this.itemsPerPage)
         },
    sortBy: function (key) {
      this.sortKey = key
      this.sortOrders[key] = this.sortOrders[key] * -1
    },
    setPage: function(pageNumber) {
          this.currentPage = pageNumber
        }
  },
   created(){
      console.log("created........data-grid")
     
    },
  }
</script>

<style>
table {
    border-collapse: collapse;
}

table, td, th {
    border: 1px solid black;
    font-size: 10px;
}
td{
  padding: 5px 10px;
}
table{
  /* width: 50%;
  height: 600px; */
}
.meeting-list{
   border: 1px solid black;
   margin: 30px;
   margin-bottom: 10px;
   padding: 20px;

 }
 .meeting{
   border: 1px solid rgba(16, 17, 124, 0.445);
   border-radius: 5px;
   /* background: rgb(8, 49, 56); */
   margin: 2px 5px;
   padding: 5px;

 }
</style>