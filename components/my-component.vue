<template>
  <div id="home">
      <div class="wrapper">
        <div class="box header">Lunch Count: {{lunchCount}}
 <editable :content="text" @update="text = $event"></editable>
   <editable :content="name" @update="name = $event"></editable>
        </div>
        <div class="box sidebar">
          <a @click="grid = !grid">Toggle Grid</a>
        </div>
        <div class="box sidebar2"></div>
        <div class="box content" v-show="!grid">
          <!-- <div class="lunches" v-for="(lunch, i) in lunches" :key="i"> -->
          <div class="lunches" v-for="(lunch, i) in filterLunches" :key="i">
            <h3 v-if="lunch.section">{{lunch.section}} </h3>
            {{ i}}.  {{ lunch.date}} {{ lunch.name}} {{ lunch.owner }} 
          </div>
        </div>
        <div v-show="grid" class="box content grid">
           <data-grid
            :db='db'
            :new_lunch="new_lunch"
            :columns="gridColumns"
            :filter-key="searchQuery"
            @save_lunch="saveLunch">
          </data-grid>
        </div>
        <div class="box footer">Footer</div>
      </div>
  </div>
</template>
<script>
//const lunchData = firebase.database().ref().child('web/data/lunches')
   // alert('lunch data = ' + lunchData)
  //  lunchData.on('value', function(snap) {
  //     // console.log('lunchData:' + JSON.stringify(snap.val(), null, ' '))
  //     myLunches.push(lunchData)
  //  });

 module.exports = {
// export default {
  components: {
    'data-grid': httpVueLoader('./data-grid.vue'),
  },
  props: ["db"],
  data: function() {
        return {
          text: "text me",
          name: "Brian",
          today: new Date(),
          title: "Monthly Lunch",
          description: "A prototype for Monthly Lunch",
          content: "list",
          sortKey: "date",
          runningDate: '',
          grid: false,
          gridColumns: ["name",'owner', 'date','addr'],
          searchQuery: '',
          new_lunch: {
            "name": 'Lurcat ',
            "owner": 'Brian',
            "date": '2018/04/24',
            "addr": '12913 Pioneer rd',
          }
         // lunches: []
        }
    },
    firebase: function () {
    return {
       lunches: db.ref().child('web/data/lunches')
  //     console.log(`lunches: ${JSON.stringify(lunches, null, 3))}`)
    }
  },
  //    firebase: {
  //         lunches: this.db.ref().child('web/data/lunches')
    
  // },
    computed: {
      dateChanged: function(d){
        lunch.date != runningDate ? lunch.date : null
      },
      lunchCount: function(){
        console.log("lunchCount computed.........")
        return this.lunches.length
      },
      filterLunches: function(){
        debugger
        var data = this.lunches
        var sortKey = this.sortKey
        var filterKey = null//this.filterKey && this.filterKey.toLowerCase()
        var order = -1//this.sortOrders[sortKey] || 1
        // var data = this.data
          if (filterKey) {
            data = data.filter(function (row) {
              return Object.keys(row).some(function (key) {
                return String(row[key]).toLowerCase().indexOf(filterKey) > -1
              })
            })
          }
          if (sortKey) {
            data = data.slice().sort(function (a, b) {
              a = a[sortKey]
              b = b[sortKey]
              return (a === b ? 0 : a > b ? 1 : -1) * order
            })
          }
          // create section for dates
          var section = ''
          data = data.map(function(lunch){
            var year = lunch.date.split('/')[0]
            if (section != year){
              section = year
              lunch.section = year
            }
            return lunch
          })
          return data
      }
    },
    methods: {
      saveLunch: function(){
        console.log(`saving lunch: ${JSON.stringify(this.new_lunch, null, 4)}`)
      },
      changed: function(e){
       // debugger
        this.chg = e.target.innerText;
      },
       startTimer: function(){

        var self = this;
        setInterval(function(){
          self.today = new Date()
        },1000)
      },
    },
    created(){
      // debugger
      this.startTimer();
  //     var self = this;
  //     console.log("getting lunches..................................")
  //     this.db.ref().child('web/data/lunches').on('value', function(snap) {
  //    // this.lunches.on('value', function(snap) {
  //  //console.log('lunchData:' + JSON.stringify(snap.val(), null, ' '))
  //     self.lunches = snap.val()
  //  });

    }
}
</script>
<style>
  *, *:before, *:after {
    box-sizing: border-box;
  }
body {
    margin: 0;
    padding: 0;
  }
  #home{
    width: 100%;
    height: 80%;
  }
  .sidebar {
          grid-area: sidebar;
      }
  .sidebar a{
          cursor: pointer;
          font-size: 14px;
          width: 3em;
          height: 1em;
          border: 1px solid black;
          background: green;
          padding: 5px;
  }
      .sidebar2 {
          grid-area: sidebar2;
      }
  
      .content {
          grid-area: content;
      }
  
      .header {
          grid-area: header;
      }
  
      .footer {
          grid-area: footer;
      }
  
      .wrapper {
        background-color: #fff;
        color: #444;
        margin: 0 auto;
      }
  
    .wrapper {
      display: grid;
      grid-gap: 1em;
      grid-template-areas:
       "header"
       "sidebar"
       "content"
       "sidebar2"
       "footer"
    }
  
    /* @media only screen and (min-width: 500px)  {
      .wrapper {
          grid-template-columns: 20% auto;
          grid-template-areas:
          "header   header"
          "sidebar  content"
          "sidebar2 sidebar2"
          "footer   footer";
      }
    } */
  
  @media only screen and (min-width: 600px)   {
      .wrapper {
          grid-gap: 20px;
          grid-template-columns: 2fr 10fr 1fr;
          /* grid-template-columns: 120px auto 120px; */
          grid-template-areas:
            "header  header  header"
            "sidebar content sidebar2"
            "footer  footer  footer";
          max-width: 900px;
      }
  } 
  
  .box {
    background-color: #444;
    color: #fff;
    border-radius: 5px;
    padding: 10px;
    font-size: 0.8em; 
  }
  
  .header,
  .footer {
    background-color: #999;
  }
  
  .sidebar2 {
    background-color: #ccc;
    color: #444;
  }
  .lunches{
      margin: 2px;
      padding: 3px;
      background: white;
      color: black;
      border: 1px solid black;
      font-size: 8px;
    }

</style>

