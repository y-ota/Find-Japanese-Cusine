<template>
  <div class="hello">
    <main>
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
          <img width=200px height=200px :src="menu.url"/>
        </div>
      </div>
    </main>
    <footer>© 2020 Yusuke Ota.</footer>
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
    selected = "";
    options =  [
        { id: 1, name: 'Pot' },
        { id: 2, name: 'Fish' },
        { id: 3, name: 'Meat' },
        { id: 4, name: 'Noodles' },
      ]
    ds = dataOperations();

    mounted() {
      const initCategory = 'Pot';
      this.category = initCategory;
      this.selected = initCategory;
      jexiaClient().init({
        projectID: "7a46b668-15e4-4d67-8f12-a2fb2cc41e0e",
        key: "0881c9e1-13d7-450f-b854-69e3f914afcd",
        secret: "UQhEH+JvH3MWAm5MJa3naqgwEdfy6NbTIUphObjVzA4/YvA5Ql4jOzvAOnrDlciIsM5p5K2rrpVpd9cyO/rL1w==",
      }, this.ds);

      this.getData(initCategory);
    }

    getData(category: string){
      this.menuList = [];
      this.category = category;

      const orders = this.ds.dataset("menu");
      const selectQuery = orders
        .select()
        .where(field => field("category").isEqualTo(this.category));

      selectQuery.subscribe(records => { 
          this.menuList = records;
        }, 
        error => { 
          console.error(error);
      }); 
    }

    onChange(event: any){
      this.getData(event.target.value);
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


footer {
  position: relative;
  top:90px;
  padding-bottom: 50px;
}

</style>
