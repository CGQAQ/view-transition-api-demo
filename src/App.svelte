<script>
  import {tick} from "svelte";

  const pics = new Array(8).fill(0).map((_, i) => `https://picsum.photos/seed/${i + 1}/200/100`);

  let selectedPic = null;

  function displayNewImage(url) {
    selectedPic = url;
  }

  function onCardClick(event, url) {
    const target = event.currentTarget;
    target.querySelector("img").style.viewTransitionName = "g";

    if (!document?.startViewTransition) {
      displayNewImage(url);
    }

    const transition = document.startViewTransition(async () => {
      displayNewImage(url);
    });

    transition.finished.then(() => {
      console.log("finished");
      target.querySelector("img").style.viewTransitionName = "none";
    });
  }

  function onGallaryClick(event) {
    console.log("onGallaryClick", event);
    if (!document?.startViewTransition) {
      selectedPic = null;
    }

    const _selectedPic = selectedPic;
    const transition = document.startViewTransition(async () => {
      selectedPic = null;
      // tick is needed to make sure the DOM is updated before the transition starts
      return tick().then(() => {
        document.querySelector(`img[src="${_selectedPic}"]`).style.viewTransitionName = "g";
      });
    });

    transition.finished.then(() => {
      document.querySelector(`img[src="${_selectedPic}"]`).style.viewTransitionName = "none";
    });
  }

</script>


<main>
  {#each pics as url}
    <link rel="prefetch" href="{url}" as="image">
  {/each}


  {#if !selectedPic}
    <div class="container">
      {#each pics as pic, index}
        {@const seed = index + 1}
        <div class="card" aria-hidden on:click={(e) => onCardClick(e, pic)}>
          <h2>Card {seed}</h2>
          <img src="{pic}" alt="pic" width="200" height="100">
          <p>Some text</p>
          <p>Some text</p>
        </div>
      {/each}
    </div>
  {/if}

  {#if selectedPic}
  <div class="gallary">
    <img src="{selectedPic}" aria-hidden alt="pic" on:click={ onGallaryClick }>
  </div>
  {/if}
</main>

<style scoped>
  .container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }

  .card:hover {
    outline: none;
    background-color: aliceblue;
    cursor: pointer;
  }

  .gallary {
    view-transition-name: g;
    display: grid;
    width: 100%;
    height: 80vh;
  }

  .gallary img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .gallary img:hover {
    cursor: pointer;
  }
</style>
