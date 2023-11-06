<template>


  <div class = "Table-Wrapper">
    <!-- Hide By status Bar -->
    <div class="HeadingDetails">
    <div class="hideBar">
      <label class="hideLabel"> Hide: </label>
      <div class="checkbox">
        <!-- All status -->
        <div class="AllStatusCheckbox">
        <input
          :id="productDataBystatus.status"
          type="checkbox"
          class="styled"
          :value="productDataBystatus.status"
          @click="hideShowALLstatus"
          v-model="hidestatus"
        />
        <label :for="productDataBystatus.status">All statuses</label>
        </div>

        <!-- Dynamic status -->
        <div class="styled-checkboxes" v-for="status in ['Announced', 'Launched', 'Discontinued', 'Launched (with IPU)']" :key="`${status}`">
          <input
            :id="`${status}`"
            type="checkbox"
            class="styled"
            @click="firstpage"
            :value="status"
            v-model="hidestatus"
          />
          <label :for="`${status}`"> 
             {{ status }}
             </label>
        
        </div>
       
      </div>
       
    </div>
    <div class="styled-pagination">
          <button class="styled-button" @click="firstpage">1</button>
          <button class="styled-button" @click="prevPage">&lt</button> 
          <button class="styled-button" @click="nextPage">></button>   
          <button class="styled-button" @click="lastPage">>></button>
    </div>
    </div>
    <div class="form">
      <label class="search-label">Search:</label>
      <input v-model="searchTerm" class="search-input"/>
    </div>
    <!-- Main Table Design -->
    <table>
      <thead>
        <tr >
          <td :colspan="12" class = "TableHeading">Dashboard SLA</td>
        </tr>
        <tr >
          <th colspan="3">{{ wwData }}</th>
          <th colspan="8">Product Info</th>
        </tr>
        <tr >
          <th>Status</th>
          <th>Cores</th>
          <th class="width1">Product</th>
          <th class="width1">Lithography</th>
          <th>Threads</th>
          <th>Base Freq</th>
          <th>Max Turbo Freq</th>
        </tr>
      </thead>
      <tbody>
        <template v-for="(data, status, index) in productDataBystatus.data">
          <!-- status -->
          <tr>
            <td class="width1"  :class="`status-${status.replace(/[\s()]+/g, '-')}`" :rowspan="calstatusRowspan(data)">
              {{ status }}
            </td>
          </tr>

          <template v-for="cores in Object.keys(data)">
            <!-- cores -->
            <tr>
              <td class="width1" :rowspan="Object.keys(data[cores]).length + 1">
                {{ cores }}
              </td>
            </tr>

            <tr v-for="(v, k) in data[cores]">
              <!-- product -->
              <td class="productColumn">{{ v.Product }}</td>

              <!-- Lithography -->
              <td>{{ v.Lithography }}</td>

              <!-- Threads -->
              <td>
                <div class="innerCells">
                  <input :value="v.Threads" :disabled="true" type="text" />
                </div>
              </td>

              <!-- Base Freq -->
              <td>
                <div class="innerCells">
                  <input :value="v.Base_Freq" :disabled="true" type="text" />
                </div>
              </td>

              <!-- Max Turbo Freq -->
              <td>
                <div class="innerCells">
                  <input :value="v.Max_Turbo_Freq" type="text" :disabled="true" />
                </div>
              </td>
            </tr>
          </template>
        </template>
      </tbody>
     
    </table>
    <!-- End of Table Design -->
  </div>
</template>


<script>
import data from "../assets/data.json";

