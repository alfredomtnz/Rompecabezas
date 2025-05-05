<script>
  const gridSize = 4;
  const totalPieces = gridSize * gridSize;

  let pieces = [];
  let selected = [];
  let imageUrl = '';
  let imageLoaded = false;

  function handleImageUpload(event) {
    const file = event.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = () => {
        imageUrl = reader.result;
        imageLoaded = true;
        generatePieces();
      };
      reader.readAsDataURL(file);
    }
  }

  function shuffle(array) {
    let currentIndex = array.length;
    let randomIndex;

    while (currentIndex !== 0) {
      randomIndex = Math.floor(Math.random() * currentIndex);
      currentIndex--;
      [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
    }
    return array;
  }

  function generatePieces() {
    if (imageLoaded) {
      pieces = shuffle(Array.from({ length: totalPieces }, (_, i) => i));
    }
  }

  function selectPiece(index) {
    if (selected.length === 0) {
      selected = [index];
    } else if (selected.length === 1) {
      if (selected[0] !== index) {
        [pieces[selected[0]], pieces[index]] = [pieces[index], pieces[selected[0]]];
      }
      selected = [];
      checkWin();
    }
  }

  function checkWin() {
    const won = pieces.every((piece, index) => piece === index);
    if (won) alert('🎉 ¡Puzzle completado!');
  }
</script>

<style>
  body {
    margin: 0;
    font-family: 'Segoe UI', sans-serif;
    background: #f4f4f9;
  }

  .container {
    text-align: center;
    padding: 40px 20px;
  }

  h1 {
    margin-bottom: 20px;
    color: #333;
    font-size: 2rem;
  }

  .upload-btn {
    padding: 12px 24px;
    font-size: 1rem;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    margin-bottom: 30px;
  }

  .upload-btn:hover {
    background-color: #0056b3;
  }

  .puzzle {
    display: grid;
    grid-template-columns: repeat(4, 150px);
    grid-template-rows: repeat(4, 150px);
    gap: 4px;
    margin: 0 auto 20px;
    width: 608px;
  }

  .piece {
    width: 150px;
    height: 150px;
    border-radius: 6px;
    background-size: 600px 600px;
    background-repeat: no-repeat;
    background-color: #eee;
    transition: transform 0.2s;
    cursor: pointer;
  }

  .piece:hover {
    transform: scale(1.03);
  }

  .selected {
    outline: 4px solid limegreen;
  }

  .shuffle-btn {
    padding: 10px 20px;
    font-size: 1rem;
    background-color: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    cursor: pointer;
  }

  .shuffle-btn:hover {
    background-color: #218838;
  }
</style>

<div class="container">
  <h1>🧩ROMPECABEZAS</h1>

  <input class="upload-btn" type="file" accept="image/*" on:change={handleImageUpload} />

  {#if imageUrl}
    <div class="puzzle">
      {#each pieces as piece, index}
        <div
          role="button"
          tabindex="0"
          class="piece {selected.includes(index) ? 'selected' : ''}"
          on:click={() => selectPiece(index)}
          on:keydown={(e) => e.key === 'Enter' && selectPiece(index)}
          style="background-image: url({imageUrl}); background-position: {-150 * (piece % 4)}px {-150 * Math.floor(piece / 4)}px"
        ></div>
      {/each}
    </div>

    <button class="shuffle-btn" on:click={generatePieces}>🔀 Revolver Piezas</button>
  {/if}
</div>
