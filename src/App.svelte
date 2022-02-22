<script>
  export let name;
  import { onMount } from "svelte";
  import Song from "./Song.svelte";
  let songs = [];
  let searchQuery = "";

  async function setData() {
    if (searchQuery.length > 1) {
      const response = await fetch("https://api.stemify.io/admin/search", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          query: searchQuery,
        }),
      });
      if (response.ok) {
        songs = await response.json();
        console.log(songs);
      } else {
        console.error(response.statusText);
        alert("Error getting songs");
      }
    } else {
      const response = await fetch("https://api.stemify.io/admin/allsongs");
      if (response.ok) {
        songs = await response.json();
        console.log(songs);
      } else {
        console.error(response.statusText);
        alert("Error getting songs");
      }
    }
  }
  onMount(setData);

  const onKeyPress = (e) => {
    if (e.charCode === 13) setData();
  };
</script>

<h1>Stemify Sniper Tool</h1>
<input bind:value={searchQuery} on:keypress={onKeyPress} />
<button on:click={setData}>Search</button>

{#if songs.length == 0}
  <div class="loading">Loading...</div>
{/if}

<div class="songs">
  {#each songs as song}
    <Song data={song} />
  {/each}
</div>

<style>
  h1 {
    text-align: center;
  }

  .songs {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(400px, 1fr));
  }
</style>
