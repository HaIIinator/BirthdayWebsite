<script>
    // Memory-Spiel Variablen
    let cards = [];
    let flippedCards = [];
    let moves = 0;
    let matches = 0;
    let columns = 6;
    let gameWon = false;
    
    // Quiz-Variablen
    let quizActive = false;
    let userAnswer = "";
    let quizCompleted = false;

    const generateCards = () => {
      const pairs = 1;
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
      gameWon = false;
      quizActive = false;
      quizCompleted = false;
      userAnswer = "";
    };
  
    const handleCardClick = (index) => {
      if (cards[index].isMatched || cards[index].isFlipped || flippedCards.length === 2 || gameWon) return;
  
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
          if (matches === cards.length / 2) {
            gameWon = true;
            setTimeout(() => {
              quizActive = true;
            }, 1500);
          }
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
  
    const nope = () => {
      alert("Sicha ned oida :)");
    };
    
    const checkAnswer = () => {
      if (userAnswer.toLowerCase() === "kuwait") {
        quizCompleted = true;
      } else {
        alert("Falsche Antwort! Versuche es erneut.");
      }
    };
    
    const giveHint = () => {
      alert("Das Land liegt auf einem Kontinent");
    };

    startNewGame();
</script>

{#if !gameWon && !quizActive}
  <div class="controls">
    <button on:click={startNewGame}>Neues Spiel</button>
    <button on:click={nope}>Leicht</button>
    <button on:click={nope}>Mittel</button>
    <button on:click={startNewGame}>Schwer</button>
  </div>
  
  <div class="stats">
    <p>Züge: {moves}</p>
    <p>Gefundene Paare: {matches}</p>
  </div>
  
  <div class="game-grid" style="--columns: {columns}">
    {#each cards as card, index (card.id)}
      <button
        class="card"
        class:flipped={card.isFlipped || card.isMatched}
        class:matched={card.isMatched}
        on:click={() => handleCardClick(index)}
        on:keydown={e => e.key === 'Enter' || e.key === ' ' ? handleCardClick(index) : null}
        disabled={card.isMatched || card.isFlipped || flippedCards.length === 2}
        aria-label={`Memory-Karte ${index + 1}, ${card.isMatched ? 'bereits gefunden' : ''}`}
      >
        <div class="front">
          <img src={card.imageUrl} alt={`Memory-Paar ${card.pairId}`} />
        </div>
        <div class="back"></div>
      </button>
    {/each}
  </div>
{:else if quizActive && !quizCompleted}
  <div class="quiz-container">
    <h2>Glückwunsch! Jetzt die nächste Aufgabe:</h2>
    <p>Erkennst du dieses Land?</p>
    <img src="data:image/png;base64,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" alt="Flagge von Kuwait" width="200" />
    <div class="quiz-input">
      <input 
        type="text" 
        bind:value={userAnswer} 
        placeholder="Land eingeben..."
        on:keydown={e => e.key === 'Enter' ? checkAnswer() : null}
      />
      <button on:click={checkAnswer}>Überprüfen</button>
      <button on:click={giveHint}>Tipp</button>
    </div>
  </div>
{:else if quizCompleted}
  <div class="quiz-completed">
    <h2>Herzlichen Glückwunsch!</h2>
    <p>Du hast beide Aufgaben erfolgreich gelöst!</p>
    <button on:click={startNewGame}>Neues Spiel starten</button>
  </div>
{/if}

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
    background: none;
    border: none;
    padding: 0;
    font: inherit;
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
  
  .quiz-container {
    text-align: center;
    padding: 20px;
    max-width: 600px;
    margin: 0 auto;
  }
  
  .quiz-input {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    gap: 10px;
  }
  
  .quiz-input input {
    padding: 10px;
    font-size: 16px;
  }
  
  .quiz-completed {
    text-align: center;
    padding: 40px;
  }
</style>