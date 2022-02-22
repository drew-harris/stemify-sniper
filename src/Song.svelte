<script>
  export let data = null;
  let deleted = false;
  function getTimeString(millis) {
    // To locale
    return new Date(millis).toLocaleString();
  }

  async function deleteSong() {
    const val = confirm("ARE YOU SURE YOU WANT TO DELETE THIS SONG?");
    if (!val) {
      return;
    }
    const response = await fetch("https://api.stemify.io/admin/" + data._id, {
      method: "DELETE",
      headers: {
        "Content-Type": "application/json",
      },
    });

    if (response.status === 200) {
      deleted = true;
    } else {
      alert("Error deleting song");
    }
  }

  async function approveSong() {
    const response = await fetch(
      "https://api.stemify.io/admin/approve/" + data._id,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    if (response.status === 200) {
      data.approved = true;
    } else {
      alert("Error approving song");
    }
  }

  async function rejectSong() {
    const response = await fetch(
      "https://api.stemify.io/admin/hide/" + data._id,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    if (response.status === 200) {
      data.adminHidden = true;
    } else {
      alert("Error approving song");
    }
  }

  function getClassOfSong(song, deleted) {
    let myClass = "song ";
    if (song.adminHidden) {
      myClass += "rejected ";
    }
    if (deleted) {
      myClass += "deleted ";
    } else if (song.approved && !song.complete) {
      myClass += "early-approved ";
    } else if (song.approved && song.complete) {
      myClass += "complete-approved  ";
    }
    return myClass;
  }

  function isYoutubeMusic() {
    if (!data.youtubeUrl) {
      return false;
    }
    return data.youtubeUrl.includes("music");
  }
</script>

<div class={getClassOfSong(data, deleted)}>
  <div class="left">
    <div class="info">ID: {data._id}</div>
    <div class={data.complete ? "info green" : "info red"}>
      COMPLETE: {data.complete}
    </div>
    <div class="info">Title: {data.title}</div>
    <div class="info">Artist: {data.metadata.artist}</div>
    <div class={data.approved ? "info green" : "info orange"}>
      Approved: {data.approved}
    </div>
    <div class="info">Slug: {data.songSlug}</div>
    <div class="info">Ticket ID: {data.ticketId}</div>
    <div class={data.metadata.trackId ? "info green" : "info red"}>
      {data.metadata.trackId ? "SPOTIFY DATA" : "MANUAL DATA"}
    </div>
    {#if data.youtubeUrl}
      <a href={data.youtubeUrl} target="_blank"
        >{isYoutubeMusic() ? "Youtube MUSIC Link" : "Youtube Link"}</a
      >
    {/if}
    <div class="info">Time Submitted: {getTimeString(data.timeSubmitted)}</div>
  </div>
  <div class="right">
    {#if !deleted}
      <button class="red" on:click={deleteSong}>Delete</button>
    {/if}
    {#if !data.approved && !deleted}
      <button class="green" on:click={approveSong}>Approve</button>
    {/if}
    {#if !data.adminHidden && !deleted && !data.approved}
      <button on:click={rejectSong}>Reject </button>
    {/if}
  </div>
</div>

<style>
  .song {
    display: flex;
    flex-direction: row;
    align-items: center;
    padding: 20px;
    border: 1px solid #ccc;
  }

  .rejected {
    /* Transparent */
    opacity: 30%;
  }

  .complete-approved {
    background-color: rgb(213, 255, 213);
  }

  .early-approved {
    background-color: rgb(230, 234, 255) !important;
  }

  .deleted {
    background-color: rgb(253, 186, 186) !important;
  }

  .right {
    margin-left: 40px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
  }

  button {
    display: block;
    margin-top: 8px;
    cursor: pointer;
  }

  a {
    font-weight: bold;
    color: green;
    text-decoration: underline;
  }

  .green {
    color: green;
  }
  .red {
    color: red;
    font-weight: bold;
  }
  .orange {
    color: red;
  }
</style>
