---
layout: post
codemirror: true
title: Sprint 6 Final Project
description: CS111 evidence portfolio covering OOP, control structures, data types, and operators drawn directly from the Escape Game source code.
permalink: /college-portfolio
---

# Final Portfolio

This page maps every required CS 111 learning objective to exact lines from the Escape Game source files. All code excerpts are taken directly from the project.

## CS111 Portfolio Checklist

### Object-Oriented Programming

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Writing Classes | `GameLevelForestDeath`, `NpcAiChat`, and 5 other level classes |
| ☑ | Methods & Parameters | `cleanAndTransition(targetLevelClass, primaryGame)`, `_bubble(text, who)`, `launchSublevel(levelClass)` |
| ☑ | Instantiation & Objects | `this.classes` arrays in every level; 12 objects in `GameLevelMazeSub` |
| ☑ | Inheritance (Basic) | `GameObject → Character → Player / Npc`, plus `Barrier`, `Coin`, `GameEnvBackground` |
| ☑ | Method Overriding | `interact` and `reaction` overridden on every NPC sprite config |
| ☑ | Constructor Chaining | `new NpcAiChat(...)` x3 in `GameLevelForestWin`; `new GameControl(..., { parentControl })` |

### Control Structures

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Iteration | Fisher-Yates `for` loop, `forEach` on container children, `.map()` for door sprites, `for` loop for typing dots |
| ☑ | Conditionals | NPC interaction guard (`if dialogueSystem && isDialogueOpen`), Exit Warden transition, `NpcAiChat.close()` guard |
| ☑ | Nested Conditions | 3-level nesting in `GameLevelDoors.js`; empty-input → `try/catch` → reply/error in `NpcAiChat.send()` |

### Data Types

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Numbers | `SCALE_FACTOR`, `STEP_FACTOR`, `ANIMATION_RATE`, pixel dimensions, `Math.round(rx * width)` |
| ☑ | Strings | `id: 'The Wraith'`, sprite `src` paths, template literals for CSS and error messages |
| ☑ | Booleans | `GRAVITY: false/true`, `isCorrect`, `isOpen()` returning `!!this.container && ...` |
| ☑ | Arrays | `dialogues[]`, `doorConfigs[]`, `xPositions[]`, `this.history[]`, `this.classes[]` |
| ☑ | Objects (JSON) | Full sprite config objects (`sprite_data_r2d2`), API request body via `JSON.stringify(...)` |

### Operators

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Mathematical | `Math.round(rx * width)`, `Math.floor(Math.random() * n)`, `% taunts.length`, `_tauntIndex++` |
| ☑ | String Operations | Template literals for CSS (`justify-content:${...}`), error strings (`API ${res.status}`), key lookup with `\|\|` fallback |
| ☑ | Boolean Expressions | `&&` interaction guard, `\|\|` fallback for `parentControl`, `!` negation, `?.` and `??` in API reply |

### Input / Output

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Keyboard Input | Player controlled via engine key event listeners (arrow keys / WASD) |
| ☑ | Canvas Rendering | `draw()` via GameEngine; sprites, backgrounds, platforms rendered each level |
| ☑ | GameEnv Configuration | `gameEnv` passed into every level constructor; `GameControl` and level arrays configured in each file |
| ☑ | API Integration | `fetch` POST to AI NPC API in `NpcAiChat._ask()` with model and messages |
| ☑ | Asynchronous I/O | `async _ask()` method; `await fetch(...)` in `NpcAiChat` |
| ☑ | JSON Parsing | `JSON.stringify(...)` for API body; `data.content.find(b => b.type === 'text')?.text ?? '...'` |

### Documentation

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Code Comments | Inline comments throughout all level files explaining logic |
| ☑ | Mini-Lesson Documentation | Portfolio page with embedded live game runners |
| ☑ | Code Highlights | Annotated code snippets with explanations for every objective |

### Debugging

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Console Debugging | `console.log("Initializing GameLevelForestDeath...")`, `console.error('NPC AI error:', e)`, `console.log` in all code runners |
| ☑ | Hit Box Visualization | Tuned widthPercentage and heightPercentage on all sprite hitboxes in GameLevelMazeSub.js by toggling the GameEngine's hitbox overlay |
| ☑ | Source-Level Debugging | Set a breakpoint on line 54 of GameLevelForestDeath.js to step through if (dist < 50 && !chaseState.caught) and find the correct catch threshold |
| ☑ | Network Debugging | Used the Network tab to find GameLevelForestSub.js returning a 404 while all other scripts loaded 200 — traced to a mismatched import path |
| ☑ | Application Debugging | Inspected the DOM to find stale canvas elements left behind after sublevel transitions; fixed with Array.from(gameContainer.children).forEach(...) cleanup |
| ☑ | Element Inspection | Console showed TypeError: Failed to fetch dynamically imported module from GameExecutor.js:338; traced the blob import failure back to a renamed file with an outdated import string |

### Testing & Verification

| Done | Objective | Evidence |
|------|-----------|----------|
| ☑ | Gameplay Testing | Live game runner embeds for every level in the portfolio |
| ☑ | Integration Testing | Live NPC AI chat in `GameLevelForestWin` |
| ☑ | API Error Handling | `try/catch` in `NpcAiChat.send()`; `console.error('NPC AI error:', e)` |

---

## Object-Oriented Programming

### Writing Classes

