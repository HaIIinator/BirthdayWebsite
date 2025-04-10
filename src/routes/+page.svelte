<script>
    let cards = [];
    let flippedCards = [];
    let moves = 0;
    let matches = 0;
    let columns = 6;
  
    const generateCards = () => {
      const pairs = 12;
      const imageIds = Array.from({length: pairs}, (_, i) => i + 1);
      const duplicated = imageIds.flatMap(id => [id, id]);
      const shuffled = duplicated.sort(() => Math.random() - 0.5);
      
      return shuffled.map((id, index) => ({
        id: index,
        imageUrl: `https://picsum.photos/100/100?${id}`,
        isFlipped: false,
        isMatched: false,
        pairId: id
      }));
    };
  
    const startNewGame = () => {
      cards = generateCards();
      flippedCards = [];
      moves = 0;
      matches = 0;
      columns = 6;
    };
  
    const handleCardClick = (index) => {
      if (cards[index].isMatched || cards[index].isFlipped || flippedCards.length === 2) return;
  
      cards[index].isFlipped = true;
      flippedCards = [...flippedCards, index];
  
      if (flippedCards.length === 2) {
        moves += 1;
        const [first, second] = flippedCards;
        
        if (cards[first].pairId === cards[second].pairId) {
          cards[first].isMatched = true;
          cards[second].isMatched = true;
          matches += 1;
          flippedCards = [];
          if (matches === cards.length / 2) alert('Gewonnen!');
        } else {
          setTimeout(() => {
            cards[first].isFlipped = false;
            cards[second].isFlipped = false;
            flippedCards = [];
            cards = cards;
          }, 1000);
        }
      }
      cards = cards;
    };
  
    startNewGame();


    function nope()
    {
        alert("Sicha ned oida :)")
    }
  </script>
  
  <div class="controls">
    <button onclick={startNewGame}>Neues Spiel</button>
    <button onclick={nope}>Leicht</button>
    <button onclick={nope}>Mittel</button>
    <button onclick={startNewGame}>Schwer</button>


  </div>
  
  <div class="stats">
    <p>Züge: {moves}</p>
    <p>Gefundene Paare: {matches}</p>
  </div>
  
  <div class="game-grid" style="--columns: {columns}">
    {#each cards as card, index (card.id)}
    <div 
    class="card" 
    class:flipped={card.isFlipped || card.isMatched}
    class:matched={card.isMatched}
    onclick={() => handleCardClick(index)}
    onkeydown={e => e.key === 'Enter' || e.key === ' ' ? handleCardClick(index) : null}
    role="button"
    tabindex="0"
    aria-label={`Memory-Karte ${index + 1}`}
    aria-disabled={card.isMatched || card.isFlipped || flippedCards.length === 2}
  >
    <div class="front">
      <img src={card.imageUrl} alt={`Memory-Paar ${card.pairId}`} />
    </div>
    <div class="back"></div>
  </div>
    {/each}
  </div>
  
  <style>
    .controls {
      text-align: center;
      padding: 20px;
    }
  
    button {
      margin: 0 10px;
      padding: 10px 20px;
    }
  
    .stats {
      text-align: center;
      font-size: 1.2em;
    }
  
    .game-grid {
      display: grid;
      grid-template-columns: repeat(var(--columns), 1fr);
      gap: 10px;
      padding: 20px;
      max-width: 800px;
      margin: 0 auto;
    }
  
    .card {
      width: 100px;
      height: 100px;
      perspective: 1000px;
      cursor: pointer;
      position: relative;
    }
  
    .card .front, .card .back {
      position: absolute;
      width: 100%;
      height: 100%;
      backface-visibility: hidden;
      transition: transform 0.6s;
      border-radius: 5px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.2);
    }
  
    .card .back {
      background: #555;
      transform: rotateY(0deg);
    }
  
    .card .front {
      transform: rotateY(180deg);
    }
  
    .card.flipped .back {
      transform: rotateY(-180deg);
    }
  
    .card.flipped .front {
      transform: rotateY(0deg);
    }
  
    .card.matched .front {
      opacity: 0.5;
      filter: grayscale(100%);
    }
  
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      border-radius: 5px;
    }
  </style>