export default {
  components:{
   
  },
  data: function () {
    return {
      hidestatus: [],
      allCheckBox: [],
      UIData: [],
      sliced_data:[],
      wwInfo: {},
      allCheck: false,
      selectedList: [],            // Your full list of items
      itemsPerPage: 100,    // Number of items per page  
      perPage: 100,
      pageSize:3,
      currentPage: 1,
      count : 0 ,
      searchTerm:""
    
    };
  },
  mounted() {
    this.UIData = data;
    this.wwInfo = this.getWWFromDate();
  },
  computed: {
    
    wwData() {
      return `${this.wwInfo.year}WW${this.wwInfo.workweek}.${this.wwInfo.numofday}`;
    },

    productDataBystatus() {
      let tmp = {};
      const startIndex = (this.currentPage - 1) * this.perPage;
      const endIndex = startIndex + this.perPage;
     
      // let data = this.UIData;
      let slice = [];
      let i = 0 
      
      
      for (const c of this.UIData){
        
        if(!this.searchTerm){
        
        if (!this.hidestatus.includes(c.Status)){
          
          slice[i] = c
          
        }}
        else if(c.Product.toLowerCase().includes(this.searchTerm.toLowerCase())){
          
          slice[i]=c;
        }
        i+=1;
      }
      
      let data = slice.slice(startIndex, endIndex)
     // sort status in order

     
      let statusSet = new Set();
      let coreSet = new Set();
      data.forEach((element) => {
        let status = element.Status;
        let cores = element.Cores;

        // push status to set
        statusSet.add(status);
        coreSet.add(cores)
        if (this.hidestatus.includes(status)) return; // Hide by status
        if (!tmp[status]) {
          tmp[status] = {};
          
        }
        if (!tmp[status][cores]) tmp[status][cores] = [];
        tmp[status][cores].push(element);
      });
      
      const strings = new Set(statusSet);
      const sortedStringsArray = [...strings].sort();
      statusSet = new Set(sortedStringsArray);

      return {
        status: [...statusSet],
        data: tmp

      };
    },

  },
  methods: {
    calstatusRowspan(data) {
      let sum = Object.keys(data).length + 1;
      for (const cores in data) {
        sum += Object.keys(data[cores]).length;
      }
      return sum;
    },
    getWWFromDate(date = null) {
      let currentDate = date || new Date();
      let startDate = new Date(currentDate.getFullYear(), 0, 1);
      let days = Math.floor((currentDate - startDate) / (24 * 60 * 60 * 1000));

      return {
        year: currentDate.getFullYear(),
        workweek: Math.ceil(days / 7),
        numofday: currentDate.getDay(),
      };
    },
    hideShowALLstatus() {
      if (!document.querySelector(".styled").checked) {
        this.hidestatus = [];
        this.allCheckBox = [];
      }

      if (document.querySelector(".styled").checked) {
        this.hidestatus = this.productDataBystatus.status;
        this.allCheckBox = this.productDataBystatus.status;
      }

      this.allCheck = !this.allCheck;

      if (this.allCheck) {
      } else {
        this.hidestatus = [];
        this.allCheckBox = [];
      }
      this.currentPage = 1
    },
    firstpage(){
      this.currentPage = 1
    },
    nextPage() {
      let total_length = Object.keys(data).length + 1
      if((this.currentPage*this.perPage) < total_length) this.currentPage++;
      // this.count = 0 
    },
    prevPage() {
      if(this.currentPage > 1) this.currentPage--;
      // this.count = 0 
    },
    lastPage(){
      this.currentPage = Math.floor(Object.keys(data).length/100)
    }
    },
};
</script>


<style scoped>
.fas.fa-times {
  display: none;
}

.fas.fa-times.comment {
  display: block;
}

.overWrittenCells:hover .fas {
  display: block;
}

.innerCells {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}

.innerCells.comment {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 15px;
}

.Table-Wrapper{
padding: 10px 60px;
}
table {
  width: 100%;
  white-space: nowrap !important;
  /* border-collapse: collapse; */
}

table td {
  position: relative;
  background-color: #f2f2f2;
  border: 1px solid #ccc;
  
}

i {
  cursor: pointer;
}

.legendColorBox {
  margin: 0.4%;
  float: left;
  height: 20px;
  width: 30px;
  border: 1px solid grey;
  margin-right: 4%;
}

.inputBox {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  text-align: center;
  border: 0;
  text-transform: uppercase !important;
}

.inputBoxOverWritten {
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  text-align: center;
  border: 0;
  width: 80px;
  text-transform: uppercase !important;
  background: none !important;
}

.overWrittenCells {
  border: 2px solid rgb(194, 1, 1);
}

.overWrittenCells input {
  outline: 0;
}

input::placeholder {
  color: black;
}

