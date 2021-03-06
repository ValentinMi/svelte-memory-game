<script>
  import cardsJson from "./cards.json";
  import Card from "./components/Card.svelte";
  import Chrono from "./components/Chrono.svelte";

  let cards = shuffleArray(cardsJson);
  let selectedCards = [];
  let pairsFound = [];
  let isPlaying = false;
  let cardsAreClickable = true;
  let chrono;

  $: win = pairsFound.length === cards.length / 2;
  $: if (win) {
    chrono.stopChrono();
  }

  function shuffleArray(array) {
    return [...Array(array.length)]
      .map((...args) => Math.floor(Math.random() * (args[1] + 1)))
      .reduce((a, rv, i) => ([a[i], a[rv]] = [a[rv], a[i]]) && a, array);
  }

  function handleClickOnCard(e) {
    if (!isPlaying) {
      isPlaying = true;
      chrono.startChrono();
    }
    let cardIndex = e.detail.index;
    // If click on a founded pair , return
    if (pairsFound.includes(cards[cardIndex].pair)) return;
    let newCards = [...cards];
    // If first card clicked
    if (selectedCards.length === 0) {
      newCards[cardIndex].isVisible = true;
      selectedCards = [...selectedCards, cardIndex];
      cards = newCards;
      // If second card
    } else if (selectedCards.length === 1) {
      cardsAreClickable = false; // Disable click to avoid spam
      newCards[cardIndex].isVisible = true;
      selectedCards = [...selectedCards, cardIndex];
      cards = newCards;
      // If cards match
      if (newCards[selectedCards[0]].pair === newCards[selectedCards[1]].pair) {
        pairsFound = [...pairsFound, newCards[selectedCards[0]].pair];
        cards = newCards;
        selectedCards = [];
        // If not
      } else {
        setTimeout(() => {
          selectedCards.forEach((cardIndex) => {
            newCards[cardIndex].isVisible = false;
          });
          selectedCards = [];
          cards = newCards;
        }, 700);
      }
      cardsAreClickable = true;
    }
  }

  function handleReset() {
    selectedCards = [];
    pairsFound = [];
    cards = shuffleArray(cardsJson);
    cards = cards.map((card) => ({ ...card, isVisible: false }));
    chrono.resetChrono();
  }
</script>

<div class="App">
  <div class={`card--grid ${!cardsAreClickable && "disabled"}`}>
    {#each cards as card, i (`${card.id}-${i}`)}
      <div class="card--container">
        <Card on:click-card={handleClickOnCard} infos={card} index={i} />
      </div>
    {/each}
    {#if win}
      <div class="win--overlay" />
      <div class="win--modal">
        You win
        <button on:click={handleReset} class="win--modal__reset">Reset</button>
      </div>
    {/if}
  </div>
  <div class="board">
    <Chrono bind:this={chrono} on:resetGame={handleReset} />
  </div>
</div>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    background-color: cyan;
  }

  .App {
    padding: 4rem;
    display: flex;
    justify-content: center;
  }

  .card--container {
    height: 100%;
    width: 100%;
    margin: 0 auto;
    display: flex;
    justify-content: center;
  }

  .card--grid {
    display: grid;
    justify-content: center;
    grid-template-columns: 130px 130px 130px 130px;
    grid-template-rows: 1fr 1fr 1fr 1fr;
    gap: 30px;
  }

  .win--overlay {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.5);
  }

  .win--modal {
    position: absolute;
    height: 300px;
    width: 400px;
    transform: translate(-50%, -50%);
    left: 50%;
    top: 50%;
    background-color: white;
    line-height: 300px;
    text-align: center;
  }

  .win--modal__reset {
    position: absolute;
    transform: translate(-50%, -50%);
    bottom: 50px;
    left: 50%;
    padding: 10px;
  }

  .disabled {
    pointer-events: none;
  }
</style>
