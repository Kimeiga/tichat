<script>
  export let id;
  import { Doc, Collection } from "sveltefire";
  import { user } from "../example/app/_store";

  export let scoped;
  $: ({ user, auth } = scoped);
  import { getContext, onMount } from "svelte";

  const firebase = getContext("firebase").getFirebase();
  const db = firebase.firestore();
  let chatId;

  let messageToSend = "";

  let userIdToNameMap = {};
  let dummy;

  onMount(async () => {});
</script>

<h1>lobby</h1>

<Collection path={"lobbies"} let:data={lobbies} let:ref={lobbiesRef} log>
  <Doc path={`lobbies/0`} log let:data={lobby} let:ref={lobbyRef}>
    {JSON.stringify(lobby)}

    <Collection
      path={`lobbies/0/messages`}
      let:data={messages}
      let:ref={messagesRef}
      query={(ref) => ref.orderBy("createdAt")}
      log
    >
      {#if !messages.length}
        send a message!
      {/if}
      <ul>
        {#each messages as message}
          <!-- {JSON.stringify(usersRef.doc(message.sender).get().data)} -->
          <li>
            <img src={message.senderPhoto} alt="person" width="20" />
            <p>
              {message.senderName}: {message.text}
            </p>
          </li>
        {/each}
      </ul>

      <form
        on:submit={async (e) => {
          e.preventDefault();
          if (!messageToSend) {
            return;
          }
          await messagesRef.add({
            createdAt: firebase.firestore.FieldValue.serverTimestamp(),
            senderID: user.uid,
            senderPhoto: user.photoURL,
            senderName: user.displayName,
            text: messageToSend,
          });

          messageToSend = "";

          console.log(dummy);
          console.log(dummy);
          dummy.current.scrollIntoView({ behavior: "smooth" });
        }}
      >
        <span bind:this={dummy} />
        <input
          type="text"
          placeholder="send a message"
          bind:value={messageToSend}
        />
        <button
          type="submit"
          on:click={async (e) => {
            e.preventDefault();
            if (!messageToSend) {
              return;
            }
            await messagesRef.add({
              createdAt: firebase.firestore.FieldValue.serverTimestamp(),
              senderID: user.uid,
              senderPhoto: user.photoURL,
              senderName: user.displayName,
              text: messageToSend,
            });

            messageToSend = "";

            console.log(dummy);
            console.log(dummy.scrollIntoView());
            dummy.scrollIntoView({ behavior: "smooth" });
          }}>send</button
        >
      </form>
    </Collection>
  </Doc>
</Collection>