The game has **six custom level classes**, all built on top of the GameEngine base classes. An example of this is `GameLevelForestDeath` (extends the engine's level pattern with `Player` and `Npc`).

**`GameLevelForestDeath.js`** — constructor receives `gameEnv`, sets up sprite data, and registers a `this.classes` array that the engine reads to instantiate all game objects:

```javascript
// GameLevelForestDeath.js
class GameLevelForestDeath {
  constructor(gameEnv) {
    console.log("Initializing GameLevelForestDeath...");
    this.gameEnv = gameEnv;
    this.classes = [
      { class: GameEnvBackground, data: image_data_bg       },
      { class: Player,            data: sprite_data_player   },
      { class: Npc,               data: sprite_data_beckoner },
      { class: Npc,               data: sprite_data_victim   },
    ];
  }
}
export default GameLevelForestDeath;
```

All six level classes (`GameLevelMaze`, `GameLevelMazeSub`, `GameLevelDoors`, `GameLevelForest`, `GameLevelForestSub`, `GameLevelForestDeath`, `GameLevelForestWin`) follow this same pattern, each with their own NPC configurations, sprite data, and transition logic.

Play the Forest Death level to see `GameLevelForestDeath` instantiated and running:

{% capture challenge_oop_writing %}
Play the Forest Death level. Use WASD or arrow keys to move. Approach the NPCs to trigger interactions!
{% endcapture %}

{% capture code_oop_writing %}
import GameControl from "/assets/js/GameEnginev1.1/essentials/GameControl.js";
import GameLevelForestDeath from "/assets/js/GameEnginev1.1/GameLevelForestDeath.js";
import GameLevelForestSub from "/assets/js/GameEnginev1.1/GameLevelForestSub.js";

export const gameLevelClasses = [GameLevelForestDeath, GameLevelForestSub];
export { GameControl };
{% endcapture %}

{% include runners/game.html
   runner_id="game_oop_writing"
   challenge=challenge_oop_writing
   code=code_oop_writing
   height="500px"
%}

---

### Methods & Parameters

The `cleanAndTransition` helper in `GameLevelDoors.js` takes 2 parameters:

```javascript
// GameLevelDoors.js
function cleanAndTransition(targetLevelClass, primaryGame) {
  // creates a fade overlay, clears the game container,
  // then calls primaryGame.transitionToLevel()
}
```

The `launchSublevel` helper in `GameLevelForestSub.js` takes 1 parameter and handles all game-in-game launching:

```javascript
// GameLevelForestSub.js
function launchSublevel(levelClass) {
  const primaryGame = gameEnv.gameControl;
  // ... fade, pause, new GameControl, start
}
```

Run the code below to see the method signatures in action with real output:

{% capture challenge_oop_methods %}
Run the code to see the method signatures from the Escape Game printed with real output.
{% endcapture %}

{% capture code_oop_methods %}
// Mirrors cleanAndTransition(targetLevelClass, primaryGame) — GameLevelDoors.js
function cleanAndTransition(targetLevelClass, primaryGame) {
  return `Transitioning to: ${targetLevelClass} via: ${primaryGame}`;
}

// Mirrors _bubble(text, who) — NpcAiChat in GameLevelForestWin.js
function _bubble(text, who) {
  const align  = who === 'user' ? 'flex-end' : 'flex-start';
  const radius = who === 'user' ? '10px 10px 2px 10px' : '10px 10px 10px 2px';
  return { text, who, align, borderRadius: radius };
}

// Mirrors launchSublevel(levelClass) — GameLevelForestSub.js
function launchSublevel(levelClass) {
  return `Launching sublevel: ${levelClass}`;
}

console.log(cleanAndTransition('GameLevelForest', 'primaryGame'));
console.log(JSON.stringify(_bubble("I came from the left.", "npc"), null, 2));
console.log(JSON.stringify(_bubble("Go right.", "user"), null, 2));
console.log(launchSublevel('GameLevelForestDeath'));
{% endcapture %}

{% include runners/code.html
   runner_id="code_oop_methods"
   language="javascript"
   challenge=challenge_oop_methods
   code=code_oop_methods
%}

---

### Instantiation & Objects

All game objects are instantiated through the `this.classes` array — a GameEngine pattern where each entry is an Object Literal with a `class` reference and a `data` config. The engine reads this array and calls `new class(data, gameEnv)` for each entry.

**`GameLevelMazeSub.js`** — the most complex level, instantiating `GameEnvBackground`, six `Barrier` platforms, a `Coin`, three `Npc` objects, and the `Player`:

```javascript
// GameLevelMazeSub.js
this.classes = [
  { class: GameEnvBackground, data: image_data_cave },
  { class: Barrier, data: floor },
  { class: Barrier, data: step1 },
  { class: Barrier, data: step2 },
  { class: Barrier, data: step3 },
  { class: Barrier, data: step4 },
  { class: Barrier, data: step5 },
  { class: Coin,    data: sprite_data_coin    },
  { class: Npc,     data: sprite_data_shadow  },
  { class: Npc,     data: sprite_data_lantern },
  { class: Npc,     data: sprite_data_warden  },
  { class: Player,  data: sprite_data_octopus },
];
```

**`GameLevelDoors.js`** takes this further by building door instances dynamically in a loop, then spreading them into `this.classes`:

```javascript
// GameLevelDoors.js — dynamic instantiation via .map()
const doorSprites = doorConfigs.map((cfg, i) => {
  const isCorrect = (i === correctIndex);
  return {
    ...doorDefaults,
    id: cfg.id,
    src: cfg.src,
    INIT_POSITION: { x: xPositions[i], y: 0.5 },
    interact: isCorrect ? function() { ... } : function() { ... }
  };
});

this.classes = [
  { class: GameEnvBackground, data: image_data_water },
  { class: Player,            data: sprite_data_octopus },
  ...doorSprites.map(data => ({ class: Npc, data }))
];
```

The code runner below simulates what the engine does internally when it reads `this.classes`:

{% capture challenge_oop_instantiation %}
Run the code to simulate how the GameEngine reads this.classes and instantiates all 12 objects from GameLevelMazeSub.
{% endcapture %}

{% capture code_oop_instantiation %}
// Simulating GameEngine instantiation from this.classes

class GameEnvBackground { constructor(data) { this.type = 'Background'; this.name = data.name; } }
class Player  { constructor(data) { this.type = 'Player';  this.id = data.id; } }
class Barrier { constructor(data) { this.type = 'Barrier'; this.id = data.id; } }
class Npc     { constructor(data) { this.type = 'Npc';     this.id = data.id; } }
class Coin    { constructor(data) { this.type = 'Coin';    this.id = data.id; } }

// this.classes from GameLevelMazeSub.js — 12 entries
const classes = [
  { class: GameEnvBackground, data: { name: 'maze' } },
  { class: Barrier, data: { id: 'floor' } },
  { class: Barrier, data: { id: 'step1' } },
  { class: Barrier, data: { id: 'step2' } },
  { class: Barrier, data: { id: 'step3' } },
  { class: Barrier, data: { id: 'step4' } },
  { class: Barrier, data: { id: 'step5' } },
  { class: Coin,    data: { id: 'coin'  } },
  { class: Npc,     data: { id: 'Whispering Shadow' } },
  { class: Npc,     data: { id: 'Lantern Keeper'    } },
  { class: Npc,     data: { id: 'Exit Warden'       } },
  { class: Player,  data: { id: 'Octopus'           } },
];

// What the GameEngine does internally
const gameObjects = classes.map(entry => new entry.class(entry.data));

gameObjects.forEach(obj => {
  const label = obj.id ?? obj.name ?? '(unnamed)';
  console.log(`[${obj.type.padEnd(12)}] ${label}`);
});
console.log(`\nTotal objects instantiated: ${gameObjects.length}`);
{% endcapture %}

{% include runners/code.html
   runner_id="code_oop_instantiation"
   language="javascript"
   challenge=challenge_oop_instantiation
   code=code_oop_instantiation
%}

Play the Maze level to see all 12 of those objects running live:

{% capture challenge_oop_maze %}
Play the Maze Sub Level. Climb the staircase platforms to reach the Exit Warden and trigger a level transition!
{% endcapture %}

{% capture code_oop_maze %}
import GameControl from "/assets/js/GameEnginev1.1/essentials/GameControl.js";
import GameLevelMaze from "/assets/js/GameEnginev1.1/GameLevelMaze.js";
import GameLevelMazeSub from "/assets/js/GameEnginev1.1/GameLevelMazeSub.js";

export const gameLevelClasses = [GameLevelMaze, GameLevelMazeSub];
export { GameControl };
{% endcapture %}

{% include runners/game.html
   runner_id="game_oop_maze"
   challenge=challenge_oop_maze
   code=code_oop_maze
   height="500px"
%}

---

### Inheritance (Basic)

The game uses the GameEngine's built-in inheritance hierarchy. Every level instantiates objects from these chains:

```
GameObject  (GameEngine base)
  └─ Character
       ├─ Player      (used in every level as the controllable octopus)
       └─ Npc         (used for all NPCs: Wraith, Warden, R2D2, doors, etc.)
  └─ GameEnvBackground
  └─ Barrier          (used in GameLevelMazeSub for staircase platforms)
  └─ Coin             (used in GameLevelDoors, GameLevelMazeSub, GameLevelForestWin)
```

All levels use the `extends` keyword through imported engine classes. `NpcAiChat` in `GameLevelForestWin.js` is a standalone custom class not extending an engine base, showing both patterns side by side in the same file.

Play the full game to see every class in the hierarchy instantiated and running together:

{% capture challenge_oop_inheritance %}
Play the full Escape Game. Every class in the inheritance hierarchy is running live — Player, Npc, Barrier, Coin, and GameEnvBackground all instantiated from the same engine base.
{% endcapture %}

{% capture code_oop_inheritance %}
import GameControl from "/assets/js/GameEnginev1.1/essentials/GameControl.js";
import GameLevelMaze        from "/assets/js/GameEnginev1.1/GameLevelMaze.js";
import GameLevelMazeSub     from "/assets/js/GameEnginev1.1/GameLevelMazeSub.js";
import GameLevelDoors       from "/assets/js/GameEnginev1.1/GameLevelDoors.js";
import GameLevelForest      from "/assets/js/GameEnginev1.1/GameLevelForest.js";
import GameLevelForestSub   from "/assets/js/GameEnginev1.1/GameLevelForestSub.js";
import GameLevelForestWin   from "/assets/js/GameEnginev1.1/GameLevelForestWin.js";
import GameLevelForestDeath from "/assets/js/GameEnginev1.1/GameLevelForestDeath.js";

export const gameLevelClasses = [GameLevelMaze, GameLevelMazeSub, GameLevelDoors, GameLevelForest, GameLevelForestSub, GameLevelForestWin, GameLevelForestDeath];
export { GameControl };
{% endcapture %}

{% include runners/game.html
   runner_id="game_oop_inheritance"
   challenge=challenge_oop_inheritance
   code=code_oop_inheritance
   height="500px"
%}

---

### Method Overriding

Every NPC sprite data object defines `react` and `interact` as overrides of the base `Npc` class methods. The `Strange Beckoner` in `GameLevelForestDeath.js` is one of the most complete overrides — `interact` maintains its own `_tauntIndex` state, builds dynamic dialogue, and triggers a level transition:

```javascript
// GameLevelForestDeath.js — sprite_data_beckoner
interact: function() {
  if (this.dialogueSystem && this.dialogueSystem.isDialogueOpen()) {
    this.dialogueSystem.closeDialogue();
    return;
  }
  if (!this.dialogueSystem) this.dialogueSystem = new DialogueSystem();

  this._tauntIndex = (this._tauntIndex || 0);
  const taunts = [
    "Oh, you came back to talk to me? Interesting.",
    "Still here? I'd have thought the shame would have driven you off.",
    "You know the right path is still there. Not that it'll help you.",
    "BAWK BAWK. Classic. An absolute classic.",
    "Fine. The fork is back that way."
  ];
  const msg = taunts[this._tauntIndex % taunts.length];
  this._tauntIndex++;

  this.dialogueSystem.showDialogue(msg, "Strange Beckoner", this.spriteData.src);
  this.dialogueSystem.addButtons([
    {
      text: "Go back to the fork",
      action: () => {
        primaryGame.levelClasses      = [GameLevelForestSub];
        primaryGame.currentLevelIndex = 0;
        primaryGame.transitionToLevel();
      }
    },
    { text: "...", action: () => this.dialogueSystem.closeDialogue() }
  ]);
}
```

Run the code below to see the taunt cycling logic from `interact` in isolation:

{% capture challenge_oop_override %}
Run the code to see the Strange Beckoner's taunt cycle — the same logic from GameLevelForestDeath.js, cycling through taunts and wrapping back around.
{% endcapture %}

{% capture code_oop_override %}
// Strange Beckoner taunt cycle — mirrors GameLevelForestDeath.js

const taunts = [
  "Oh, you came back to talk to me? Interesting.",
  "Still here? I'd have thought the shame would have driven you off.",
  "You know the right path is still there. Not that it'll help you.",
  "BAWK BAWK. Classic. An absolute classic.",
  "Fine. The fork is back that way."
];

// Simulates pressing interact 7 times — cycles back after 5
let _tauntIndex = 0;
for (let press = 1; press <= 7; press++) {
  const msg = taunts[_tauntIndex % taunts.length];
  console.log(`Press ${press}: "${msg}"`);
  _tauntIndex++;
}
{% endcapture %}

{% include runners/code.html
   runner_id="code_oop_override"
   language="javascript"
   challenge=challenge_oop_override
   code=code_oop_override
%}

---

### Constructor Chaining

The `NpcAiChat` class demonstrates explicit constructor chaining — three instances are created inside `GameLevelForestWin`'s constructor, each receiving a unique name, system prompt, and avatar:

```javascript
// GameLevelForestWin.js — three NpcAiChat instances chained from parent constructor
const r2d2Chat     = new NpcAiChat('R2D2',          PERSONA_R2D2,    "/images/.../r2_idle.png");
const elderChat    = new NpcAiChat('Village Elder',  PERSONA_ELDER,   "/images/.../tux.png");
const villagerChat = new NpcAiChat('Villager',       PERSONA_VILLAGER,"/images/.../octocat.png");
```

`GameControl` is also chained with a `parentControl` reference so nested game-in-game instances can communicate back to their parent:

```javascript
// GameLevelForest.js — constructor chaining via GameControl
const gameInGame = new GameControl(gameEnv.game, levelArray, {
  parentControl: primaryGame
});
gameInGame.start();
gameInGame.gameOver = function() {
  primaryGame.resume();
};
```

Play the Forest Win level and talk to R2D2, the Village Elder, or the Villager — each one opens a separate `NpcAiChat` instance created from its own constructor chain:

{% capture challenge_oop_chaining %}
Play the Forest Win level. Walk up to R2D2, the Village Elder, or the Villager and interact with them. Each NPC has its own NpcAiChat instance created by constructor chaining!
{% endcapture %}

{% capture code_oop_chaining %}
import GameControl from "/assets/js/GameEnginev1.1/essentials/GameControl.js";
import GameLevelForestWin from "/assets/js/GameEnginev1.1/GameLevelForestWin.js";

export const gameLevelClasses = [GameLevelForestWin];
export { GameControl };
{% endcapture %}

{% include runners/game.html
   runner_id="game_oop_chaining"
   challenge=challenge_oop_chaining
   code=code_oop_chaining
   height="500px"
%}

---

## Control Structures

### Iteration

**`for` loop** — `GameLevelDoors.js` Fisher-Yates shuffle to randomize door x-positions each run:

```javascript
// GameLevelDoors.js
const xPositions = [0.2, 0.35, 0.5, 0.65, 0.8];
for (let i = xPositions.length - 1; i > 0; i--) {
  const j = Math.floor(Math.random() * (i + 1));
  [xPositions[i], xPositions[j]] = [xPositions[j], xPositions[i]];
}
```

**`forEach` loop** — clearing game container children during every level transition:

```javascript
// GameLevelForestDeath.js
Array.from(gameContainer.children).forEach(child => {
  if (child.id !== 'promptDropDown') gameContainer.removeChild(child);
});
```

**`.map()` loop** — `GameLevelDoors.js` builds all 5 door sprite objects from a config array:

```javascript
// GameLevelDoors.js
const doorSprites = doorConfigs.map((cfg, i) => {
  const isCorrect = (i === correctIndex);
  return { ...doorDefaults, id: cfg.id, src: cfg.src, INIT_POSITION: { x: xPositions[i], y: 0.5 } };
});
```

**`for` loop** — `NpcAiChat._typingBubble()` iterates to build the 3 animated typing dots:

```javascript
// GameLevelForestWin.js
for (let i = 0; i < 3; i++) {
  const d = document.createElement('span');
  d.style.animation = `npcDot 1s ease-in-out ${i * 0.18}s infinite`;
  b.appendChild(d);
}
```

Run the code below to see the shuffle and `.map()` produce a new door layout every time:

{% capture challenge_iteration %}
Run the code to see the Fisher-Yates shuffle and .map() from GameLevelDoors.js. Run it multiple times — the door layout changes every time!
{% endcapture %}

{% capture code_iteration %}
// Iteration — for loop shuffle + .map() from GameLevelDoors.js

const doorConfigs = [
  { id: 'Blue Door'   },
  { id: 'Brown Door'  },
  { id: 'Green Door'  },
  { id: 'Orange Door' },
  { id: 'Red Door'    },
];

// for loop — Fisher-Yates shuffle
const xPositions = [0.2, 0.35, 0.5, 0.65, 0.8];
console.log("Before shuffle:", [...xPositions]);
for (let i = xPositions.length - 1; i > 0; i--) {
  const j = Math.floor(Math.random() * (i + 1));
  [xPositions[i], xPositions[j]] = [xPositions[j], xPositions[i]];
}
console.log("After shuffle: ", [...xPositions]);

// .map() — builds door sprite data from config array
const correctIndex = Math.floor(Math.random() * doorConfigs.length);
const doorSprites  = doorConfigs.map((cfg, i) => ({
  id:        cfg.id,
  isCorrect: i === correctIndex,
  x:         xPositions[i],
}));

console.log("\nDoor layout this run:");
doorSprites.forEach(d =>
  console.log(`  ${d.id.padEnd(14)} x=${d.x}  ${d.isCorrect ? '<-- CORRECT' : ''}`)
);
{% endcapture %}

{% include runners/code.html
   runner_id="code_iteration"
   language="javascript"
   challenge=challenge_iteration
   code=code_iteration
%}

Play the Doors level to see the shuffle running live — the doors appear in a different order every time the page loads:

{% capture challenge_iteration_game %}
Play the Doors Level! The doors are shuffled into a random order every time. Find the correct door to advance to the Forest.
{% endcapture %}

{% capture code_iteration_game %}
import GameControl from "/assets/js/GameEnginev1.1/essentials/GameControl.js";
import GameLevelDoors from "/assets/js/GameEnginev1.1/GameLevelDoors.js";

export const gameLevelClasses = [GameLevelDoors];
export { GameControl };
{% endcapture %}

{% include runners/game.html
   runner_id="game_iteration"
   challenge=challenge_iteration_game
   code=code_iteration_game
   height="500px"
%}

---

### Conditionals

**Interaction guard** — used in every NPC's `interact` across all files, checking dialogue state before proceeding:

```javascript
// GameLevelForest.js — sprite_data_wraith
interact: function() {
  if (this.dialogueSystem && this.dialogueSystem.isDialogueOpen()) {
    this.dialogueSystem.closeDialogue();
    return;
  }
  if (!this.dialogueSystem) {
    this.dialogueSystem = new DialogueSystem();
  }
  this.showRandomDialogue();
}
```

**State transition conditional** — `GameLevelMazeSub.js` checks which game control to use before transitioning out of the maze:

```javascript
// GameLevelMazeSub.js — Exit Warden
const topGame = primaryGame?.parentControl || primaryGame;
if (topGame) {
  topGame.levelClasses      = [GameLevelDoors];
  topGame.currentLevelIndex = 0;
  topGame.isPaused          = false;
  topGame.transitionToLevel();
}
```

**Guard conditional** — `NpcAiChat.close()` guards against acting on an already-removed panel:

```javascript
// GameLevelForestWin.js
close() {
  if (!this.isOpen()) return;
  const panel   = this.container.querySelector('.npc-chat-panel');
  const overlay = this.container.querySelector('.npc-chat-overlay');
  if (panel)   { panel.style.opacity = '0'; }
  if (overlay) { overlay.style.opacity = '0'; }
}
```

Run the code below to see the dialogue guard and transition conditional in isolation:

{% capture challenge_conditionals %}
Run the code to see the NPC dialogue guard and state transition conditional from the Escape Game. Watch how the dialogue state changes with each interact call.
{% endcapture %}

{% capture code_conditionals %}
// Conditionals — NPC dialogue guard + state transition

const dialogueSystem = {
  _open: false,
  isDialogueOpen() { return this._open; },
  openDialogue()   { this._open = true;  console.log("  -> Dialogue opened"); },
  closeDialogue()  { this._open = false; console.log("  -> Dialogue closed"); },
};

function interact(dialogueSystem) {
  if (dialogueSystem && dialogueSystem.isDialogueOpen()) {
    dialogueSystem.closeDialogue();
    return;
  }
  dialogueSystem.openDialogue();
}

console.log("First interact (dialogue closed):");
interact(dialogueSystem);
console.log("Second interact (dialogue already open):");
interact(dialogueSystem);
console.log("Third interact (closed again):");
interact(dialogueSystem);

// Mirrors Exit Warden transition conditional from GameLevelMazeSub.js
const primaryGame = { parentControl: { name: 'topLevelGame' } };
const topGame = primaryGame?.parentControl || primaryGame;
console.log(`\nTransition target: ${topGame.name}`);
{% endcapture %}

{% include runners/code.html
   runner_id="code_conditionals"
   language="javascript"
   challenge=challenge_conditionals
   code=code_conditionals
%}

---

### Nested Conditions

`GameLevelDoors.js` has three levels of nesting — the outer `.map()` checks `isCorrect`, the middle level checks dialogue state, and the deepest level fires the level transition:

```javascript
// GameLevelDoors.js
const doorSprites = doorConfigs.map((cfg, i) => {
  const isCorrect = (i === correctIndex);           // level 1: correct door?

  return {
    interact: isCorrect
      ? function() {
          if (this.dialogueSystem && this.dialogueSystem.isDialogueOpen()) {  // level 2: dialogue open?
            this.dialogueSystem.closeDialogue();
            return;
          }
          if (!this.dialogueSystem) this.dialogueSystem = new DialogueSystem();
          this.dialogueSystem.addButtons([
            {
              text: "Enter",
              action: () => {
                this.dialogueSystem.closeDialogue();
                cleanAndTransition(GameLevelForest, gameEnv.gameControl); // level 3: fire transition
              }
            },
            { text: "Not yet", action: () => this.dialogueSystem.closeDialogue() }
          ]);
        }
      : function() {                                // else branch: dead end
          if (this.dialogueSystem && this.dialogueSystem.isDialogueOpen()) {
            this.dialogueSystem.closeDialogue();
            return;
          }
          if (!this.dialogueSystem) this.dialogueSystem = new DialogueSystem();
          this.dialogueSystem.showDialogue(cfg.deadEnd, "Dead End!", this.spriteData.src);
        }
  };
});
```

Run the code below to see the door picker nested logic produce different results each run:

{% capture challenge_nested %}
Run the code to see the nested door conditional logic from GameLevelDoors.js. The correct door changes every run — see which one it picks!
{% endcapture %}

{% capture code_nested %}
// Nested conditions — door picker from GameLevelDoors.js

const doorConfigs = [
  { id: 'Blue Door',   deadEnd: "The blue light flickers. Nothing lies beyond."  },
  { id: 'Brown Door',  deadEnd: "Splinters and cobwebs. Nothing lies beyond."    },
  { id: 'Green Door',  deadEnd: "Roots and earth block the way."                 },
  { id: 'Orange Door', deadEnd: "A wall of heat forces you back."                },
  { id: 'Red Door',    deadEnd: "A cold dread seizes your hand. Not this one."   },
];

const correctIndex = Math.floor(Math.random() * doorConfigs.length);
let dialogueOpen   = false;

function interact(i) {
  const isCorrect = (i === correctIndex);          // level 1

  if (dialogueOpen) {                              // level 2: dialogue already open
    console.log(`  [${doorConfigs[i].id}] Closing open dialogue`);
    dialogueOpen = false;
    return;
  }

  if (isCorrect) {
    dialogueOpen = true;
    console.log(`  [${doorConfigs[i].id}] Correct door! Showing transition prompt...`);
    console.log(`  -> Would call: cleanAndTransition(GameLevelForest, primaryGame)`); // level 3
  } else {
    dialogueOpen = true;
    console.log(`  [${doorConfigs[i].id}] Dead end: "${doorConfigs[i].deadEnd}"`);
  }
}

console.log(`Correct door this run: ${doorConfigs[correctIndex].id}\n`);
doorConfigs.forEach((_, i) => interact(i));
{% endcapture %}

{% include runners/code.html
   runner_id="code_nested"
   language="javascript"
   challenge=challenge_nested
   code=code_nested
%}

---

## Data Types

### Numbers

Position coordinates, scale factors, and animation rates are numeric properties used across every level:

```javascript
// GameLevelForest.js — numeric properties on sprite_data_octopus
const OCTOPUS_SCALE_FACTOR = 5;
const sprite_data_octopus = {
  SCALE_FACTOR:    OCTOPUS_SCALE_FACTOR,  // 5
  STEP_FACTOR:     1000,
  ANIMATION_RATE:  50,
  INIT_POSITION:   { x: 0.05, y: 0.85 },
  pixels:          { height: 250, width: 167 },
  hitbox:          { widthPercentage: 0.45, heightPercentage: 0.2 },
};
```

`GameLevelMazeSub.js` computes all barrier positions from numeric multiplication:

```javascript
// GameLevelMazeSub.js
function b(id, rx, ry, rw, rh) {
  return {
    x:      Math.round(rx * width),
    y:      Math.round(ry * height),
    width:  Math.round(rw * width),
    height: Math.round(rh * height),
  };
}
const step3 = b('step3', 0.41, 0.40, 0.22, 0.03);
```

---

### Strings

Character IDs, sprite paths, greeting text, and state strings are used throughout:

```javascript
// GameLevelForest.js
const sprite_data_wraith = {
  id:       'The Wraith',
  greeting: "...it took my family. Both paths lead somewhere.",
  src:      "/images/projects/escape-game/tux.png",
};
```

Template literals appear in `NpcAiChat` for dynamic error messages and CSS:

```javascript
// GameLevelForestWin.js
throw new Error(`API ${res.status}`);

row.style.cssText = `display:flex;justify-content:${who === 'user' ? 'flex-end' : 'flex-start'}`;

d.style.animation = `npcDot 1s ease-in-out ${i * 0.18}s infinite`;
```

---

### Booleans

Boolean flags control gravity, dialogue open state, and NPC initialization:

```javascript
// GameLevelForest.js — GRAVITY flag
GRAVITY: false,    // octopus floats in the forest level

// GameLevelMaze.js — GRAVITY flag flipped for dungeon
GRAVITY: true,

// GameLevelDoors.js — boolean for correct door selection
const isCorrect = (i === correctIndex);   // true for exactly one door

// GameLevelForestWin.js — NpcAiChat.isOpen() returns boolean
isOpen() {
  return !!this.container && document.body.contains(this.container);
}
```

---

### Arrays

Arrays store dialogue lines, door configs, position sets, chat history, and class lists:

```javascript
// GameLevelForest.js — dialogues array
dialogues: [
  "...it took my family. Both paths lead somewhere. Not all somewheres are safe.",
  "The trees shift when the fog comes in. I stopped trusting my eyes.",
  "I wandered left. I ended up here. I cannot leave.",
  "Follow the light... if you can find any."
],

// GameLevelDoors.js — array of door config objects
const doorConfigs = [
  { id: 'Blue Door',   src: "...", greeting: "...", deadEnd: "..." },
  { id: 'Brown Door',  src: "...", greeting: "...", deadEnd: "..." },
  { id: 'Green Door',  src: "...", greeting: "...", deadEnd: "..." },
  { id: 'Orange Door', src: "...", greeting: "...", deadEnd: "..." },
  { id: 'Red Door',    src: "...", greeting: "...", deadEnd: "..." },
];

// GameLevelForestWin.js — chat history as growing array
this.history = [];
this.history.push({ role: 'user',      content: text  });
this.history.push({ role: 'assistant', content: reply });
```

---

### Objects (JSON)

Every sprite configuration is a nested Object Literal. The R2D2 config in `GameLevelForestSub.js` is a complete example:

```javascript
// GameLevelForestSub.js
const sprite_data_right = {
  id:             'R2D2',
  greeting:       "I have been waiting for someone to choose correctly.",
  src:            "/images/projects/escape-game/r2_idle.png",
  SCALE_FACTOR:   8,
  ANIMATION_RATE: 100,
  pixels:         { height: 223, width: 505 },
  INIT_POSITION:  { x: 0.82,    y: 0.35   },
  orientation:    { rows: 1, columns: 3   },
  down:           { row: 0, start: 0, columns: 3 },
  hitbox:         { widthPercentage: 0.1, heightPercentage: 0.2 },
};
```

The NPC AI API call body is also a JSON object:

```javascript
// GameLevelForestWin.js
body: JSON.stringify({
  model:      'claude-sonnet-4-20250514',
  max_tokens: 1000,
  system:     this.systemPrompt,
  messages:   this.history,
}),
```

Run the code below to see all five data types printed from real sprite config values:

{% capture challenge_datatypes %}
Run the code to see all five data types — Numbers, Strings, Booleans, Arrays, and Objects — printed from real Escape Game sprite configs.
{% endcapture %}

{% capture code_datatypes %}
// All five data types from real Escape Game sprite configs

// Numbers
const OCTOPUS_SCALE_FACTOR = 5;
const sprite_data_octopus = {
  SCALE_FACTOR:   OCTOPUS_SCALE_FACTOR,
  STEP_FACTOR:    1000,
  ANIMATION_RATE: 50,
  INIT_POSITION:  { x: 0.05, y: 0.85 },
  pixels:         { height: 250, width: 167 },
};

// Strings
const sprite_data_wraith = {
  id:       'The Wraith',
  greeting: "...it took my family. Both paths lead somewhere.",
  src:      "/images/projects/escape-game/tux.png",
};

// Booleans
const forestGravity = false;
const mazeGravity   = true;
const isCorrect     = (2 === Math.floor(Math.random() * 5));

// Arrays
const dialogues  = [
  "...it took my family. Both paths lead somewhere.",
  "The trees shift when the fog comes in.",
  "I wandered left. I ended up here. I cannot leave.",
  "Follow the light... if you can find any."
];
const xPositions = [0.2, 0.35, 0.5, 0.65, 0.8];
const history    = [];
history.push({ role: 'user',      content: "Which way should I go?" });
history.push({ role: 'assistant', content: "The right path feels heavier." });

// Objects (JSON)
const sprite_data_r2d2 = {
  id:             'R2D2',
  SCALE_FACTOR:   8,
  pixels:         { height: 223, width: 505 },
  INIT_POSITION:  { x: 0.82,    y: 0.35   },
  orientation:    { rows: 1, columns: 3   },
  hitbox:         { widthPercentage: 0.1, heightPercentage: 0.2 },
};

console.log("=== Numbers ===");
console.log(`SCALE: ${sprite_data_octopus.SCALE_FACTOR}  STEP: ${sprite_data_octopus.STEP_FACTOR}  ANIM: ${sprite_data_octopus.ANIMATION_RATE}`);

console.log("\n=== Strings ===");
console.log(`id: "${sprite_data_wraith.id}"`);
console.log(`greeting: "${sprite_data_wraith.greeting}"`);

console.log("\n=== Booleans ===");
console.log(`forestGravity: ${forestGravity}  mazeGravity: ${mazeGravity}  isCorrect: ${isCorrect}`);

console.log("\n=== Arrays ===");
console.log(`dialogues[0]: "${dialogues[0]}"`);
console.log(`xPositions: [${xPositions}]`);
console.log(`history entries: ${history.length}`);

console.log("\n=== Object (JSON) ===");
console.log(JSON.stringify(sprite_data_r2d2, null, 2));
{% endcapture %}

{% include runners/code.html
   runner_id="code_datatypes"
   language="javascript"
   challenge=challenge_datatypes
   code=code_datatypes
%}

---

## Operators

### Mathematical

Barrier geometry uses `*` and `Math.round`. The door shuffle uses `*` and `Math.floor`. The typing animation uses `*` for staggered delays. The taunt cycling uses `%`:

```javascript
// GameLevelMazeSub.js
x:      Math.round(rx * width),
y:      Math.round(ry * height),
width:  Math.round(rw * width),
height: Math.round(rh * height),

// GameLevelDoors.js
const correctIndex = Math.floor(Math.random() * doorConfigs.length);

// GameLevelForestWin.js
d.style.animation = `npcDot 1s ease-in-out ${i * 0.18}s infinite`;

// GameLevelForestDeath.js
const msg = taunts[this._tauntIndex % taunts.length];
this._tauntIndex++;
```

---

### String Operations

Template literals construct dynamic strings for error messages, CSS values, and animation timing:

```javascript
// GameLevelForestWin.js
throw new Error(`API ${res.status}`);

row.style.cssText = `display:flex;justify-content:${who === 'user' ? 'flex-end' : 'flex-start'}`;

d.style.animation = `npcDot 1s ease-in-out ${i * 0.18}s infinite`;

// String key lookup in _greeting()
return {
  'R2D2':          'Bweeeep! You made it! Ask me anything!',
  'Village Elder': "We don't get many travellers here.",
  'Villager':      "Oh! A new face! It's been so long!",
}[this.npcName] || 'Hello, traveller.';
```

---

### Boolean Expressions

Compound `&&` and `||` expressions control NPC interaction guards and fallback state across all files:

```javascript
// Every NPC interact — && compound expression
if (this.dialogueSystem && this.dialogueSystem.isDialogueOpen()) {
  this.dialogueSystem.closeDialogue();
  return;
}

// GameLevelForestWin.js — && in isOpen()
return !!this.container && document.body.contains(this.container);

// GameLevelMazeSub.js — || fallback for nested game control
const topGame = primaryGame?.parentControl || primaryGame;

// GameLevelForestDeath.js — ! negation guard
if (!this.isOpen()) return;

// GameLevelForestWin.js — optional chaining + nullish coalescing
return data.content.find(b => b.type === 'text')?.text ?? '...';
```

Run the code below to see all three operator types produce output from real game logic:

{% capture challenge_operators %}
Run the code to see mathematical, string, and boolean operators all producing output from real Escape Game logic.
{% endcapture %}

{% capture code_operators %}
// Operators — mathematical, string, boolean from the Escape Game

// Mathematical — barrier geometry (GameLevelMazeSub.js)
const width = 800, height = 400;
function b(id, rx, ry, rw, rh) {
  return {
    id,
    x:      Math.round(rx * width),
    y:      Math.round(ry * height),
    width:  Math.round(rw * width),
    height: Math.round(rh * height),
  };
}
const step3 = b('step3', 0.41, 0.40, 0.22, 0.03);
console.log("=== Mathematical ===");
console.log(`step3: x=${step3.x} y=${step3.y} w=${step3.width} h=${step3.height}`);

let _tauntIndex = 3;
const taunts = ["Taunt A", "Taunt B", "Taunt C"];
console.log(`Modulo taunt (3 % 3 = ${3 % 3}): "${taunts[_tauntIndex % taunts.length]}"  next index: ${++_tauntIndex}`);

const correctIndex = Math.floor(Math.random() * 5);
console.log(`Correct door index (Math.floor * Math.random): ${correctIndex}`);

// String Operations — template literals (GameLevelForestWin.js)
console.log("\n=== String Operations ===");
const who = 'user', status = 400;
console.log(`CSS justify: "justify-content:${who === 'user' ? 'flex-end' : 'flex-start'}"`);
console.log(`Error string: "API ${status}"`);
const npcName = 'R2D2';
const greeting = {
  'R2D2':          'Bweeeep! You made it! Ask me anything!',
  'Village Elder': "We don't get many travellers here.",
  'Villager':      "Oh! A new face! It's been so long!",
}[npcName] || 'Hello, traveller.';
console.log(`Key lookup for "${npcName}": "${greeting}"`);

// Boolean Expressions — &&, ||, !, ?., ??
console.log("\n=== Boolean Expressions ===");
const dialogueSystem = { _open: true, isDialogueOpen() { return this._open; } };
console.log(`&& guard (dialogue open): ${dialogueSystem && dialogueSystem.isDialogueOpen()}`);
const primaryGame = { parentControl: null };
const topGame = primaryGame?.parentControl || primaryGame;
console.log(`|| fallback — topGame is primaryGame: ${topGame === primaryGame}`);
console.log(`! negation (!false): ${!false}`);
const data = { content: [{ type: 'text', text: 'Hello traveller.' }] };
const reply = data.content.find(b => b.type === 'text')?.text ?? '...';
console.log(`?. and ?? reply: "${reply}"`);
{% endcapture %}

{% include runners/code.html
   runner_id="code_operators"
   language="javascript"
   challenge=challenge_operators
   code=code_operators
%}

Play the full game one more time as a final demo — every operator, data type, and control structure on this page is running live inside:

{% capture challenge_operators_game %}
Play the full Escape Game as a final demo! Every operator, data type, and control structure covered in this portfolio is running live inside.
{% endcapture %}

{% capture code_operators_game %}
import GameControl from "/assets/js/GameEnginev1.1/essentials/GameControl.js";
import GameLevelMaze        from "/assets/js/GameEnginev1.1/GameLevelMaze.js";
import GameLevelMazeSub     from "/assets/js/GameEnginev1.1/GameLevelMazeSub.js";
import GameLevelDoors       from "/assets/js/GameEnginev1.1/GameLevelDoors.js";
import GameLevelForest      from "/assets/js/GameEnginev1.1/GameLevelForest.js";
import GameLevelForestSub   from "/assets/js/GameEnginev1.1/GameLevelForestSub.js";
import GameLevelForestWin   from "/assets/js/GameEnginev1.1/GameLevelForestWin.js";
import GameLevelForestDeath from "/assets/js/GameEnginev1.1/GameLevelForestDeath.js";

export const gameLevelClasses = [GameLevelMaze, GameLevelMazeSub, GameLevelDoors, GameLevelForest, GameLevelForestSub, GameLevelForestWin, GameLevelForestDeath];
export { GameControl };
{% endcapture %}

{% include runners/game.html
   runner_id="game_operators_final"
   challenge=challenge_operators_game
   code=code_operators_game
   height="500px"
%}

---

## Input / Output

### Keyboard Input

The player is controlled entirely through keyboard events. The `keypress` map on every Player sprite data config binds WASD keys to directions — `87` is W (up), `65` is A (left), `83` is S (down), `68` is D (right). The GameEngine reads this map each frame and moves the player accordingly:

```javascript
// GameLevelForest.js — keypress map on sprite_data_octopus
keypress: { up: 87, left: 65, down: 83, right: 68 }
```

Every level file defines this same map identically on its Player config. The NPC `interact` is also triggered by a key event (the E key), which the engine routes to whichever NPC the player is overlapping.

### Canvas Rendering

All sprites, backgrounds, platforms, and NPCs are drawn to a `<canvas>` element each frame via the GameEngine's `draw()` loop. Each level's `this.classes` array tells the engine what to instantiate; the engine then calls `draw()` on every game object every tick.

The `ANIMATION_RATE` and `orientation` properties on each sprite config control which frame of the spritesheet is drawn:

```javascript
// GameLevelForestDeath.js — sprite_data_beckoner animation config
ANIMATION_RATE: 80,
pixels:      { height: 255, width: 150 },
orientation: { rows: 1, columns: 1 },
down:        { row: 0, start: 0, columns: 1 },
```

### GameEnv Configuration

`gameEnv` is passed into every level constructor and gives each level access to the canvas dimensions, the asset path, and the running `GameControl` instance. All sprite positions and barrier sizes are computed from `gameEnv.innerWidth` and `gameEnv.innerHeight` so the game scales to any screen:

```javascript
// GameLevelMazeSub.js — gameEnv consumed in constructor
constructor(gameEnv) {
  let width  = gameEnv.innerWidth;
  let height = gameEnv.innerHeight;
  let path   = gameEnv.path;

  // All positions derived from width/height — never hardcoded pixels
  const ledge3 = {
    x:      Math.round(width  * 0.40),
    y:      Math.round(height * 0.43),
    width:  Math.round(width  * 0.55),
    height: 18,
  };
}
```

### DOM Output

Every level transition creates a full-screen black `<div>` overlay using the DOM API and animates it with CSS transitions:

```javascript
// GameLevelMazeSub.js — fade overlay built and injected at transition time
const fade = document.createElement('div');
Object.assign(fade.style, {
  position: 'fixed',
  top: '0', left: '0',
  width: '100%', height: '100%',
  backgroundColor: '#000',
  opacity: '0',
  transition: 'opacity 0.8s ease-in-out',
  zIndex: '9999',
  pointerEvents: 'none'
});
document.body.appendChild(fade);

requestAnimationFrame(() => {
  fade.style.opacity = '1';
  setTimeout(() => {
    fade.style.opacity = '0';
    setTimeout(() => {
      if (fade.parentNode) fade.parentNode.removeChild(fade);
    }, 800);
  }, 800);
});
```

Run the code below to see the fade overlay sequencing in isolation:

{% capture challenge_dom %}
Run the code to see the fade overlay transition sequence from GameLevelMazeSub.js simulated with console output.
{% endcapture %}

{% capture code_dom %}
// DOM Output — fade overlay sequencing from every level transition

function simulateFadeTransition(targetLevel) {
  const fade = {
    style: { opacity: '0', transition: 'opacity 0.8s ease-in-out' },
    parentNode: true,
  };

  console.log(`[append]   opacity=${fade.style.opacity} — overlay added to DOM`);

  fade.style.opacity = '1';
  console.log(`[rAF]      opacity=${fade.style.opacity} — fading to black`);

  console.log(`[timeout]  screen is black — transitioning to: ${targetLevel}`);

  fade.style.opacity = '0';
  console.log(`[fade out] opacity=${fade.style.opacity} — fading back in`);

  console.log(`[remove]   fade removed from DOM\n`);
}

simulateFadeTransition('GameLevelDoors');
simulateFadeTransition('GameLevelForestWin');
{% endcapture %}

{% include runners/code.html
   runner_id="code_dom"
   language="javascript"
   challenge=challenge_dom
   code=code_dom
%}

### Asynchronous I/O

The game uses `requestAnimationFrame` and nested `setTimeout` calls to sequence all level transitions without blocking the browser. `BeckonerChaseController` in `GameLevelForestDeath.js` uses elapsed real time to ramp up the chase speed gradually:

```javascript
// GameLevelForestDeath.js — BeckonerChaseController.update()
update() {
  const elapsed = (Date.now() - this.startTime) / 1000;
  if (elapsed < 3) return;   // does nothing for the first 3 seconds

  // Speed increases the longer the player stays — capped at 2.5
  const speed = Math.min(1.2 + (elapsed - 3) * 0.025, 2.5);
}
```

Run the code below to see the speed ramp logic in isolation:

{% capture challenge_async %}
Run the code to see the BeckonerChaseController speed ramp from GameLevelForestDeath.js. Watch how the speed increases over time and caps at 2.5!
{% endcapture %}

{% capture code_async %}
// Async I/O — BeckonerChaseController timing from GameLevelForestDeath.js

const startTime = Date.now() - 5000;   // simulate 5 seconds already elapsed

function update() {
  const elapsed = (Date.now() - startTime) / 1000;
  if (elapsed < 3) {
    console.log(`elapsed=${elapsed.toFixed(1)}s — chase not started yet`);
    return;
  }
  const speed = Math.min(1.2 + (elapsed - 3) * 0.025, 2.5);
  console.log(`elapsed=${elapsed.toFixed(1)}s — speed=${speed.toFixed(3)} px/frame`);
}

update();

console.log('\nSpeed ramp over time:');
[0, 1, 2, 3, 5, 10, 30, 60].forEach(t => {
  const speed = t < 3 ? 0 : Math.min(1.2 + (t - 3) * 0.025, 2.5);
  const bar = '█'.repeat(Math.round(speed * 8));
  console.log(`  t=${String(t).padStart(2)}s  speed=${speed.toFixed(3)}  ${bar}`);
});
{% endcapture %}

{% include runners/code.html
   runner_id="code_async"
   language="javascript"
   challenge=challenge_async
   code=code_async
%}

### JSON Parsing

Sprite config objects are deeply nested JSON-style object literals throughout every file. The `doorSprites` array in `GameLevelDoors.js` is built by spreading a shared `doorDefaults` object into each individual door config:

```javascript
// GameLevelDoors.js — spreading doorDefaults into each door sprite config
const doorDefaults = {
  SCALE_FACTOR: 8, ANIMATION_RATE: 50,
  pixels:      { height: 414, width: 252 },
  orientation: { rows: 1, columns: 1 },
  hitbox:      { widthPercentage: 0.1, heightPercentage: 0.2 },
};

const doorSprites = doorConfigs.map((cfg, i) => ({
  ...doorDefaults,           // spread shared defaults in
  id:            cfg.id,
  src:           cfg.src,
  INIT_POSITION: { x: xPositions[i], y: 0.5 },
}));
```

Run the code below to see the spread and `JSON.stringify` in action:

{% capture challenge_json %}
Run the code to see the object spread and JSON.stringify from GameLevelDoors.js. Each door gets all shared defaults merged with its own config!
{% endcapture %}

{% capture code_json %}
// JSON — object spread + JSON.stringify from GameLevelDoors.js

const doorDefaults = {
  SCALE_FACTOR:   8,
  ANIMATION_RATE: 50,
  pixels:         { height: 414, width: 252 },
  orientation:    { rows: 1, columns: 1 },
  hitbox:         { widthPercentage: 0.1, heightPercentage: 0.2 },
};

const doorConfigs = [
  { id: 'Blue Door',   src: 'bluedoor.png'   },
  { id: 'Brown Door',  src: 'browndoor.png'  },
  { id: 'Green Door',  src: 'greendoor.png'  },
  { id: 'Orange Door', src: 'orangedoor.png' },
  { id: 'Red Door',    src: 'reddoor.png'    },
];

const xPositions = [0.2, 0.35, 0.5, 0.65, 0.8];
const correctIndex = Math.floor(Math.random() * doorConfigs.length);

const doorSprites = doorConfigs.map((cfg, i) => ({
  ...doorDefaults,
  id:            cfg.id,
  src:           cfg.src,
  INIT_POSITION: { x: xPositions[i], y: 0.5 },
  isCorrect:     i === correctIndex,
}));

console.log('Correct door this run:', doorConfigs[correctIndex].id);
console.log('\nAll door sprites (JSON.stringify):');
doorSprites.forEach(d => {
  console.log(JSON.stringify({
    id: d.id, x: d.INIT_POSITION.x, isCorrect: d.isCorrect, pixels: d.pixels
  }));
});
{% endcapture %}

{% include runners/code.html
   runner_id="code_json"
   language="javascript"
   challenge=challenge_json
   code=code_json
%}

---

## Documentation

### Code Comments

Every level file opens with a description comment and has inline comments throughout explaining the purpose of each block. `GameLevelMazeSub.js` has the most detailed comments — it includes an ASCII diagram of the staircase layout right in the source:

```javascript
// GameLevelMazeSub.js — ASCII layout comment
//
//   ledge5  ░░░░░░░░░░░░░░░░░░░░░░░░░░░  (x 0.55–0.95, y ~0.12)
//   ledge4  ░░░░░░░░░░░░░░░░░░░░         (x 0.10–0.60, y ~0.27)
//   ledge3  ░░░░░░░░░░░░░░░░░░░░░░░░░░░  (x 0.40–0.95, y ~0.43)
//   ledge2  ░░░░░░░░░░░░░░░░░░░░         (x 0.05–0.55, y ~0.58)
//   ledge1  ░░░░░░░░░░░░░░░░░░░░░░░░░░░  (x 0.00–0.50, y ~0.78)
//   floor   ═══════════════════════════  (y 0.90)
//
// The player starts above ledge1 and must climb up-right, up-left, …
```

### Mini-Lesson Documentation

This portfolio page itself is the mini-lesson document. Each section maps one CS111 objective to exact source code, with a prose explanation of the concept, a direct code excerpt from the relevant level file, a code runner block where the logic runs live in the browser, and a game runner block where the actual level can be played.

---

### Code Highlights

Each section above includes annotated excerpts pulled directly from the source files, with comments pointing to the specific thing being demonstrated. The code runner blocks serve as runnable highlights: they strip the game engine out and run just the logic being discussed, so you can see the exact output without needing to play through the whole game to reach that code path.

---

## Debugging

### Console Debugging

Every level constructor starts with a `console.log` confirming initialization. Transition code uses `console.warn` for recoverable failures:

```javascript
// Every level file — initialization confirmation
console.log("Initializing GameLevelForestDeath...");
console.log("Initializing GameLevelMazeSub...");
console.log("Initializing GameLevelDoors...");

// GameLevelDoors.js — logs the correct door at load time
console.log('Correct door this run:', doorConfigs[correctIndex].id);

// Every transition function — warn instead of crashing on optional calls
console.warn('Could not hide parent canvas state', e);
```

### Hit Box Visualization

The GameEngine supports toggling hitbox outlines on every game object, which was used during development of `GameLevelMazeSub.js` to verify that the zigzag ledge barriers were positioned correctly. The hitbox percentages on each sprite config were tuned using this tool:

```javascript
// GameLevelMazeSub.js — narrow hitbox tuned via hitbox visualization
hitbox: { widthPercentage: 0.45, heightPercentage: 0.2 }
```

### Source-Level Debugging

Browser DevTools source-level debugging was used when the `BeckonerChaseController` in `GameLevelForestDeath.js` wasn't correctly detecting the caught condition. Setting a breakpoint inside `update()` revealed the catch threshold needed to be higher:

```javascript
// GameLevelForestDeath.js — catch threshold found via step-through debugging
const dist = Math.hypot(dx, dy) || 1;
if (dist < 50 && !chaseState.caught) {   // value found by watching dist in DevTools
  chaseState.caught = true;
  this.onCaught(beckoner, player);
}
```
![Breakpoint set on line 54 of GameLevelForestDeath.js in Chrome DevTools](images/breakpoint.png)

### Network Debugging

The browser's Network tab was used to verify that sprite images and level assets were loading from the correct paths. Early on, several levels used a mismatched `path` prefix that caused images to 404 silently:

```javascript
// GameLevelForest.js — path prefix verified via Network tab
let path = gameEnv.path;
const sprite_data_wraith = {
  src: path + "/images/gamify/tux.png",   // Network tab confirmed this resolved correctly
};
```
![Network tab showing GameLevelForestSub.js returning a 404](images/network-error.png)

### Application Debugging

The browser's Application tab was used to track down a bug where transitioning between nested sublevels was leaving stale canvas elements in the DOM. The fix was the `Array.from(gameContainer.children).forEach(...)` cleanup that now appears in every transition function:

```javascript
// GameLevelMazeSub.js — cleanup added after DOM inspection revealed stale canvases
const gameContainer = document.getElementById('gameContainer');
if (gameContainer) {
  Array.from(gameContainer.children).forEach(child => {
    if (child.id !== 'promptDropDown') gameContainer.removeChild(child);
  });
}
```

### Element Inspection

The browser's Element Inspector was used to fix incorrect import paths. Early in development, several levels were importing from the wrong directory. Inspecting the `<script>` elements that the engine injected into the page and checking their `src` attributes made it possible to see which paths were resolving and which weren't.

![Chrome DevTools console showing a failed dynamic import error](images/element-inspect.png)

---

## Testing & Verification

### Gameplay Testing

Every level has a live game runner embed in this portfolio so the level can be played directly on the page. The game runners are still live below — clicking into any of them confirms the level is functional right now:

{% capture challenge_testing_game %}
Play the full Escape Game for final verification! All levels, NPCs, transitions, and AI chat are live and testable right here.
{% endcapture %}

{% capture code_testing_game %}
import GameControl from "/assets/js/GameEnginev1.1/essentials/GameControl.js";
import GameLevelMaze        from "/assets/js/GameEnginev1.1/GameLevelMaze.js";
import GameLevelMazeSub     from "/assets/js/GameEnginev1.1/GameLevelMazeSub.js";
import GameLevelDoors       from "/assets/js/GameEnginev1.1/GameLevelDoors.js";
import GameLevelForest      from "/assets/js/GameEnginev1.1/GameLevelForest.js";
import GameLevelForestSub   from "/assets/js/GameEnginev1.1/GameLevelForestSub.js";
import GameLevelForestWin   from "/assets/js/GameEnginev1.1/GameLevelForestWin.js";
import GameLevelForestDeath from "/assets/js/GameEnginev1.1/GameLevelForestDeath.js";

export const gameLevelClasses = [GameLevelMaze, GameLevelMazeSub, GameLevelDoors, GameLevelForest, GameLevelForestSub, GameLevelForestWin, GameLevelForestDeath];
export { GameControl };
{% endcapture %}

{% include runners/game.html
   runner_id="game_testing_final"
   challenge=challenge_testing_game
   code=code_testing_game
   height="500px"
%}

### Integration Testing

The most complex integration point is the nested `GameControl` chain: `GameLevelMaze` → `GameLevelMazeSub` (sublevel) → `GameLevelDoors` (via `parentControl`). This was tested by playing all the way through and confirming that the parent `GameControl` correctly pauses when the sublevel launches, and `topGame.transitionToLevel()` correctly transitions to `GameLevelDoors`:

```javascript
// GameLevelMazeSub.js — parentControl chain tested end-to-end
const topGame = primaryGame?.parentControl || primaryGame;
if (topGame) {
  topGame.levelClasses      = [GameLevelDoors];
  topGame.currentLevelIndex = 0;
  topGame.isPaused          = false;
  topGame.transitionToLevel();
}
```

### Error Handling

Every level transition wraps the optional `hideCanvasState()` call in a `try/catch` with a `console.warn`. `BeckonerChaseController` guards every property access with optional chaining to avoid crashing if game objects haven't finished initializing:

```javascript
// GameLevelForest.js — try/catch around optional engine method
try {
  if (typeof primaryGame.hideCanvasState === 'function') {
    primaryGame.hideCanvasState();
  }
} catch(e) {
  console.warn('Could not hide parent canvas state', e);
}

// GameLevelForestDeath.js — optional chaining guards in BeckonerChaseController
const player   = this.gameEnv.gameObjects?.find(o => o?.spriteData?.id === 'Octopus');
const beckoner = this.gameEnv.gameObjects?.find(o => o?.spriteData?.id === 'Strange Beckoner');
if (!player || !beckoner) return;   // bail out safely if objects not ready yet
```

Run the code below to see both patterns in isolation:

{% capture challenge_error %}
Run the code to see the try/catch and optional chaining error-handling patterns from the Escape Game tested against three different scenarios.
{% endcapture %}

{% capture code_error %}
// Error handling — try/catch + optional chaining from GameLevelForestDeath.js

function safeHideCanvas(primaryGame) {
  try {
    if (typeof primaryGame.hideCanvasState === 'function') {
      primaryGame.hideCanvasState();
      console.log('[try]  hideCanvasState() succeeded');
    } else {
      console.log('[try]  hideCanvasState not a function — skipped safely');
    }
  } catch(e) {
    console.warn('[catch] Could not hide canvas state:', e.message);
  }
}

console.log('=== Game with hideCanvasState ===');
safeHideCanvas({ hideCanvasState: () => {} });

console.log('\n=== Game without hideCanvasState ===');
safeHideCanvas({});

console.log('\n=== Game where hideCanvasState throws ===');
safeHideCanvas({ hideCanvasState: () => { throw new Error('Canvas locked'); } });

// Mirrors optional chaining in BeckonerChaseController.update()
console.log('\n=== Optional chaining guard ===');
const gameEnv = { gameObjects: null };
const player = gameEnv.gameObjects?.find(o => o?.spriteData?.id === 'Octopus');
console.log(`player found: ${player ?? 'undefined — bailed out safely'}`);
{% endcapture %}

{% include runners/code.html
   runner_id="code_error"
   language="javascript"
   challenge=challenge_error
   code=code_error
%}