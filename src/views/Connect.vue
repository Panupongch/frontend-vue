<template>
    <v-container>
      <v-row>
        <v-col cols="12"> 
          <ConnectBackend />
        </v-col>
      </v-row>
    </v-container>
  </template>
  
  <script>
  export default {
    components: {
        ConnectBackend: () => import("../components/ConnectBackend.vue"),
    },
    };
  </script>
  
  <style>
  </style>