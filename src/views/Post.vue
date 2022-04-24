<template>
  <div>
    <app-alert v-if="alert" :message="alertMessage"></app-alert>

    <app-dialog
    :dialog="dialog"
    @closeDialog="dialog = !dialog"
    :posts="posts"
    :toolBarTitle="toolBarTitle"
    :idxPostInArray="idxPostInArray"
    :post="post"
    :typeDialog="typeDialog"
    :key="key"
    @alertEdit="showAlertEdit"
    ></app-dialog>

    <v-container  v-show="!dialog">
      <confirm-dialog
      :confirm="confirmDialog"
      @confirmNo="confirmDialog=!confirmDialog"
      @confirmYes="confirmDelete"
      ></confirm-dialog>
      <!-- панель управления -->
      <v-toolbar
        dense
        elevation="4"
        flat
      >
        <v-btn
          class="ma-2"
          color="primary"
          dark
          @click="$router.push('../')"
        >
          <v-icon
            dark
            left
          >
            mdi-arrow-left
          </v-icon>Назад к постам
        </v-btn>
        <v-spacer></v-spacer>
        <v-btn
          color="red"
          depressed
          @click="deletePost"
        >
          <v-icon left>
            mdi-delete
          </v-icon>
          Удалить пост
        </v-btn>
      </v-toolbar>
      <!-- пост -->
      <v-card
        elevation="9"
        outlined
        class="my-10"
      >
        <v-card-title>
          {{post.title}}
          <v-spacer></v-spacer>
          <v-tooltip bottom>
            <template v-slot:activator="{ on, attrs }">
              <v-btn icon v-on="on">
                <v-icon
                  small
                  v-bind="attrs"
                  class="mr-2"
                  @click="editPost"
                >
                  mdi-pencil
                </v-icon>
              </v-btn>
            </template>
            <span>Редактировать пост</span>
          </v-tooltip>
        </v-card-title>
        <v-card-text>
          {{post.text}}
        </v-card-text>
      </v-card>
      <!-- список комментариев -->
      <span><strong>Комментарии</strong></span>
      <v-card min-height="100" >
        <div v-if="comments.length !== 0">
          <div  v-for="(item, idx) in post.comments" :key="idx">
            <v-card-title class="pb-0 pt-2 text-caption font-weight-bold">{{item.name}}
              <v-spacer></v-spacer>
              <v-tooltip bottom>
                <template v-slot:activator="{ on, attrs }">
                  <v-icon
                    @click="deleteMessage(idx)"
                    right
                    small
                    color="red"
                    v-bind="attrs"
                    v-on="on"
                    >
                      mdi-close
                  </v-icon>
                </template>
                <span>Удалить комментарий</span>
              </v-tooltip>

            </v-card-title>
            <v-card-text class="text-caption grey--text">{{item.message}}</v-card-text>
            <v-divider></v-divider>
          </div>
        </div>
        <span v-else><small>нет комментариев</small></span>
      </v-card>
      <br>
      <hr>
      <!-- форма отправки комментария -->
      <v-row>
        <v-col md="6">
          <div class="mt-10 mb-0">Новый комментарий</div>
          <v-form
            ref="form"
            v-model="valid"
            lazy-validation
          >
            <v-text-field
              @click="goBottom"
              class="mt-10"
              v-model="commentName"
              label="Имя"
              :rules="nameRules"
              outlined
              required
            ></v-text-field>
            <v-textarea
              rows="10"
              v-model="commentMessage"
              counter
              label="Текст комментария"
              outlined
            ></v-textarea>
            <v-btn
            color="primary"
            @click="sendMessage"
            >отправить
            </v-btn>
          </v-form>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>
<script>
import appAlert from '../components/appAlert.vue'
import appDialog from '../components/appDialog.vue'
import confirmDialog from '../components/confirmDialog.vue'

export default {
  components: {appAlert, appDialog, confirmDialog},
  data() {
    return {
      alert: false,
      alertMessage: '',

      dialog: false,
      typeDialog: 'edit',
      key: 0,
      toolBarTitle: '',

      valid: true,
      nameRules: [
        v => !!v || 'Введите свое имя',
      ],
      id: null,
      idxPostInArray: null,
      post: {},
      posts: [],
      title: '',
      text: '',
      commentName: '',
      commentMessage: '',
      comments: [],

      confirmDialog:false
    }
  },
  methods: {
    goBottom() {
      window.scrollTo(0, document.body.scrollHeight || document.documentElement.scrollHeight);
    },
    deletePost(){
      this.confirmDialog = true
    },
    confirmDelete(){
      localStorage.removeItem("posts")
      const newPostsArray = this.posts.filter(item => item.id !== this.id)
      localStorage.setItem("posts", JSON.stringify(newPostsArray))
      this.$router.push('../')
    },
    editPost() {
      this.dialog = !this.dialog
      this.toolBarTitle = 'Редактирование поста'
      this.key++
    },
    sendMessage(){
      if(this.$refs.form.validate()){
        this.post.comments.push({name: this.commentName, message: this.commentMessage})
        this.posts[this.idxPostInArray] = this.post
        this.comments = this.post.comments
        localStorage.setItem("posts", JSON.stringify(this.posts))
        this.commentName = ''
        this.commentMessage = ''
        this.alert = true
        this.alertMessage = 'комментарий отправлен'
        setTimeout(()=>{
          this.alert=!this.alert
        }, 1500)
        this.$refs.form.resetValidation()
      }
    },
    deleteMessage(idx){
      localStorage.removeItem("posts")
      const message = this.post.comments[idx].message
      this.post.comments = this.post.comments.filter(item => item.message !== message)
      this.comments = this.post.comments
      this.posts[this.idxPostInArray] = this.post
      localStorage.setItem("posts", JSON.stringify(this.posts))
    },
    showAlertEdit(){
      this.alert = true
      this.alertMessage = 'Пост изменен'
      setTimeout(()=>{
        this.alert=!this.alert
      }, 1500)
    }
  },
  mounted() {
    this.id = Number(this.$route.params.id)
    this.posts = JSON.parse(localStorage.getItem("posts"));
    this.idxPostInArray = this.posts.findIndex(item => item.id === this.id)
    this.post = this.posts[this.idxPostInArray]
    this.comments = this.post.comments
  },
}
</script>