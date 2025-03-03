# HistoricLE_1
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>HistoConnections</title>
    <style>
      @import url("https://fonts.googleapis.com/css2?family=Lobster&family=Playfair+Display:wght@400;700&display=swap");

      body {
        font-family: "Playfair Display", serif;
        background-color: #d2b48c;
        color: black;
        text-align: center;
        margin: 0;
        padding: 0;
      }
      .header {
        background: #8b4513;
        color: white;
        padding: 15px;
        font-size: 42px;
        font-family: "Lobster", cursive;
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 10px 30px;
      }
      .header a {
        color: white;
        font-size: 24px;
        text-decoration: none;
        cursor: pointer;
      }
      .container {
        width: 90%;
        max-width: 1000px;
        margin: auto;
        padding-top: 20px;
      }
      .instructions {
        font-size: 18px;
        margin-bottom: 15px;
        color: black;
      }
      .button-grid {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 15px;
        margin-top: 20px;
      }
      .word-btn {
        padding: 18px;
        background-color: #a0522d;
        border: 2px solid transparent;
        cursor: pointer;
        font-size: 18px;
        color: white;
        border-radius: 8px;
        transition: transform 0.3s ease, background 0.3s ease;
      }
      .word-btn:hover {
        background-color: #8b4513;
      }
      .word-btn.selected {
        border: 3px solid white;
        transform: scale(1.02);
      }
      .correct-group {
        background: #f5deb3;
        color: black;
        font-weight: bold;
        padding: 20px;
        margin-top: 10px;
        border-radius: 10px;
        width: 100%;
        text-align: center;
        font-size: 24px;
        transition: all 0.5s ease-in-out;
      }
      .category-details {
        display: none;
        background: #fff8dc;
        padding: 15px;
        margin-top: 10px;
        border-radius: 10px;
        font-size: 18px;
        text-align: left;
      }
      .mistakes {
        margin-top: 20px;
        font-size: 20px;
        color: black;
      }
    </style>
  </head>
  <body>
    <div class="header">
      <div>HistoConnections</div>
      <a href="#">About</a>
    </div>
    <div class="container">
      <div class="instructions">
        Select four related words to form a category. Incorrect choices will
        turn red and reduce your remaining guesses.
      </div>
      <div id="categories"></div>
      <div class="button-grid" id="words"></div>
      <div class="mistakes">
        Mistakes Remaining: <span id="mistakes">3</span>
      </div>
      <div id="category-details" class="category-details"></div>
    </div>

    <script>
      const categories = {
        "Assassinated World Leaders": {
          words: [
            "J.F.K.",
            "Julius Caesar",
            "Abraham Lincoln",
            "Archduke Franz Ferdinand",
          ],
          description:
            "These leaders were assassinated due to political or ideological motives.",
        },
        "British in India": {
          words: [
            "Gandhi",
            "Sepoy Rebellion",
            "British East India Company",
            "Robert Clive",
          ],
          description:
            "Key figures and events related to British colonial rule in India.",
        },
        "WWII Things": {
          words: [
            "Operation Barbarossa",
            "Neville Chamberlain",
            "Blitzkrieg",
            "D-Day",
          ],
          description:
            "Important events, people, and strategies from World War II.",
        },
        "Scientific Revolution": {
          words: [
            "Isaac Newton",
            "Galileo Galilei",
            "Johannes Kepler",
            "Francis Bacon",
          ],
          description:
            "Pioneers who contributed to modern science during the Scientific Revolution.",
        },
      };

      let selected = [];
      let words = shuffleArray(
        Object.values(categories).flatMap((cat) => cat.words)
      );
      let mistakesLeft = 3;

      function shuffleArray(array) {
        for (let i = array.length - 1; i > 0; i--) {
          const j = Math.floor(Math.random() * (i + 1));
          [array[i], array[j]] = [array[j], array[i]];
        }
        return array;
      }

      function createButtons() {
        const wordContainer = document.getElementById("words");
        wordContainer.innerHTML = "";
        words.forEach((word) => {
          let btn = document.createElement("button");
          btn.textContent = word;
          btn.classList.add("word-btn");
          btn.onclick = () => selectWord(word, btn);
          wordContainer.appendChild(btn);
        });
      }

      function selectWord(word, btn) {
        if (selected.includes(word)) {
          selected = selected.filter((w) => w !== word);
          btn.classList.remove("selected");
        } else if (selected.length < 4) {
          selected.push(word);
          btn.classList.add("selected");
        }
        if (selected.length === 4) {
          checkSelection();
        }
      }

      function checkSelection() {
        let correctCategory = null;
        for (let category in categories) {
          if (
            categories[category].words.every((word) => selected.includes(word))
          ) {
            correctCategory = category;
            break;
          }
        }

        if (correctCategory) {
          document.querySelectorAll(".word-btn").forEach((btn) => {
            if (selected.includes(btn.textContent)) {
              btn.remove();
            }
          });
          displayCategory(correctCategory);
          selected = [];
        } else {
          mistakesLeft--;
          document.getElementById("mistakes").textContent = mistakesLeft;
          document.querySelectorAll(".word-btn.selected").forEach((btn) => {
            btn.classList.add("incorrect");
            setTimeout(() => {
              btn.classList.remove("incorrect", "selected");
            }, 1000);
          });
          if (mistakesLeft === 0) {
            alert("Game Over! Refresh to try again.");
            document
              .querySelectorAll(".word-btn")
              .forEach((btn) => (btn.disabled = true));
          }
          selected = [];
        }
      }

      function displayCategory(category) {
        const categoryContainer = document.getElementById("categories");
        let div = document.createElement("div");
        div.classList.add("correct-group");
        div.textContent = category;
        categoryContainer.appendChild(div);

        const detailsContainer = document.getElementById("category-details");
        detailsContainer.innerHTML = `<strong>${category}:</strong> ${categories[category].description}`;
        detailsContainer.style.display = "block";
      }

      createButtons();
    </script>
  </body>
</html>
