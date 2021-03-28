<!-- routify:options preload="proximity" -->
<script>
  import { User } from "sveltefire";
  // import Navbar from "./_navbar.svelte";

  import { getContext, onMount } from "svelte";
  const firebase = getContext("firebase").getFirebase();
  const googleProvider = new firebase.auth.GoogleAuthProvider();

  import Login from "_login.svelte";
</script>

<User persist={localStorage} let:user let:auth>
  <!-- <Navbar profileImgURL={user.photoURL} /> -->

  <slot scoped={{ user, auth }} />

  <div slot="signed-out">
    <h1>Tichat</h1>
    <hr />
    <Login />
  </div>
</User>

<style>
</style>
