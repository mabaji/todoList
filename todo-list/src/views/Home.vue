<template>
  <v-container>
    <v-layout row wrap>
      <v-flex xs6>
        {{ msg }}
      </v-flex>
    </v-layout>    
  </v-container>
</template>

<script>
  

  export default{
    data(){
      return {
        msg: '환영합니다!'
      }
    },
    methods: {

    }
  }
</script>
