---
layout: post
title: Idea Finder
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
</style>

<div id="ideaCard">
  <h2 id="ideaName"></h2>
  <p id="ideaDescription"></p>
  
  <div id="buttonContainer">
    <button id="invest" onclick="invest()">&#60; Invest</button>
    <button id="swipeUp" onclick="swipeCard()">Swipe Up</button>
    <button id="join" onclick="join()">Join &#62;</button>
  </div>
</div>

<script>
  // Sample JSON data with ideas
  const ideas = [
    { name: "Idea 1", description: "Description for Idea 1" },
    { name: "Idea 2", description: "Description for Idea 2" },
    { name: "Idea 3", description: "Description for Idea 3" },
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

  // Initial swipe to show the first idea
  swipeCard();
</script>