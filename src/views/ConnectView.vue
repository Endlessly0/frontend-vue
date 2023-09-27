<template>
    <v-container>
      <v-row>
          <v-col cols="12">
              <Connect/>
          </v-col>
      </v-row>
    </v-container>
  </template>
  
  <script>
  export default {
      components: {
          Connect: ()=> import('../components/Connect.vue')
      }
  }
  </script>
  
  <style>
  
  </style>