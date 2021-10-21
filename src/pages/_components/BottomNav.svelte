<script>
  export let profileImgURL;
</script>

<div class="bottom-nav">
  <a href="/"><span>🏠</span></a>
  <a href="/messages"><span>💬</span></a>
  <a href="/lobby"><span>📖</span></a>
  <a href="/friends"><span>👪</span></a>
  <a href="/ma">
    <img id="bottom-nav-profile-img" src={profileImgURL} alt="me" /></a
  >
  <!-- TODO: change me to the person's username -->
</div>

<style>
  .bottom-nav {
    position: fixed;
    bottom: 0;
    width: 100%;
    background: black;
    border-top: 1px grey solid;
    display: flex;
    justify-content: space-between;
    padding: 0.2rem calc(20vw - 3.5rem);
    font-size: 1.5rem;
    align-items: flex-end;
  }
  #bottom-nav-profile-img {
    height: 1.5rem;
  }
</style>
