<!-- routify:options preload="proximity" -->
<script>
  import { User } from "sveltefire";
  // import Navbar from "./_navbar.svelte";
  import BottomNav from "./_components/BottomNav.svelte";

  import { getContext, onMount } from "svelte";
  const firebase = getContext("firebase").getFirebase();
  const googleProvider = new firebase.auth.GoogleAuthProvider();

  import Login from "_login.svelte";
</script>

<User persist={localStorage} let:user let:auth>
  <!-- <Navbar profileImgURL={user.photoURL} /> -->
  <div slot="signed-out">
    <h1>Tichat</h1>
    <hr />
    <Login />
  </div>

  <main>
    <slot scoped={{ user, auth }} />
  </main>
  <BottomNav profileImgURL={user.photoURL} />
</User>

<style>
  main {
    padding: 0.2rem calc(20vw - 3.5rem) 4rem;
  }
</style>
