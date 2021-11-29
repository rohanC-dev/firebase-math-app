<template>
  <router-view />
</template>
<script lang="ts">
import { defineComponent } from 'vue';
import { initializeApp } from "firebase/app";
export default defineComponent({
  name: 'App',
  setup() {

  // Your web app's Firebase configuration
  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  const firebaseConfig = {
  apiKey: "AIzaSyCjNUa2NHABdcMwnPBi5N0WwuEHP1Qs7Cs",
  authDomain: "rapid-webdev.firebaseapp.com",
  projectId: "rapid-webdev",
  storageBucket: "rapid-webdev.appspot.com",
  messagingSenderId: "635998119669",
  appId: "1:635998119669:web:e8578fda6e14e0750847bc",
  measurementId: "G-EF7K9YBNJG"
  };
  const app = initializeApp(firebaseConfig);
  }
})
</script>
