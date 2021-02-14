<script>
  import cardsJson from "./cards.json";
  import Card from "./components/Card.svelte";

  let cards = shuffleArray(cardsJson);
  let selectedCards = [];
  let pairsFound = [];

  function shuffleArray(array) {
    return [...Array(array.length)]
      .map((...args) => Math.floor(Math.random() * (args[1] + 1)))
      .reduce((a, rv, i) => ([a[i], a[rv]] = [a[rv], a[i]]) && a, array);
  }

  function handleClickOnCard(e) {
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
        }, 1000);
      }
    }
  }

  function handleReset() {
    selectedCards = [];
    pairsFound = [];
    cards = shuffleArray(cardsJson);
    cards = cards.map((card) => ({ ...card, isVisible: false }));
  }
</script>

<div class="App">
  <div class="card--grid">
    {#each cards as card, i (`${card.id}-${i}`)}
      <div class="card--container">
        <Card on:click-card={handleClickOnCard} infos={card} index={i} />
      </div>
    {/each}
    {#if pairsFound.length === cards.length / 2}
      <div class="win--overlay" />
      <div class="win--modal">
        You win
        <button on:click={handleReset} class="win--modal__reset">Reset</button>
      </div>
    {/if}
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
</style>
