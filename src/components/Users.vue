<template>
  <div class="container">
    <div class="container center">
      <table class="table table-hover">
        <thead>
          <tr>
            <th scope="col">ID</th>
            <th scope="col">Login</th>
            <th scope="col"></th>
            <th scope="col"></th>
          </tr>
        </thead>
        <tbody v-for="user in users" :key="user.id">
          <tr>
            <td>{{ user.id }}</td>
            <td>{{ user.login }}</td>
            <td><button class="btn btn-primary" @click="userDetails(user.login)">Details</button></td>
            <td><button class="btn btn-primary" @click="userRepos(user.login)">Repos</button></td>
          </tr>
          <tr v-show="user.details">
            <td colspan="4">
              <div>
                {{ details }}
              </div>
            </td>
          </tr>
          <tr v-show="user.repos">
            <td colspan="4">
              <div>
                {{ repos }}
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <br>
    <div class="container">
      <button class="btn btn-primary">Previous</button>
      <button class="btn btn-primary">Next</button>
    </div>
    <br><br><br>
  </div>
</template>

<script>
// import Vue from 'vue'

export default {
  name: 'Users',
  props: {
    
  },
  data(){
    return{
      users: [],
      nextPage: "",
      repos: [],
      details: []
    }
  },
  mounted(){
    let self = this
    this.$http.get('http://localhost:8000/api/users').then((response) =>{
      response.body.forEach(user => {
        if(!user.next_page){
          self.users.push({login: user.login, id: user.id, details: false, repos: false})
        } else {
          self.nextPage = user.next_page
        }
      });
    })
  },
  methods: {
    userDetails(login){
      let self = this
      self.users.forEach(function(user){
        if(user.login == login){
          if(!user.details){
            self.$http.get('http://localhost:8000/api/users/'+ login +'/details').then((response) =>{
              self.details = {login: response.body.login, id: response.body.id, url: response.body.url, date: response.body.created_at}
              user.details = true
            })
          } else {
            user.details = false
          }
        }
      })
    },
    userRepos(login){
      let self = this
      self.users.forEach(function(user){
        if(user.login == login){
          if(!user.repos){
            self.$http.get('http://localhost:8000/api/users/'+ login +'/repos').then((response) =>{
              response.body.forEach(repos => {
                repos.forEach(repo => {
                  self.repos.push({name: repo.name, id: repo.id, url: repo.url})
                })
              });
              user.repos = true
            })
          } else {
            user.repos = false
          }
        }
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
