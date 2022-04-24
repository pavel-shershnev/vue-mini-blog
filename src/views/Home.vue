<template>
<div>
  <app-alert v-if="alert" :message="alertMessage"></app-alert>
  <app-dialog
  :dialog="dialog"
  @closeDialog="dialog = !dialog"
  :posts="posts"
  :toolBarTitle="toolBarTitle"
  :typeDialog="typeDialog"
  @alertCreate="showAlertCreate"
  ></app-dialog>
  <v-container v-show="!dialog">
    <div class="text-center mt-10" v-if="posts.length === 0">
      <p>
        Не опубликовано ни одного поста ...
      </p>
    </div>
    <div  v-else>
      <v-card
        elevation="9"
        outlined
        class="my-10 mx-5"
        v-for="(item, idx) in posts" :key="idx"
      >
        <v-card-title>
          {{item.title}}
          <v-spacer></v-spacer>
          <v-icon
            large
            color="green darken-2"
          >
            mdi-comment-text-outline
          </v-icon>
          &nbsp;{{item.comments.length}}
        </v-card-title>
        <v-card-text>
          {{item.description}}
        </v-card-text>
        <v-card-actions>
          <v-btn
            text
            color="green accent-4"
            @click="openPost(item.id)"
          >
            Читать
          </v-btn>
        </v-card-actions>
      </v-card>
    </div>
    <div class="text-center">
      <v-btn color="green" @click="addPost">Добавить пост</v-btn>
    </div>
  </v-container>
</div>
</template>
<script>
import appDialog from '../components/appDialog.vue'
import appAlert from '../components/appAlert.vue'

export default {
  name: 'Home',
  data() {
    return {
      dialog: false,
      alert: false,
      alertMessage: '',
      typeDialog: 'create',
      posts: [],
      toolBarTitle: ''
    }
  },
  methods: {
    addPost() {
      this.dialog =! this.dialog
      this.toolBarTitle = 'Создание нового поста'
    },
    openPost(id) {
      this.$router.push(`/post/${id}`)
    },
    showAlertCreate() {
      this.alert = true
      this.alertMessage = 'создан новый пост'
    }
  },
  components: {
    appDialog, appAlert
  },
  mounted() {
    this.posts = JSON.parse (localStorage.getItem ("posts")) || [];
  },
}
</script>
