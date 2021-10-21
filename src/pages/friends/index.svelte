<script>
  import { metatags } from "@roxi/routify";
  metatags.title = "My Routify app";
  metatags.description = "Description coming soon...";
  import { FirebaseApp, User, Doc, Collection } from "sveltefire";
  import { goto } from "@roxi/routify";
  import { getContext, onMount } from "svelte";

  const firebase = getContext("firebase").getFirebase();
  const db = firebase.firestore();

  export let scoped;
  $: ({ user, auth } = scoped);
</script>

<h1>Tichat</h1>
<h2>friends</h2>

<Collection path={"chats"} let:data={chats} let:ref={chatsRef} log>
  <Collection
    path={"users"}
    let:data={users}
    let:ref={usersRef}
    query={(ref) => ref.orderBy("createdAt")}
    log
  >
    {#each users as person}
      {#if person.id !== user.uid}
        <img src={person.photoURL} alt="user's face idk" style="float: left" />
        <h2>{person.displayName}</h2>

        <p>id: {person.id}</p>

        <button
          on:click={async () => {
            // usersRef.collection('chats').doc(user.uid).({
            //   title: "📜 I like Svelte",
            //   createdAt: Date.now(),
            // })

            // check if you already have a chat with this person
            let chatWithPerson = await usersRef
              .doc(user.uid)
              .collection("chats")
              .doc(person.id)
              .get();

            if (chatWithPerson.exists) {
              // you already have a chat with this person, just go to that chat
              $goto(`/${person.id}`);
            } else {
              // make a new chat with this person
              let chatDocRef = await chatsRef.add({
                createdAt: firebase.firestore.FieldValue.serverTimestamp(),
                updatedAt: firebase.firestore.FieldValue.serverTimestamp(),
                // first two people in the chat
                users: [user.uid, person.id],
              });

              await usersRef
                .doc(user.uid)
                .collection("chats")
                .doc(person.id)
                .set({
                  // set chat foreign key in you and
                  chatId: chatDocRef.id,
                });

              await usersRef
                .doc(person.id)
                .collection("chats")
                .doc(user.uid)
                .set({
                  // set chat foreign key in you and
                  chatId: chatDocRef.id,
                });

              $goto(`/${person.id}`);
            }
          }}>Chat with this person</button
        >
        <hr />
      {/if}
    {/each}
  </Collection>
</Collection>

<!-- Styles -->
<style>
  /* main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1,
  em {
    color: #ff3e00;
  }

  hr {
    height: 1px;
    border: none;
    background: rgb(195, 195, 195);
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  } */
</style>
