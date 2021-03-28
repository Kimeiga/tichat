<script>
  import { Router } from "@roxi/routify";
  import { routes } from "../.routify/routes";

  import Serviceworker from "./Serviceworker.svelte";

  import { FirebaseApp } from "sveltefire";
  import firebase from "firebase/app";
  import "firebase/firestore";
  import "firebase/auth";
  // import "firebase/performance";
  // import "firebase/analytics";

  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  const firebaseConfig = {
    apiKey: "AIzaSyD7edhlGOLeE2uKbbs4bKi1uFhMKLk4ayI",
    authDomain: "tichat.firebaseapp.com",
    projectId: "tichat",
    storageBucket: "tichat.appspot.com",
    messagingSenderId: "561811199163",
    appId: "1:561811199163:web:49c9359ea12e0d6f67059e",
    measurementId: "G-G5XEKYZZK8",
  };

  if (!firebase.apps.length) {
    firebase.initializeApp(firebaseConfig);
  } else {
    firebase.app(); // if already initialized, use that one
  }
</script>

<FirebaseApp {firebase}>
  <Router {routes} />
</FirebaseApp>

<Serviceworker />

<style global>
  @import "../assets/global.css";
</style>
