<template>
  <div id="app">
    <h1 v-text="title"></h1>
    <h2>Reflected XSS</h2>
    <form method="get" >
      <input v-model="query" name="query" />
      <input type="submit" value="Submit" />
    </form>
    <p>
      You are searching <span v-html="getQueryString"></span>
    </p>
    <br/>
    <h2>Persistent XSS</h2>
    <input v-model="newItem" v-on:keyup.enter="addNew" />
    <div>
      <h3>Your new comment: (You can use "Enter" to submit the comment)</h3>
      <p v-html="newItem"></p>
    </div>

    <div>
      <h3>Comment List Using Raw HTML (Dangerous)</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
         <span v-html="item.label"></span>
        </li>
      </ul>
    </div>

    <div>
      <h3>Comment List Interpreting as Plaintext (More secure)</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
         {{item.label}}
        </li>
      </ul>
    </div>

    <div>
      <h2>Try following inputs: </h2>
      <ul>
        <li v-for="item in comment" :key="item.id">
         {{item.label}}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import Store from './store'
// import ComponentA from './components/componentA'

export default {
  data: function(){
    return {
      title: 'Comment Board',
      items: Store.fetch(),
      query: '',
      newItem:'',
      idCounter: 0,
      comment: [
        { 
          id: 0,
          label: 'Dog'
        },
        { 
          id: 1,
          label: '<a style="color: #ff0000" href="http://google.com" target="_blank">Here!</a>'
        },
        { 
          id: 2,
          label: '<button onclick="alert(123)">Here!</button>'
        },
        { 
          id: 3,
          label: '<a href="http://google.com" target="_blank">Here!</a>'
        },
      ]
    }
  },
  // components: { ComponentA },
  watch:{
    items: {
      handler: function(items){
        Store.save(items)
      }, 
      deep: true
    }
  },
  computed: {
    getQueryString: function(){
      name = 'query'
      let reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)")
      let r = window.location.search.substr(1).match(reg)
      if (r!=null) {
        console.log(decodeURIComponent(r[2]).replace(/\+/g, ' '))
        return decodeURIComponent(r[2].replace(/\+/g, ' '))
      } else {
        return null
      }
    }
  },
  methods:{
    addNew(){
      this.items.push({
        label: this.newItem,
        id: this.idCounter++
      })
      this.newItem=''
      // this.$broadcast('onAddnew',this.items)
    }, 
    
  }
}
</script>

<style>
h1{
  color:green;
}
</style>