input:focus::-webkit-input-placeholder {
  color: grey;
}

input[disabled] {
  cursor: text;
  background-color: inherit;
  color: black;
}

.legend-labels {
  white-space: nowrap !important;
  padding: 0%;
  display: flex;
  list-style-type: none;
  margin-bottom: 5px;
}

.legend-labels li {
  font-size: small;
  margin-right: 2%;
}

select {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  text-align: center;
  border: 0;
}

table tr td:not(.skip),
table tr th {
  text-align: center;
  border: 1px solid #ccc;
}

td,
th {
  padding: 2px !important;
  width: 100px;
  /* border: 1px solid black; */
}

.reference {
  width: 1%;
  background-color: #00b0f0;
}

.released {
  width: 1%;
  background-color: #7fff00;
}

.partial {
  width: 1%;
  background-color: #ffa500;
}

.tentative {
  width: 1%;
  background-color: #dcdcdc;
}

.planned {
  width: 1%;
  background-color: #82ffac;
}

.hideBar {
  list-style: none;
  display: flex;
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  color: #192c55c2;
  /* padding-left: 30px; */
}

.productColumn {
  width: 1%;
  background-color: white;
}

.HeadingDetails{
  display: flex;
  justify-content: space-between;
  /* padding-bottom: 30px; */
}
.checkbox {
  /* background-color: #4273DE; */
  /* color: #ccc; */
  display: flex;
  /* padding-left: 30px; */
  
}
.hideLabel{
  display: flex; /* Display checkboxes in a horizontal line */
  padding: 10px; /*Add padding to the container*/
  font-weight: 500;
  font-size: large;
}
.AllStatusCheckbox{
  display: flex; /* Display checkboxes in a horizontal line */
  padding: 10px; /*Add padding to the container*/
}
.styled-checkboxes {
  display: flex; /* Display checkboxes in a horizontal line */
  padding: 10px; /*Add padding to the container*/
}
.styled {
  display: none; /* Hide the default checkbox input */
  /* padding: 50px; */

}
.styled + label {
  position: relative;
  padding-left: 30px; /* Add space for a custom checkbox */
  cursor: pointer;

}

.styled + label:before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  width: 20px;
  height: 20px;
  border: 2px solid #007bff; /* Checkbox border color */
  background-color: #fff; /* Checkbox background color */
}

.styled:checked + label:before {
  background-color: #007bff; /* Checked checkbox background color */
}

.styled:checked + label:after {
  content: '✔'; /* Checkmark symbol */
  position: absolute;
  top: 1px;
  left: 5px;
  color: #fff; /* Checkmark color */
}

.redActual {
  width: 1%;
  color: red;
  background-color: #dcdcdc;
}

.width1 {
  width: 1%;
  /* white-space: nowrap !important; */
}
.TableHeading{

  background:lightblue; 
  border: 1px solid #ccc; 
  font-family:\'Roboto\' ;
  font-size: 40px;
}
.status-Announced {
    background-color: lightgreen;
  }

  .status-Launched {
    background-color: lightblue;
  }

  .status-Discontinued {
    background-color: lightcoral;
    
  }
  .status-Launched-with-IPU- {
    background-color: rgb(215, 183, 69);
  }
.styled-pagination{
   /* Display checkboxes in a horizontal line */
  padding: 0px; /*Add padding to the container*/
  justify-content: flex-end; /* Right-align the buttons */
}
  .styled-button {
  background-color: #007bff; /* Button background color */
  color: #fff; /* Button text color */
  padding: 10px 20px; /* Padding around the text */
  border: none; /* Remove the button border */
  border-radius: 5px; /* Rounded corners */
  cursor: pointer; /* Show a hand cursor on hover */
  font-size: 16px; /* Font size */
  margin: 5px; /* Margin around the buttons */
  transition: background-color 0.3s ease; /* Smooth background color transition */
  top: 0px
}

.styled-button:hover {
  background-color: #0056b3; /* Button background color on hover */
}
.form{
  font-family :system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  Padding: 10px
}
.search-label {
  /* font-weight: bold; */
  font-family :system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;

  margin-right: 5px;
}
.search-input {
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
  width: 200px;
}
</style>