<script>
  import { getContext, onMount } from "svelte";

  import * as firebaseui from "firebaseui";
  const firebase = getContext("firebase").getFirebase();

  const db = firebase.firestore();

  onMount(() => {
    firebase
      .auth()
      .setPersistence(firebase.auth.Auth.Persistence.LOCAL)
      .then(() => {
        let ui =
          firebaseui.auth.AuthUI.getInstance() ||
          new firebaseui.auth.AuthUI(firebase.auth());
        let uiConfig = {
          callbacks: {
            signInSuccessWithAuthResult: (authResult) => {
              // this is a new user, add them to the firestore users collection!

              // if (authResult.additionalUserInfo.isNewUser) {
              db.collection("users")
                .doc(authResult.user.uid)
                .set({
                  displayName: authResult.user.displayName,
                  photoURL: authResult.user.photoURL,
                  createdAt: firebase.firestore.FieldValue.serverTimestamp(),
                })
                .then(() => {
                  console.log("User document successfully written!");
                })
                .catch((error) => {
                  console.error("Error writing user document: ", error);
                });
              // }
              return false;
            },
          },
          signInOptions: [
            {
              provider: firebase.auth.GoogleAuthProvider.PROVIDER_ID,
              clientId:
                "561811199163-mlumhdkfre3068f2o981lljj5srh9f0h.apps.googleusercontent.com",
            },
            firebase.auth.EmailAuthProvider.PROVIDER_ID,
          ],
          // Required to enable one-tap sign-up credential helper.
          credentialHelper: firebaseui.auth.CredentialHelper.GOOGLE_YOLO,
        };
        ui.start("#firebaseui-auth-container", uiConfig);
      });
  });
</script>

<svelte:head>
  <link
    type="text/css"
    rel="stylesheet"
    href="https://www.gstatic.com/firebasejs/ui/4.6.1/firebase-ui-auth.css"
  />
</svelte:head>

<div id="firebaseui-auth-container" />

<style>
  @import "firebaseui/dist/firebaseui.css";
</style>
