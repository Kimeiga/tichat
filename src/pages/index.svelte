<script>
  import RoutifyIntro from "./example/_components/RoutifyIntro.svelte";
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

  let postToSend = "";
</script>

<h1>Tichat</h1>
<!-- 2. 😀 Get the current user -->

Howdy 😀! User
<em>{user.displayName}</em>

<button on:click={() => auth.signOut()}>Sign Out</button>

<hr />

<Collection
  path="posts"
  query={(ref) => ref.orderBy("createdAt")}
  let:data={posts}
  let:ref={postsRef}
  log
>
  {#if !posts.length}
    No posts yet...
  {/if}

  <form
    on:submit={async (e) => {
      e.preventDefault();
      if (!postToSend) {
        return;
      }
      await postToSend.add({
        createdAt: postsRef.firestore.FieldValue.serverTimestamp(),
        senderID: user.uid,
        senderPhoto: user.photoURL,
        senderName: user.displayName,
        text: postToSend,
      });

      postToSend = "";
    }}
  >
    <input type="text" placeholder="send a message" bind:value={postToSend} />
    <button
      type="submit"
      on:click={async (e) => {
        e.preventDefault();
        if (!postToSend) {
          return;
        }
        await postToSend.add({
          createdAt: postsRef.firestore.FieldValue.serverTimestamp(),
          senderID: user.uid,
          senderPhoto: user.photoURL,
          senderName: user.displayName,
          text: postToSend,
        });
      }}>send</button
    >
  </form>

  {#each posts as post}
    <p>
      <!-- ID: <em>{comment.ref.id}</em> -->
    </p>
    <p>
      {post.text}
      <button on:click={() => post.ref.delete()}>Delete</button>
    </p>
  {/each}
</Collection>

<!-- 3. 📜 Get a Firestore document owned by a user -->
<Doc path={`posts/${user.uid}`} let:data={post} let:ref={postRef} log>
  <h2>{post.title}</h2>

  <p>
    Document created at <em>{new Date(post.createdAt).toLocaleString()}</em>
  </p>

  <span slot="loading">Loading post...</span>
  <span slot="fallback">
    <button
      on:click={() =>
        postRef.set({
          title: "📜 I like Svelte",
          createdAt: Date.now(),
        })}
    >
      Create Document
    </button>
  </span>

  <!-- 4. 💬 Get all the comments in its subcollection -->

  <h3>Comments</h3>
  <Collection
    path={postRef.collection("comments")}
    query={(ref) => ref.orderBy("createdAt")}
    let:data={comments}
    let:ref={commentsRef}
    log
  >
    {#if !comments.length}
      No comments yet...
    {/if}

    {#each comments as comment}
      <p>
        <!-- ID: <em>{comment.ref.id}</em> -->
      </p>
      <p>
        {comment.text}
        <button on:click={() => comment.ref.delete()}>Delete</button>
      </p>
    {/each}

    <button
      on:click={() =>
        commentsRef.add({
          text: "💬 Me too!",
          createdAt: Date.now(),
        })}
    >
      Add Comment
    </button>

    <span slot="loading">Loading comments...</span>
  </Collection>
</Doc>

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
