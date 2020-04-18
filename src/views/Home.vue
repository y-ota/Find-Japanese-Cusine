<template>
  <div class="hello">
    <h1>Find your favorite <span class="title-highlight">Japanese</span> Cusine</h1>
    <select v-model="selected" @change="onChange($event)">
        <option v-for="option in options" v-bind:value="option.name" v-bind:key="option.id">
            {{ option.name }}
        </option>
    </select>
    <div class="show-result">
      <div class="title">{{ category }}</div>
      <div class="result" v-for="menu in menuList" v-bind:key="menu.id">
        <div>* {{ menu.name }}</div>
        <img width=200px height=200px src="https://d1f5hsy4d47upe.cloudfront.net/fb/fb68e3a3989975582120cdc9619c9528_t.jpeg"/>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import { jexiaClient, dataOperations, field } from "jexia-sdk-js/browser"; 

export interface Menu {
  id: string;
  name: string;
  category: string;
  url: string;
}

@Component
export default class Home extends Vue {
    category = "";
    menuList: Menu[] = [];
    selected =  '';
    options =  [
        { id: 1, name: 'Pot' },
        { id: 2, name: 'Bowl' },
        { id: 3, name: 'Fish' },
        { id: 4, name: 'Meat' },
        { id: 5, name: 'Noodles' },
      ]


    onChange(event){
      this.menuList = [];
      this.category = event.target.value;
      const ds = dataOperations();

      jexiaClient().init({
        projectID: "7a46b668-15e4-4d67-8f12-a2fb2cc41e0e",
        key: "0881c9e1-13d7-450f-b854-69e3f914afcd",
        secret: "UQhEH+JvH3MWAm5MJa3naqgwEdfy6NbTIUphObjVzA4/YvA5Ql4jOzvAOnrDlciIsM5p5K2rrpVpd9cyO/rL1w==",
      }, ds);

      const orders = ds.dataset("menu");
      const selectQuery = orders
        .select()
        .where(field => field("category").isEqualTo(this.category ));

      selectQuery.subscribe(records => { 
          // You will always get an array of created records, including their 
          // generated IDs (even when inserting a single record) 
          console.log(records);

          this.menuList = records;
        }, 
        error => { 
          // If something goes wrong, the error information is accessible here 
      }); 
    }


}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.title-highlight {
  color: #FF0461;
  font-size: 2.5rem;
}

select {
  width: 20rem;
  height: 2rem;
  background-color: #FFF;
}

.show-result{
  font-size: 20px;
  margin: auto;
  position: relative;
  top: 50px;
  width: 500px;
  text-align: left;
  line-height: 40px;
}
.show-result .title{
  font-size: 35px;
  padding-bottom: 10px;
  padding-left: 20px;
  border-bottom: 1px solid;

}

.show-result .result{
  display: flex;
  padding-top: 15px;
  height: 210px;
  border-bottom: 1px solid;
}

.show-result .result img{
  margin-right: 0;
  margin-left: auto;
}

</style>
