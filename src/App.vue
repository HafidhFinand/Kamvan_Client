<template>
  <div>
      <navbar 
        :isLogin="isLogin" 
        @showRegister="showRegister" 
        @showLogin="showLogin"
        @logout="logout" 
      >
      </navbar>
      <register :currentPage="currentPage" @changeToLogin="changeToLogin"></register>
      <login :currentPage="currentPage" :isLogin="isLogin" @changeLogin="changeLogin"></login>
      <div class="container" v-show="currentPage === 'dashboard'">
        <div class="row">
          <listKanban v-for="(category, i) in categories" :key="category.id" :category="category" :tasks="kanbans[i]" @showAddForm="showAddForm" @showEditForm="showEditForm" @fetchKanban="fetchKanban" @changeLogin="changeLogin" @checkLogin="checkLogin"></listKanban>
        </div>
      </div>
      <kanbanAdd @fetchKanban="fetchKanban" @changeLogin="changeLogin" v-show="currentPage === 'addKanban'"></kanbanAdd>
      <kanbanUpdate @fetchKanban="fetchKanban" @changeLogin="changeLogin" :activity="activity" v-show="currentPage === 'editKanban'"  id="edit-form"></kanbanUpdate>
  </div>
</template>

<script>
import navbar from './components/Navbar';
import register from './components/Register';
import login from './components/Login';
import listKanban from './components/ListKanban';
import kanbanAdd from './components/KanbanAdd';
import kanbanUpdate from './components/KanbanUpdate';
import socket from './config/socket'

export default {
    name: 'App',
    data() {
      return {
        isLogin: '',
        currentPage: 'login',
        categories: ['Backlog', 'ToDo', 'Doing', 'Done'],
        kanbans: [],
        activity: '',
        newData: []
      }
    },
    components: {
      navbar,
      register, 
      login,
      listKanban,
      kanbanAdd,
      kanbanUpdate,
    },
    methods: {
      // Menampilkan register form
      showRegister() {
        this.currentPage = 'register';
      },
      // Menampilkan login form
      showLogin() {
        this.currentPage = 'login';
      },
      // Proses logout dan clear token di browser
      logout() {
        localStorage.clear();
        this.isLogin = false;
        this.currentPage = 'login';
      },
      // Merubah isLogin ketika sudah berhasil login
      changeLogin(input) {
        this.isLogin = input;
        this.fetchKanban();
        this.currentPage = 'dashboard';
        this.checkLogin();
      },
      // Mengecek apakah sudah login atau belum
      checkLogin() {
        if (localStorage.getItem('token')) {
          this.isLogin = true;
          this.currentPage = 'dashboard';
        }
      },
      // Menarik data dari server 
      fetchKanban() {
        this.kanbans = [];
        const axios = require('axios');
        axios.get('http://localhost:3000/kanbans', {
          headers: {
            token: localStorage.token
          }
        })
          .then(kanban => {
            let { data } = kanban;
            kanban = data;
            const Backlog = kanban.filter(el => el.status === 'Backlog');
            const ToDo = kanban.filter(el => el.status === 'ToDo');
            const Doing = kanban.filter(el => el.status === 'Doing');
            const Done = kanban.filter(el => el.status === 'Done');
            this.kanbans.push(Backlog)
            this.kanbans.push(ToDo)
            this.kanbans.push(Doing)
            this.kanbans.push(Done)
          })
          .catch(err => {
            err = err.response;
            console.log(err);
          })
      },
      // Menampilkan kanban add form
      showAddForm(input) {
        this.currentPage = input;
      },
      // Menampilkan edit kanban form
      showEditForm(input) {
          this.activity = input;
          this.currentPage = 'editKanban';
          checkLogin();
      },
      // Menampilkan form login setelah success register
      changeToLogin(input) {
        this.currentPage = input
      }
    },
    watch: {
      newData () {
        this.fetchKanban()
      }
    },
    created() {
      this.fetchKanban();
      this.checkLogin();
      socket.on('fetch-kanban', (data) => {
        this.newData = data
      })
    }
}
</script>

<style>
  .form-container {
    width: 500px;
    margin: auto;
  }
  .main-container {
    width: 90%;
    margin: auto;
    display: flex;
    justify-content: center;
  }

  * {
    font-size: 15px;
  }
</style>