<template>
  <div>
    <br>
    <v-row
    justify="center"
     >
      <v-dialog
        v-model="dialog"
        fullscreen
        hide-overlay
        transition="dialog-bottom-transition"
        scrollable
        class="overlay-color-black"
        @keydown.esc="$emit('closeDialog')"
      >
        <v-container>
          <v-card tile>
            <v-toolbar
              flat
              dark
              color="primary"
            >
              <v-btn
                icon
                dark
                @click="$emit('closeDialog')"
              >
                <v-icon>mdi-close</v-icon>
              </v-btn>

              <v-toolbar-title>{{toolBarTitle}}</v-toolbar-title>
              <v-spacer></v-spacer>
              <v-toolbar-items>
                <v-btn
                  dark
                  text
                  @click="savePost"
                >
                  Сохранить
                </v-btn>
              </v-toolbar-items>
            </v-toolbar>
            <div style="width:80%; margin:auto" >
              <v-form
                ref="form"
                v-model="valid"
                lazy-validation
              >
                <v-text-field
                class="my-5"
                v-model="title"
                label="Название поста"
                :rules="nameRules"
                outlined
                required
                ></v-text-field>
                <v-textarea
                rows="2"
                v-model="description"
                counter
                label="Краткое описание"
                outlined
                maxlength="50"
                >
                </v-textarea>
                <v-textarea
                rows="10"
                v-model="text"
                counter
                label="Текст"
                outlined
                ></v-textarea>
              </v-form>
            </div>
          </v-card>
        </v-container>
      </v-dialog>
    </v-row>
  </div>
</template>
<script>
export default {
  name: 'Home',
  props: ['dialog', 'typeDialog', 'posts', 'toolBarTitle', 'idxPostInArray', 'post'],
  emits: ['closeDialog', 'alertCreate', 'alertEdit' ],
  data() {
    return {
      valid: true,
      title: '',
      description: '',
      text: '',
      nameRules: [
        v => !!v || 'Заполните необходимые поля',
      ],
    }
  },
  computed: {
  },
  methods: {
    savePost() {
      if(this.$refs.form.validate() && (this.typeDialog === 'create')){
        this.$emit('closeDialog')
        this.posts.push({
          id: Math.floor(Math.random() * 1000) + 1,
          title: this.title,
          description: this.description,
          text: this.text,
          comments: []
        })
        localStorage.setItem("posts", JSON.stringify(this.posts))
        this.title = ''
        this.description = ''
        this.text = ''
        this.$refs.form.resetValidation()
        this.$emit('alertCreate')
      } else if(this.$refs.form.validate() && (this.typeDialog === 'edit')){
        this.$emit('closeDialog')
        this.post.title = this.title
        this.post.description = this.description
        this.post.text = this.text
        this.posts[this.idxPostInArray] = this.post
        localStorage.setItem("posts", JSON.stringify(this.posts))
        this.$refs.form.resetValidation()
        this.$emit('alertEdit')
      }
    },
  },
  mounted() {
    if(this.typeDialog ==='edit'){
      this.title = this.post.title
      this.description = this.post.description
      this.text = this.post.text
    }
  },
}
</script>
