<template>
<div><nav class="panel column is-offset-2 is-8">
  <p class="panel-heading">
    PhoneBook
     <button class="button is-link is-outlined" @click="openAdd">
      Add New
    </button>
  </p>
  <div class="panel-block">
    <p class="control has-icons-left">
      <input class="input is-small" type="text" placeholder="search" v-model="search">
      <span class="icon is-small is-left">
        <i class="fa fa-search" aria-hidden="true"></i>
      </span>
    </p>
  </div>
  
  <a class="panel-block is-active" v-for="item,key in temp ">
    <span class="column is-9">
    {{item.name}}
    </span>
    <span class="panel-icon column is-1" @click="updatedetails(key)">
      <i class="has-text-info fa fa-edit" aria-hidden="true"></i>
    </span>
    <span class="panel-icon column is-1" @click="del(key,item.id)">
      <i class="has-text-danger fa fa-trash" aria-hidden="true"></i>
    </span>
    <span class="panel-icon column is-1" @click="showdetalis(key)">
      <i class="has-text-primary fa fa-eye" aria-hidden="true"></i>
    </span>
  </a>
  
  <div class="panel-block">
   
  </div>
</nav>

<Add :openmodel='addActive' @closeRequest="close"></Add>
<Show :openmodel='showActive' @closeRequest="close"></Show>
<Update :openmodel='updateActive' @closeRequest="close"></Update>

</div>
    
</template>
<script>
let Add = require('./Add.vue').default
let Show = require('./Show.vue').default
let Update = require('./Update.vue').default
export default {
  components:{Add,Show,Update},
  data(){
return{
  showActive:'',
  addActive : '',
  updateActive : '',
  lists:{},
  search:'',
  temp:'',
}
  },
  mounted(){
    axios.post('/getData')
    .then((response)=>this.lists = this.temp = response.data)
            .catch((error)=>this.errors = error.response.data.errors)
  },
  watch:{
        search(){
          if(this.search.length > 0){
            this.temp = this.lists.filter((item)=>{
              return Object.keys(item).some((key)=>{
                let string = String(item[key])
              return string.toLowerCase().indexOf(this.search.toLowerCase())>-1

              })
            });
           
          }else{
            this.temp = this.lists
          }
        }
  },
  methods:{
    openAdd(){
      this.addActive = "is-active";
    },
    close(){
      this.addActive =this.showActive=this.updateActive= "";
    },
    showdetalis(key){
      this.$children['1'].list = this.lists[key];
       this.showActive = "is-active";
    },
    updatedetails(key){
      this.$children[2].list = this.lists[key];
       this.updateActive = "is-active";
    },
    del(key,id){
      if(confirm("Are you sure ?")){
             axios.delete(`/phonebook/${id}`).then((response)=>this.lists.splice(key,1))
            .catch((error)=>this.errors = error.response.data.errors)
      }
 
    }
  }
}
</script>