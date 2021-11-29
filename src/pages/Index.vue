<template>
  <q-page class="row items-center justify-evenly">

    <div class = "column">
        <q-btn v-if = "lives > 0" color = "primary" @click="signIn">Login</q-btn>
        <br>
        <br>

        <q-item clickable v-ripple>
      <q-item-section side>
        <q-avatar rounded size="48px">
          <img :src="avatarUrl" />
        </q-avatar>
      </q-item-section>
      <q-item-section>
        <q-item-label>{{loggedInUsername}} </q-item-label>
      </q-item-section>
    </q-item>

    </div>

    <div v-if = "lives > 0" class = "column">
      <div class = "row"> <h6 style = "margin-right: 10px;">Score: {{score}}</h6> <h6 style = "margin-left: 10px;">Lives: {{lives}}</h6></div>
      <q-card
      class="my-card text-white"
      style="background: radial-gradient(circle, #35a2ff 0%, #014a88 100%)">
      <q-card-section>
        <div class="text-h6"> Problem: {{problem_num}} </div>
      </q-card-section>


      <q-card-section  class="q-pt-none">
        {{ number1 }} +  {{ number2 }}
      </q-card-section>
    </q-card>
      <br>
      <br>
      <q-input clearable @keydown.enter="onClick(); saveStatus(loggedInUsername, score, loggedInUsername, avatarUrl);" standout="bg-teal text-white" v-model="answer" label="Your Answer" />
      <br>
      <br>
      <q-btn @click="onClick(); saveStatus(loggedInUsername, score, loggedInUsername, avatarUrl);">Submit</q-btn>
      <br>
      <!--<p v-if="number1 + number2 == answer">Score: {{score}}</p>-->
    </div>

    <div class = "column">
      <h1 v-if = "lives <= 0">Game Over.</h1>

     <h5 v-if = "lives <= 0">Leaderboard</h5>
    <q-card style="margin-bottom:10px" v-for="(val, index) in scores" :key="index" class="my-card">
      <q-item v-if = "lives <= 0" clickable v-ripple>
      <div class = "row" style="margin-right:10px">
          <q-item-section>
             {{index+1}}
          </q-item-section>
       
        <q-item-section side>
        
        <q-avatar rounded size="48px">
          <img :src="val.avatarUrl" />
        </q-avatar>
       </q-item-section>
          
      </div>
      
      <q-item-section>
        <q-item-label> {{val.displayName}}</q-item-label>
        <q-item-label caption> Score: {{val.score}}</q-item-label>
      </q-item-section>
    </q-item>
    
    </q-card>


      <q-btn v-if = "lives <= 0" color = "primary" @click="reloadPage">Refresh</q-btn>
    </div>
     

  </q-page>

</template>

<script lang="ts">
//import { Todo, Meta } from 'components/models';
//import ExampleComponent from 'components/CompositionComponent.vue';
import { defineComponent } from 'vue';
import {ref} from 'vue';
import { getAuth, signInWithPopup, GoogleAuthProvider, onAuthStateChanged } from 'firebase/auth';
import { doc, setDoc } from 'firebase/firestore';
import { getFirestore } from 'firebase/firestore';
import { onSnapshot } from "firebase/firestore";
import { collection, query, where } from "firebase/firestore";

export default defineComponent({
  name: 'PageIndex',
  setup() {
    const score = ref();
    const problem_num = ref();
    problem_num.value = 1;
    score.value = 1;
    const lives = ref();
    lives.value = 3;
    const answer = ref();
    const isCorrect = ref();
    const number1 = ref();
    const number2 = ref();
    const loggedInUsername = ref('');
    const auth = getAuth();
    const avatarUrl = ref('');
    const provider = new GoogleAuthProvider();
    const db = getFirestore();
    const scores = ref(<any[]>[]);
    //number.value = Math.floor(Math.random() * (10 - 1) + 10);
    number1.value = Math.floor(Math.random() * (10 - 1) + 10);
    number2.value = Math.floor(Math.random() * (10 - 1) + 10);
    const sum = ref();
    sum.value = parseInt(number1.value) + parseInt(number2.value);


    function onClick(){
      console.log('outside')
      isCorrect.value = parseInt(answer.value) === parseInt(sum.value) ? updateNumbers(): decrementLives();
      //number1.value = Math.floor(Math.random() * (10 - 1) + 10);
      //number2.value = Math.floor(Math.random() * (10 - 1) + 10);
      //sum.value = parseInt(number1.value) + parseInt(number2.value);
      if (lives.value < 0) {
        console.log('game over');
      }
    }

    function updateNumbers(){
      number1.value = Math.floor(Math.random() * (10 - 1) + 10);
      number2.value = Math.floor(Math.random() * (10 - 1) + 10);
      sum.value = parseInt(number1.value) + parseInt(number2.value);
      score.value = parseInt(score.value) + Math.floor(Math.random() * (2 - 1) + 2);
      problem_num.value = parseInt(problem_num.value) + 1;
    }
    
    function decrementLives(){
      lives.value = parseInt(lives.value) - 1;

    }

    async function signIn(){
      
      const result = await signInWithPopup(auth, provider)
      
    }

    onAuthStateChanged(auth, (user) => {
        loggedInUsername.value = user?.displayName || '';
        avatarUrl.value = user?.photoURL || '';
    });

    const unsub = onSnapshot(query(collection(db, 'scores')), (querySnapshot) => {
      scores.value = querySnapshot.docs.map(doc => doc.data());
      
      console.log(scores.value);
    });

    async function saveStatus(id: string, score: string, name: string, avatarUrl: string){
        await setDoc(doc(db, 'scores', id), {
          score, displayName: id, avatarUrl
        });

    }

    function reloadPage() {
      window.location.reload();
    }

    


    return { signIn, onClick, reloadPage, isCorrect, loggedInUsername, avatarUrl, saveStatus, number1, number2, sum, answer, score, lives, problem_num, scores};
  },

  

  

  generateRandomNumber(){
    const number = ref();
    number.value = Math.floor(Math.random() * (10 - 1) + 10);
  },
  
  
  
  
});
</script>

