---
title: "Virtual Board Game"
---

## Virtual Board Game – Team Java Project  

## **Febuary - April 2025**

**Tech stack:** Java, OOP, Agile & Waterfall methodologies, Jira  

---

## Overview

I worked in a team of five to design and implement a **virtual board game** in Java. The project challenged us to apply both **Agile** and **Waterfall** frameworks in practice, helping us understand the trade-offs between structured planning and iterative development.  

---

## Problem → Solution → Impact  

- **Problem:** Build a playable text-based game in a limited time frame, balancing creativity with realistic project scope.  
- **Solution:** Applied **object-oriented programming** principles to structure the game logic (players, rules, board state). Negotiated scope as a team to ensure we delivered a **minimum viable product** while still including engaging features.  
- **Impact:** Delivered a functional game alongside a report documenting the development process. Strengthened my technical expertise, teamwork, and communication skills.  

---

## Screenshots  

| Game Intro | Main Menu |
|-----------------|------------------|
| {{< figure src="/assets/boardGameIntro.png" width="1000px" >}}  | {{< figure src="/assets/boardMap.png" width="1000px" >}}  |

| Taking a Turn |
|-----------------|
| {{< figure src="/assets/takingTurn.png" width="1000px" >}} |

---

## Code Snippet  

```java
/**
 * @param args game run here
 */
public static void main(String[] args) {

typewriter.printWithDelay(ascii.getWelcomeArt(), 1);

displayGameRules();
printAllPrices();

typewriter.printWithDelay("It is recommended you put your console into fullscreen mode!", DEFAULT_TEXT_DELAY_SPEED);

setNumberOfPlayersAndNames();

establishTurnOrder();

playTheGame();

quitGame(null);
}

/**
* Logic for running the game and permitting a player to take a turn
*/
public static void playTheGame() {

int turnCounter = 1;
int totalTurns = players.size() * TURN_LIMIT_PER_PLAYER;

do {

    for (Player player : players) {
        System.out.println(); // new line for spacing

        // map of board and updates is displayed at the start of each turn
        displayBoardMap(board, players);

        typewriter.printWithDelay("\nIt is " + player.getDisplayName() + "'s turn!", DEFAULT_TEXT_DELAY_SPEED);
        typewriter.printWithDelay("\nTurn "+ turnCounter + " out of "+ totalTurns , DEFAULT_TEXT_DELAY_SPEED);

        // takes a turn
        player.takeTurn(board, dice);

        boolean turnOver = false;

        while (!turnOver) {
            turnOver = showDynamicMenu(player, board.getSquare(player.getPosition()));
        }

        // shows the player's updated balance of resources after a turn
        player.displayPlayerResources();

        player.resetTurn(); // Call this at the start of the player's turn
        turnCounter++;

        // displays board and updates at the start of each turn
        // displayBoardMap(board, players);
    }
} while (turnCounter <= totalTurns);

// tells players if and when they run out of turns
if (turnCounter >= totalTurns) {
    typewriter.printWithDelay("You're out of turns!", DEFAULT_TEXT_DELAY_SPEED);
    }
}
