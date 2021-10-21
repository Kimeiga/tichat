<script>
  export let id;
  import { Doc, Collection } from "sveltefire";

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

  const setupMap = (data) => {
    console.log(data);
    if (!data) {
      return;
    }
    data.users.forEach(async (userId) => {
      let userRef = await db.collection("users").doc(userId).get();
      let userData = userRef.data();
      console.log(userData);

      userIdToNameMap[userId] = {
        displayName: userData.displayName,
        photoURL: userData.photoURL,
      };
    });
  };

  $: console.log(userIdToNameMap);

  const sendMessage = async (e) => {
    e.preventDefault();
    await messagesRef.add({
      createdAt: firebase.firestore.FieldValue.serverTimestamp(),
      sender: user.uid,
      text: messageToSend,
    });

    messageToSend = "";

    console.log(dummy);
    console.log(dummy.current);
    dummy.current.scrollIntoView({ behavior: "smooth" });
  };
</script>

<Doc
  path={`users/${user.uid}/chats/${id}`}
  log
  on:data={(e) => (chatId = e.detail.data ? e.detail.data.chatId : null)}
  on:ref
/>
<Collection path={"users"} let:data={users} let:ref={usersRef} log>
  <Doc
    path={`chats/${chatId}`}
    log
    let:data={chat}
    let:ref={chatRef}
    on:data={(e) => setupMap(e.detail.data)}
  >
    {JSON.stringify(chat)}

    <Collection
      path={`chats/${chatId}/messages`}
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
            {#if userIdToNameMap[message.sender]}
              {#if userIdToNameMap[message.sender].photoURL}
                <img
                  src={userIdToNameMap[message.sender].photoURL}
                  alt="person"
                  width="20"
                />
              {/if}
              <p>
                {userIdToNameMap[message.sender].displayName}: {message.text}
              </p>
            {/if}
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
            sender: user.uid,
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
              sender: user.uid,
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
