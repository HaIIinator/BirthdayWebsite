<script>
  // Memory-Spiel Variablen
  let cards = [];
  let flippedCards = [];
  let moves = 0;
  let matches = 0;
  let columns = 6;
  let gameWon = false;
  
  // Slider-Variablen
  let sliderActive = false;
  let sliderValue = 5000;
  let sliderCompleted = false;

  // Kuwait-Quiz Variablen
  let kuwaitQuizActive = false;
  let countryInput = "";
  let kuwaitCompleted = false;

  // Finaler Slider
  let finalSliderActive = false;
  let finalSliderValue = 500;
  let finalSliderCompleted = false;

  // Geschenk-Variablen
  let showGift = false;
  let giftPosition = { x: 0, y: 0 };
  let giftSpeed = { x: 3, y: 3 };
  let giftVisible = false;

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
    gameWon = false;
    sliderActive = false;
    sliderCompleted = false;
    sliderValue = 5000;
    kuwaitQuizActive = false;
    countryInput = "";
    kuwaitCompleted = false;
    finalSliderActive = false;
    finalSliderValue = 500;
    finalSliderCompleted = false;
    showGift = false;
    giftVisible = false;
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
            sliderActive = true;
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
  
  const checkSlider = () => {
    if (sliderValue === 8888) {
      sliderCompleted = true;
      setTimeout(() => {
        kuwaitQuizActive = true;
      }, 1000);
    } else {
      alert(`Fast! Du hast ${sliderValue}. Ziel ist 8888.`);
    }
  };

  const checkCountry = () => {
    if (countryInput.toLowerCase() === "kuwait") {
      kuwaitCompleted = true;
      setTimeout(() => {
        finalSliderActive = true;
      }, 1000);
    } else {
      alert("Falsches Land! Versuche es nochmal.");
    }
  };

  const giveHint = () => {
    alert("Das Land liegt auf einem Kontinent");
  };

  const checkFinalSlider = () => {
    if (finalSliderValue === 888) {
      finalSliderCompleted = true;
      // Starte das fliegende Geschenk nach einer Verzögerung
      setTimeout(() => {
        giftVisible = true;
        startGiftAnimation();
      }, 2000);
    } else {
      alert(`Fast! Du hast ${finalSliderValue}. Ziel ist 888.`);
    }
  };

  const startGiftAnimation = () => {
    // Initiale Position des Geschenks
    giftPosition = { x: -100, y: 50 };
    showGift = true;
    
    // Animation starten
    const animate = () => {
      if (!giftVisible) return;
      
      // Position aktualisieren
      giftPosition.x += giftSpeed.x;
      giftPosition.y += giftSpeed.y;
      
      // Wenn es den Bildschirm verlässt, von vorne beginnen
      if (giftPosition.x > window.innerWidth + 100) {
        giftPosition.x = -100;
        giftPosition.y = Math.random() * (window.innerHeight - 100);
      }
      
      // Wenn es den oberen oder unteren Rand berührt, Richtung ändern
      if (giftPosition.y < 0 || giftPosition.y > window.innerHeight - 100) {
        giftSpeed.y = -giftSpeed.y;
      }
      
      // Nächster Frame
      requestAnimationFrame(animate);
    };
    
    animate();
  };

  const handleGiftClick = () => {
    alert("Du host afoch zeit verschwendet schau afoch auf Steam ;-)");
    giftVisible = false;
  };

  startNewGame();
</script>

{#if !gameWon && !sliderActive && !kuwaitQuizActive && !finalSliderActive && !finalSliderCompleted}
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
{:else if sliderActive && !sliderCompleted}
<div class="slider-container">
  <h2>Glückwunsch! Jetzt die nächste Aufgabe:</h2>
  <p>Ziehe den Slider genau auf <strong>8888</strong></p>
  
  <div class="slider-wrapper">
    <input 
      type="range" 
      min="1" 
      max="10000" 
      bind:value={sliderValue}
      class="slider"
    />
    <div class="slider-value">{sliderValue}</div>
  </div>
  
  <button on:click={checkSlider} class:success={sliderValue === 8888}>
    {sliderValue === 8888 ? 'Weiter' : 'Überprüfen'}
  </button>
</div>
{:else if kuwaitQuizActive && !kuwaitCompleted}
<div class="kuwait-quiz">
  <h2>Nächste Herausforderung:</h2>
  <p>Erkennst du dieses Land?</p>
  <img src='kuwait.png' alt="bild">
  <div class="kuwait-outline"></div>
  <div class="input-wrapper">
    <input type="text" bind:value={countryInput} placeholder="Land eingeben" />
    <button on:click={checkCountry}>Überprüfen</button>
    <button on:click={giveHint}>Tipp</button>
  </div>
</div>
{:else if finalSliderActive && !finalSliderCompleted}
<div class="slider-container">
  <h2>Letzte Aufgabe!</h2>
  <p>Ziehe den Slider genau auf <strong>888</strong></p>
  
  <div class="slider-wrapper">
    <input 
      type="range" 
      min="1" 
      max="1000" 
      bind:value={finalSliderValue}
      class="slider"
    />
    <div class="slider-value">{finalSliderValue}</div>
  </div>
  
  <button on:click={checkFinalSlider} class:success={finalSliderValue === 888}>
    {finalSliderValue === 888 ? 'Abschließen' : 'Überprüfen'}
  </button>
</div>
{:else if finalSliderCompleted}
<div class="completed">
  <h2>Herzlichen Glückwunsch!</h2>
  <p>Du hast alle Aufgaben erfolgreich gelöst!</p>
  
  <div class="video-container">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/dQw4w9WgXcQ?autoplay=1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
  {#if giftVisible}
    <div 
      class="gift" 
      style="left: {giftPosition.x}px; top: {giftPosition.y}px"
      on:click={handleGiftClick}
    >
      🎁
    </div>
  {/if}
  
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
  cursor: pointer;
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

.slider-container, .kuwait-quiz {
  text-align: center;
  padding: 20px;
  max-width: 600px;
  margin: 0 auto;
}

.slider-wrapper {
  margin: 40px 0;
}

.slider {
  width: 100%;
  max-width: 500px;
  margin: 20px 0;
}

.slider-value {
  font-size: 2em;
  font-weight: bold;
  margin: 10px 0;
}

button.success {
  background-color: #4CAF50;
  color: white;
}

.completed {
  text-align: center;
  padding: 40px;
  position: relative;
  overflow: hidden;
}

.video-container {
  margin: 20px auto;
  max-width: 560px;
}

.gift {
  position: absolute;
  font-size: 50px;
  cursor: pointer;
  z-index: 100;
  user-select: none;
}

.kuwait-outline {
  width: 200px;
  height: 150px;
  margin: 20px auto;
  background-image: url('/favicon.png');
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
  filter: brightness(0) invert(1);
}

.input-wrapper {
  margin-top: 30px;
}

input {
  padding: 10px;
  margin-right: 10px;
  font-size: 1em;
}
</style>