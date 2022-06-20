<script setup>
import { onMounted, reactive, onBeforeMount } from 'vue';
import { useRouter } from 'vue-router';
import {initializeApp} from 'firebase/app';//imported fore firebase
import { config } from '../config';//imported object from firebase
import {getFirestore,
        collection,
        onSnapshot,
        doc,
        setDoc } from 'firebase/firestore'//firestore 
import {getAuth}from 'firebase/auth'//auth

const firebaseApp = initializeApp(config.firebase);//created the instance
const auth = getAuth(firebaseApp);
const firestore = getFirestore(firebaseApp);//firestore
const markdownsCol = collection(firestore, 'markdowns');//collection

const state = reactive({ markdowns: [] });
const router = useRouter();

onBeforeMount(async () => {
  // Get a user
  state.user = auth.currentUser;
  console.log(auth.currentUser)

 
})

onMounted(() => {
   onSnapshot(markdownsCol , snapshot =>{

state.markdowns = snapshot.docs.map(d => ({id: d.id, ...d.data() }));
});
})

function newMarkdown() {
  const newDoc = doc(markdownsCol);
  setDoc(newDoc, {markdown: '', coverted: ''});
  router.push(`/editor/${newDoc.id}`)
}
</script>

<template>
  <h1>I am the dashboard</h1>

  <ul v-for="markdown in state.markdowns" :key="markdown.id">
    <li>
      <router-link :to="{ path: `/editor/${markdown.id}` }">{{ markdown.id }}</router-link>
    </li>
  </ul>

  <button @click="newMarkdown">New</button>

</template>
