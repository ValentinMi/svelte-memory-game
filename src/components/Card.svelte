<script>
  import { createEventDispatcher } from "svelte";
  export let infos;
  export let index;

  const dispatch = createEventDispatcher();

  function onCardClick() {
    dispatch("click-card", { index });
  }
</script>

<div class="card" on:click={onCardClick}>
  {#if infos.isVisible}
    <span class="card--emoji">{infos.emoji}</span>
  {/if}
</div>

<style>
  .card {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 130px;
    height: 180px;
    background-color: crimson;
  }

  .card--emoji {
    font-size: 40px;
  }
</style>
