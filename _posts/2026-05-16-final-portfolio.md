---
layout: post
codemirror: true
title: Final Portfolio
description: An introduction showing how to create JavaScript games.  Game Builder will start the design process. lessons using the UI Runner help understatn the parts of  for game development, canvas graphics, DOM manipulation, and interactive visualizations.
permalink: /final-portfolio
---

This page maps every required CS 111 learning objective to exact lines from the Escape Game source files. All code excerpts are taken directly from the project.

---

## Object-Oriented Programming

### Writing Classes

The game has **six custom level classes** and a standalone **NpcAiChat class**, all built on top of the GameEngine base classes. Two representative examples are `GameLevelForestDeath` (extends the engine's level pattern with `Player` and `Npc`) and `NpcAiChat` (a fully custom class with its own constructor, state, and methods).

**`GameLevelForestDeath.js`** — constructor receives `gameEnv`, sets up sprite data, and registers a `this.classes` array that the engine reads to instantiate all game objects:

```js
// GameLevelForestDeath.js
class GameLevelForestDeath {
  constructor(gameEnv) {
    console.log("Initializing GameLevelForestDeath...");
    this.gameEnv = gameEnv;
    // ... sprite configs ...
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

**`NpcAiChat`** (in `GameLevelForestWin.js`) — a full custom class with constructor, private state (`history`, `container`), and multiple methods:

```js
// GameLevelForestWin.js
class NpcAiChat {
  constructor(npcName, systemPrompt, avatarSrc) {
    this.npcName      = npcName;
    this.systemPrompt = systemPrompt;
    this.avatarSrc    = avatarSrc;
    this.history      = [];
    this.container    = null;
  }
  isOpen() { ... }
  close()  { ... }
  open()   { ... }
  async _ask() { ... }
}
```

All six level classes (`GameLevelMaze`, `GameLevelMazeSub`, `GameLevelDoors`, `GameLevelForest`, `GameLevelForestSub`, `GameLevelForestDeath`, `GameLevelForestWin`) follow this same pattern, each with their own NPC configurations, sprite data, and transition logic.

---

### Methods & Parameters

Every NPC's `interact` and `reaction` functions are methods with internal logic. The `NpcAiChat` class has multiple methods with distinct parameter signatures:

```js
// NpcAiChat — constructor with 3 parameters
constructor(npcName, systemPrompt, avatarSrc) { ... }

// _bubble — 2 parameters, returns a DOM element
_bubble(text, who) {
  const row = document.createElement('div');
  // styles bubble differently depending on who === 'user' vs 'npc'
  ...
  return row;
}

// _ask — async, uses this.history and this.systemPrompt, returns reply string
async _ask() {
  const res = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 1000,
      system: this.systemPrompt,
      messages: this.history,
    }),
  });
  if (!res.ok) throw new Error(`API ${res.status}`);
  const data = await res.json();
  return data.content.find(b => b.type === 'text')?.text ?? '...';
}
```

The `cleanAndTransition` helper in `GameLevelDoors.js` is a method with 2 parameters:

```js
// GameLevelDoors.js
function cleanAndTransition(targetLevelClass, primaryGame) {
  // creates a fade overlay, clears the game container,
  // then calls primaryGame.transitionToLevel()
  ...
}
```

The `launchSublevel` helper in `GameLevelForestSub.js` takes 1 parameter and handles all game-in-game launching:

```js
// GameLevelForestSub.js
function launchSublevel(levelClass) {
  const primaryGame = gameEnv.gameControl;
  // ... fade, pause, new GameControl, start
}
```

---

### Instantiation & Objects

All game objects are instantiated through the `this.classes` array — a GameEngine pattern where each entry is an Object Literal with a `class` reference and a `data` config. The engine reads this array and calls `new class(data, gameEnv)` for each entry.

**`GameLevelMazeSub.js`** — the most complex level, instantiating `GameEnvBackground`, six `Barrier` platforms, a `Coin`, three `Npc` objects, and the `Player`:

```js
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

```js
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
  ...
  ...doorSprites.map(data => ({ class: Npc, data }))   // spread into class list
];
```

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

---

### Method Overriding

Every NPC sprite data object defines `react` and `interact` as overrides of the base `Npc` class methods. The `Strange Beckoner` in `GameLevelForestDeath.js` is one of the most complete overrides — `interact` maintains its own `_tauntIndex` state, builds dynamic dialogue, and triggers a level transition:

```js
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
    ...
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

The `reaction` function is overridden on every NPC across all files:

```js
// Pattern used across all NPCs (e.g. sprite_data_wraith in GameLevelForest.js)
reaction: function() {
  if (this.dialogueSystem) this.showReactionDialogue();
  else console.log(sprite_greet_wraith);
}
```

---

### Constructor Chaining

The `NpcAiChat` class demonstrates explicit constructor chaining — three instances are created inside `GameLevelForestWin`'s constructor, each receiving a unique name, system prompt, and avatar:

```js
// GameLevelForestWin.js — three NpcAiChat instances chained from parent constructor
const r2d2Chat     = new NpcAiChat('R2D2',          PERSONA_R2D2,    "/images/.../r2_idle.png");
const elderChat    = new NpcAiChat('Village Elder',  PERSONA_ELDER,   "/images/.../tux.png");
const villagerChat = new NpcAiChat('Villager',       PERSONA_VILLAGER,"/images/.../octocat.png");
```

`GameControl` is also chained with a `parentControl` reference so nested game-in-game instances can communicate back to their parent:

```js
// GameLevelForest.js — constructor chaining via GameControl
const gameInGame = new GameControl(gameEnv.game, levelArray, {
  parentControl: primaryGame    // parent passed into child constructor
});
gameInGame.start();
gameInGame.gameOver = function() {
  primaryGame.resume();         // child calls back to parent on completion
};
```

---

## Control Structures

### Iteration

**`for` loop** — `GameLevelDoors.js` Fisher-Yates shuffle to randomize door x-positions each run:

```js
// GameLevelDoors.js
const xPositions = [0.2, 0.35, 0.5, 0.65, 0.8];
for (let i = xPositions.length - 1; i > 0; i--) {
  const j = Math.floor(Math.random() * (i + 1));
  [xPositions[i], xPositions[j]] = [xPositions[j], xPositions[i]];
}
```

**`forEach` loop** — clearing game container children during every level transition:

```js
// GameLevelForestDeath.js
Array.from(gameContainer.children).forEach(child => {
  if (child.id !== 'promptDropDown') gameContainer.removeChild(child);
});
```

**`.map()` loop** — `GameLevelDoors.js` builds all 5 door sprite objects from a config array:

```js
// GameLevelDoors.js
const doorSprites = doorConfigs.map((cfg, i) => {
  const isCorrect = (i === correctIndex);
  return { ...doorDefaults, id: cfg.id, src: cfg.src, INIT_POSITION: { x: xPositions[i], y: 0.5 }, ... };
});
```

**`for` loop** — `NpcAiChat._typingBubble()` iterates to build the 3 animated typing dots:

```js
// GameLevelForestWin.js
for (let i = 0; i < 3; i++) {
  const d = document.createElement('span');
  d.style.animation = `npcDot 1s ease-in-out ${i * 0.18}s infinite`;
  b.appendChild(d);
}
```

---

### Conditionals

**Interaction guard** — used in every NPC's `interact` across all files, checking dialogue state before proceeding:

```js
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

```js
// GameLevelMazeSub.js — Exit Warden
const topGame = primaryGame?.parentControl || primaryGame;
if (topGame) {
  topGame.levelClasses      = [GameLevelDoors];
  topGame.currentLevelIndex = 0;
  topGame.isPaused          = false;
  topGame.transitionToLevel();
}
```

**API response check** — `NpcAiChat.close()` guards against acting on an already-removed panel:

```js
// GameLevelForestWin.js
close() {
  if (!this.isOpen()) return;   // guard: nothing to close
  const panel   = this.container.querySelector('.npc-chat-panel');
  const overlay = this.container.querySelector('.npc-chat-overlay');
  if (panel)   { panel.style.opacity = '0'; ... }
  if (overlay) { overlay.style.opacity = '0'; }
  ...
}
```

---

### Nested Conditions

`GameLevelDoors.js` has three levels of nesting — the outer `.map()` checks `isCorrect`, the middle level checks dialogue state, and the deepest level fires the level transition:

```js
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

`NpcAiChat.send()` in `GameLevelForestWin.js` also nests: empty-input guard → `try/catch` → reply vs. error:

```js
// GameLevelForestWin.js
const send = async () => {
  const text = input.value.trim();
  if (!text) return;                          // level 1: skip empty input
  ...
  try {
    const reply = await this._ask();
    if (reply) {                              // level 2: valid reply
      this.history.push({ role: 'assistant', content: reply });
      msgList.appendChild(this._bubble(reply, 'npc'));
    }
  } catch (e) {                              // level 2 else: error path
    msgList.appendChild(this._bubble('...I seem to have lost my words.', 'npc'));
    console.error('NPC AI error:', e);
  }
};
```

---

## Data Types

### Numbers

Position coordinates, scale factors, and animation rates are numeric properties used across every level:

```js
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

```js
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

```js
// GameLevelForest.js
const sprite_data_wraith = {
  id:       'The Wraith',
  greeting: "...it took my family. Both paths lead somewhere.",
  src:      "/images/projects/escape-game/tux.png",
};
```

Template literals appear in `NpcAiChat` for dynamic error messages and CSS:

```js
// GameLevelForestWin.js
throw new Error(`API ${res.status}`);

row.style.cssText = `display:flex;justify-content:${who === 'user' ? 'flex-end' : 'flex-start'}`;

d.style.animation = `npcDot 1s ease-in-out ${i * 0.18}s infinite`;
```

---

### Booleans

Boolean flags control gravity, dialogue open state, and NPC initialization:

```js
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

// Every NPC interact — isDialogueOpen() is a boolean guard
if (this.dialogueSystem && this.dialogueSystem.isDialogueOpen()) { ... }
```

---

### Arrays

Arrays store dialogue lines, door configs, position sets, chat history, and class lists:

```js
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

const xPositions = [0.2, 0.35, 0.5, 0.65, 0.8];

// GameLevelForestWin.js — chat history as growing array
this.history = [];
this.history.push({ role: 'user',      content: text  });
this.history.push({ role: 'assistant', content: reply });
```

---

### Objects (JSON)

Every sprite configuration is a nested Object Literal. The R2D2 config in `GameLevelForestSub.js` is a complete example:

```js
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
  dialogues:      [ ... ],
  reaction:       function() { ... },
  interact:       function() { ... }
};
```

The NPC AI API call body is also a JSON object:

```js
// GameLevelForestWin.js
body: JSON.stringify({
  model:      'claude-sonnet-4-20250514',
  max_tokens: 1000,
  system:     this.systemPrompt,
  messages:   this.history,
}),
```

---

## Operators

### Mathematical

Barrier geometry uses `*` and `Math.round`. The door shuffle uses `*` and `Math.floor`. The typing animation uses `*` for staggered delays. The taunt cycling uses `%`:

```js
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

```js
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

```js
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