<template>
  <div class="home__table__container">
    <div class="row">
      <div class="col-4">
        <label for="colSelection" class="form-label">Колонка</label>
        <select class="form-select" v-model="selectedCol" id="colSelection" aria-label="Default select example">
          <option value="Date">Date</option>
          <option value="Name">Name</option>
          <option value="Amount">Amount</option>
          <option value="Distance">Distance</option>
        </select>
      </div>
      <div class="col-4">
        <label for="conditionSelection" class="form-label">Условие</label>
        <select class="form-select" v-model="selectedCondition" id="conditionSelection" aria-label="Default select example">
          <option value="=">=</option>
          <option value=">">></option>
          <option value="<"><</option>
          <option value="contain">Contain</option>
        </select>
      </div>
      <div class="col-4">
        <label for="filterValueField" class="form-label">Значение условия</label>
        <input type="text" v-model="filterVal" @input="filter()" id="filterValueField" class="form-control" placeholder="Значение">
      </div>
    </div>
    <table class="table">
      <thead>
        <tr>
          <th scope="col">Date</th>
          <th scope="col" @click="sortData('name'); sortType = !sortType">Name</th>
          <th scope="col" @click="sortData('amount'); sortType = !sortType">Amount</th>
          <th scope="col" @click="sortData('distance'); sortType = !sortType">Distance</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in perPageData">
          <th scope="row">{{item.date}}</th>
          <td>{{item.name}}</td>
          <td>{{item.amount}}</td>
          <td>{{ item.distance }}</td>
        </tr>
      </tbody>
    </table>
    <ul class="pagination">
      <li class="page-item" v-for="page in pages">
        <a class="page-link" @click="changePage(page)">{{page}}</a>
      </li>
    </ul>
  </div>
</template>

<script>
import {tableDataAsset} from "@/assets/data";
export default {
  name: 'HomeView',
  data() {
    return {
      pageNumber: 1,
      itemsPerPage: 5,
      tableData: tableDataAsset,
      sortType: true, // true - descending false - ascending
      selectedCol: '',
      selectedCondition: '',
      filterVal: ''
    }
  },
  computed: {
    pages() {
      return (Math.ceil(this.tableData.length / this.itemsPerPage));
    },
    perPageData() {
      let startPosition = this.itemsPerPage * (this.pageNumber - 1);
      let endPosition = startPosition + this.itemsPerPage;
      return this.tableData.slice(startPosition, endPosition);
    }
  },
  methods: {
    changePage(number) {
      this.pageNumber = number;
    },
    sortData(sortBy) {
      this.tableData.sort((a,b) => {
        return this.sortType ? this.compareSort(a[sortBy], b[sortBy]) : this.compareSort(b[sortBy], a[sortBy])
      })
    },
    compareSort(a, b) {
      if(typeof a == 'number') {
        return a - b;
      }
      return a.localeCompare(b)
    },
    filter() {
      const buffer = tableDataAsset;
      let subLayer = {
        '=': function (a,b) {
          return  buffer.filter((item) => {
            return item[a] == b;
          })
        },
        '>': function (a,b) {
          return  buffer.filter((item) => {
            return item[a] > b;
          })
        },
        '<': function (a,b) {
          return  buffer.filter((item) => {
            return item[a] < b;
          })
        },
        'contain': function (a,b) {
          return  buffer.filter((item) => {
            return item[a].toString().includes(b.toLocaleString());
          })
        }
      }
      this.tableData = subLayer[this.selectedCondition](this.selectedCol.toLowerCase(), this.filterVal)
    },
  }
}
</script>
