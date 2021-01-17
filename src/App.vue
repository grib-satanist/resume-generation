<template>
  <app-alert :alert="alert" @close="alert = null"></app-alert>
  <div class="container column">
    <form class="card card-w30"  @submit.prevent="createResume">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="blockName">
          <option value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" v-model="blockValue" rows="3"></textarea>
      </div>

      <button class="btn primary" :disabled="disableBtn">Добавить</button>
    </form>

    <div class="card card-w70">
      
      <component
        v-for="item in blocks"
        :key="item"
        :is="'app-' + item.blockName"
        :image="item.blockValue"
      >{{item.blockValue}}</component>
      
      <h3 v-if="blocks.length === 0">Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>
  <app-comments :comments="comments" @load="loadComments"></app-comments>
  <app-loader v-if="loading"></app-loader>
</template>

<script>
import AppSubtitle from './AppSubtitle'
import AppTitle from './AppTitle'
import AppAvatar from './AppAvatar'
import AppText from './AppText'
import AppComments from './AppComments'
import AppLoader from './AppLoader'
import AppAlert from './AppAlert'

export default {
  data() {
    return {
      blockName: 'title',
      blocks: [],
      comments: [],
      blockValue: '',
      loading: false,
      alert: null
    }
  },
  mounted() {
    this.loadResume()
  },
  methods: {
    async createResume() {
      const response = await fetch('https://vue-hhtp-52a5d-default-rtdb.firebaseio.com/resume.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          blockName: this.blockName,
          blockValue: this.blockValue
        })
      })

      this.blocks.push({
        blockName: this.blockName,
        blockValue: this.blockValue
      })

      this.blockValue = ''
    },
    async loadResume() {
      try {
        this.loading = true
        const response = await fetch('https://vue-hhtp-52a5d-default-rtdb.firebaseio.com/resume.json')
        
        const commentsData = await response.json()
        if (!commentsData) {
          throw new Error('На сервере нет данных ((')
        }
        this.blocks = Object.keys(commentsData).map( key => {
          return {
            ...commentsData[key]
          }
        })
        this.loading = false
      } catch(e) {
        
        this.loading = false
        this.alert = {
          type: 'danger',
          title: 'Ошибка!',
          text: e.message
        }
      }
    },
    async loadComments() {
      try {
        this.loading = true
        const response = await fetch('https://jsonplaceholder.typicode.com/comments?_limit=42')

        const commentsData = await response.json()
        this.comments = commentsData.map( key => {
          return {
            email: key.email,
            text: key.body
          }
        })
        this.loading = false
      } catch(e) {
        this.loading = false
        this.alert = {
          type: 'danger',
          title: 'Ошибка!',
          text: e.message
        }
      }
    }
  },
  computed: {
    disableBtn() {
      return this.blockValue.length <= 3
    }
  },
  components: {AppSubtitle, AppTitle, AppAvatar, AppText, AppComments, AppLoader, AppAlert}

}
</script>

<style>
  .avatar {
    display: flex;
    justify-content: center;
  }

  .avatar img {
    width: 150px;
    height: auto;
    border-radius: 50%;
  }
</style>
