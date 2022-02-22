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
</script>

<h1>Stemify Sniper Tool</h1>
<input bind:value={searchQuery} />
<button on:click={setData}>Search</button>

{#each songs as song}
  <Song data={song} />
{/each}
