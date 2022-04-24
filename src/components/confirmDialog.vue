<template>
    <v-dialog
      v-model="confirm"
      max-width="350"
    >
      <v-card class="text-center">
        <v-card-text class="ma-0">
          Вы действительно хотите удалить пост?
        </v-card-text>


        <v-card-actions class="ma-0">


          <v-btn
            color="green darken-1"
            text
            @click="$emit('confirmNo')"
          >
            Нет
          </v-btn>
 <v-spacer></v-spacer>
          <v-btn
            color="red darken-1"
            text
           @click="$emit('confirmYes')"
          >
            Да
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>
<script>
  export default {
    props: ['confirm']
  }
</script>