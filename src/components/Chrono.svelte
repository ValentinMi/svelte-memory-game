<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  let chrono = 0;
  let localStorageKey = "svelte-memo-chrono";
  let timesHistory =
    sortTimeHistory(JSON.parse(localStorage.getItem(localStorageKey))) || [];
  let interval = null;

  function incrementChrono() {
    chrono++;
  }

  function saveTime() {
    localStorage.setItem(
      localStorageKey,
      JSON.stringify([...timesHistory, chrono])
    );
  }

  function sortTimeHistory(history) {
    if (!history) return;
    const sortedHistory = history.sort((a, b) => a - b);
    // Keep 5 best times
    return sortedHistory.slice(0, 5);
  }

  export function resetChrono() {
    stopChrono();
    chrono = 0;
  }

  export function startChrono() {
    interval = setInterval(() => {
      incrementChrono();
    }, 1000);
  }

  export function stopChrono() {
    clearInterval(interval);
    interval = null;
    // Save time
    saveTime();
    // Refresh time history
    timesHistory = sortTimeHistory(
      JSON.parse(localStorage.getItem(localStorageKey))
    );
  }
</script>

<div class="chrono-container">
  <div class="chrono-frame-container">
    <span class="chrono-frame">{chrono}s</span>
  </div>

  {#if timesHistory.length > 0}
    <ol class="chrono-history">
      {#each timesHistory as time}
        <li class="chrono-history-time"><span>{time}s</span></li>
      {/each}
    </ol>
  {/if}

  <button class="chrono-reset-game" on:click={() => dispatch("resetGame")}
    >Reset</button
  >
</div>

<style>
  .chrono-container {
    margin-left: 30px;
  }
  .chrono-frame-container {
    display: flex;
  }
  .chrono-frame {
    border: 1px solid black;
    background: #fff;
    padding: 10px 20px;
    font-size: 40px;
    text-align: end;
  }
  .chrono-history {
    background: white;
    padding: 1rem 2rem;
    width: auto;
    min-width: 50px;
  }
  .chrono-history-time {
    margin: 1rem 0;
  }

  .chrono-history-time > span {
    margin-left: 10px;
  }

  .chrono-reset-game {
    padding: 10px;
  }
</style>
