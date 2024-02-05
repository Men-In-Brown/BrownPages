---
layout: post
title: Idea Findr
units: "1,2,3,4,5,6,7,8,9"
course: csp
---

<style>
    body {
      font-family: 'San Francisco Pro', 'Helvetica', sans-serif;
      background-color: #f0f0f0;
      margin: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100vh;
    }

    #ideaCard {
      width: 300px;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      background: linear-gradient(to bottom, #8e44ad, #673ab7);
      color: #fff;
      text-align: center;
      position: relative;
    }

    button {
      padding: 10px;
      font-size: 16px;
      cursor: pointer;
      border: none;
      border-radius: 5px;
      margin: 5px;
    }

    #swipeUp {
      background-color: #007bff;
      color: #fff;
    }

    #invest {
      background-color: #28a745;
      color: #fff;
    }

    #join {
      background-color: #dc3545;
      color: #fff;
    }

    #ideaName {
      margin-top: 10px;
    }

    #buttonContainer {
      display: flex;
      justify-content: space-between;
      margin-top: 20px;
    }

  .invisible-swipe-area {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    opacity: 0; /* Make it invisible */
    pointer-events: auto; /* Allow it to capture touch events */
  }

  .left-swipe-area {
    width: 30%; /* Adjust the width based on your preference */
  }

  .right-swipe-area {
    width: 30%; /* Adjust the width based on your preference */
    right: 0;
  }

  .top-swipe-area {
    height: 30%; /* Adjust the height based on your preference */
  }
</style>

<div id="ideaCard">
  <h2 id="ideaName"></h2>
  <p id="ideaDescription"></p>
</div>

<script>
  // Sample JSON data with ideas
  const ideas = [
    { name: "Idea 1", description: "Description for Idea 1" },
    { name: "Idea 2", description: "Description for Idea 2" },
    { name: "Idea 3", description: "Description for Idea 3" },
    { name: "Idea 4", description: "Description for Idea 3" },
    { name: "Idea 5", description: "Description for Idea 3" },
    { name: "Idea 6", description: "Description for Idea 3" },
    { name: "Idea 7", description: "Description for Idea 3" },
    { name: "Idea 8", description: "Description for Idea 3" },
    { name: "Idea 9", description: "Description for Idea 3" },
    { name: "Idea 10", description: "Description for Idea 3" },
    { name: "Idea 11", description: "Description for Idea 3" }
    // Add more ideas as needed
  ];

  let currentIdeaIndex = -1;

  function getRandomIdea() {
    return ideas[Math.floor(Math.random() * ideas.length)];
  }

  function displayIdea(idea) {
    document.getElementById("ideaName").innerText = idea.name;
    document.getElementById("ideaDescription").innerText = idea.description;
  }

  function swipeCard() {
    const idea = getRandomIdea();
    displayIdea(idea);
  }

  function invest() {
    const currentIdea = ideas[currentIdeaIndex];
    if (currentIdea) {
      console.log(`Invested ${currentIdea.name}!`);
    } else {
      console.log("No idea to invest.");
    }
  }

  function join() {
    const currentIdea = ideas[currentIdeaIndex];
    if (currentIdea) {
      console.log(`Joined ${currentIdea.name}!`);
    } else {
      console.log("No idea to join.");
    }
  }

  // Event listeners for arrow key presses
  document.addEventListener("keydown", function(event) {
    if (event.key === "ArrowUp") {
      swipeCard();
    } else if (event.key === "ArrowLeft") {
      invest();
    } else if (event.key === "ArrowRight") {
      join();
    }
  });

  let touchStartX = 0;
  let touchEndX = 0;

  // Function to handle touch start
  function handleTouchStart(event) {
    touchStartX = event.touches[0].clientX;
  }

  // Function to handle touch end
  function handleTouchEnd(event) {
    touchEndX = event.changedTouches[0].clientX;
    handleSwipe();
  }

  // Function to determine swipe direction and call corresponding function
  function handleSwipe() {
    const swipeDistance = touchEndX - touchStartX;

    if (swipeDistance > 50) {
      // Swipe right
      join();
    } else if (swipeDistance < -50) {
      // Swipe left
      invest();
    } else {
      // Swipe up
      swipeCard();
    }
  }

  // Event listeners for touch gestures
  document.getElementById("ideaCard").addEventListener("touchstart", handleTouchStart);
  document.getElementById("ideaCard").addEventListener("touchend", handleTouchEnd);

  // Initial swipe to show the first idea
  swipeCard();

</script>
<script>
  const ideaCard = document.getElementById("ideaCard");

  // Function to handle touch start
  function handleTouchStart(event) {
    touchStartX = event.touches[0].clientX;
  }

  // Function to handle touch end
  function handleTouchEnd(event) {
    touchEndX = event.changedTouches[0].clientX;
    handleSwipe();
  }

  // Function to determine swipe direction and call corresponding function
  function handleSwipe() {
    const swipeDistance = touchEndX - touchStartX;

    if (swipeDistance > 50) {
      // Swipe right
      join();
    } else if (swipeDistance < -50) {
      // Swipe left
      invest();
    } else {
      // Swipe up
      swipeCard();
    }
  }

  // Event listeners for touch gestures on the idea card
  ideaCard.addEventListener("touchstart", handleTouchStart);
  ideaCard.addEventListener("touchend", handleTouchEnd);

  // Create invisible div elements for swipe actions
  const leftSwipeArea = document.createElement("div");
  leftSwipeArea.classList.add("invisible-swipe-area", "left-swipe-area");
  leftSwipeArea.addEventListener("click", invest);

  const rightSwipeArea = document.createElement("div");
  rightSwipeArea.classList.add("invisible-swipe-area", "right-swipe-area");
  rightSwipeArea.addEventListener("click", join);

  const topSwipeArea = document.createElement("div");
  topSwipeArea.classList.add("invisible-swipe-area", "top-swipe-area");
  topSwipeArea.addEventListener("click", swipeCard);

  // Append invisible div elements to the body
  document.body.appendChild(leftSwipeArea);
  document.body.appendChild(rightSwipeArea);
  document.body.appendChild(topSwipeArea);

  // Initial swipe to show the first idea
  swipeCard();
</script>