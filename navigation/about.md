---
layout: post
title: About
permalink: /about/
comments: true
---

## As a conversation Starter

Here are some places I come from. 

<comment>
Flags are made using Wikipedia images
</comment>

<style>
    .grid-container {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
        gap: 10px;
    }
    .grid-item {
        text-align: center;
    }
    .grid-item img {
        width: 100%;
        height: 100px;
        object-fit: contain;
    }
    .grid-item p {
        margin: 5px 0;
    }

    .image-gallery {
        display: flex;
        flex-wrap: nowrap;
        overflow-x: auto;
        gap: 10px;
    }

    .image-gallery img {
        max-height: 150px;
        object-fit: cover;
        border-radius: 5px;
    }
</style>


<div class="grid-container" id="grid_container"></div>


## Favorite Video Games
<div class="grid-container" id="games_container"></div>

<script>

var container = document.getElementById("grid_container");
var gamesContainer = document.getElementById("games_container");

var http_source = "https://upload.wikimedia.org/wikipedia/commons/";

var living_in_the_world = [
    {
        flag: "0/01/Flag_of_California.svg",
        greeting: "Hey",
        description: "California - forever"
    },
    {
        flag: "f/fa/Flag_of_the_People%27s_Republic_of_China.svg",
        greeting: "你好",
        description: "China - this is where my mother is from"
    },
    {
        flag: "a/a4/Flag_of_the_United_States.svg",
        greeting: "Hello",
        description: "United States - this is where my father is from"
    }
];

for (const location of living_in_the_world) {
    var item = document.createElement("div");
    item.className = "grid-item";

    var img = document.createElement("img");
    img.src = http_source + location.flag;
    img.alt = location.description + " Flag";

    var desc = document.createElement("p");
    desc.textContent = location.description;

    var greet = document.createElement("p");
    greet.textContent = location.greeting;

    item.appendChild(img);
    item.appendChild(desc);
    item.appendChild(greet);

    container.appendChild(item);
}


var http_source = "https://upload.wikimedia.org/wikipedia/";
var favorite_games = [
    {
        title: "Counter Strike",
        image: "en/f/f2/CS2_Cover_Art.jpg",
        note: "Best FPS game right now"
    },
    {
        title: "Valorant",
        image: "en/b/ba/Valorant_cover.jpg",
        note: "Only fun with friends"
    },
    {
        title: "Hollow Knight",
        image: "en/0/04/Hollow_Knight_first_cover_art.webp",
        note: "Best platformer game ever"
    },
    {
        title: "Cuphead",
        image: "en/e/eb/Cuphead_%28artwork%29.png",
        note: "Pure rage fuel"
    }

];


for (const game of favorite_games) {
    var item = document.createElement("div");
    item.className = "grid-item";

    var img = document.createElement("img");
    img.src = http_source + game.image;
    img.alt = game.title;

    var title = document.createElement("p");
    title.textContent = game.title;
    title.style.fontWeight = "bold";

    var note = document.createElement("p");
    note.textContent = game.note;

    item.appendChild(img);
    item.appendChild(title);
    item.appendChild(note);

    gamesContainer.appendChild(item);
}
</script>

### Journey through Life

Here are some of my hobbies or things I like to do

- Playing video games  
- Drawing  
- Sleeping  
- Skateboarding  
- Playing trumpet  

### Culture, Family, and Fun

- My family comes from many places, such as China, England, and Germany.  
- I have one sibling and one dog.  
- The gallery of pics has some of my family, fun, and culture.  

<comment>
Gallery of Pics, scroll to the right for more ...
</comment>

<div class="image-gallery">
  <img src="{{site.baseurl}}/images/about/some_guy.jpg" alt="Image 1">
  <img src="{{site.baseurl}}/images/about/aura.JPG" alt="Image 2">
  <img src="{{site.baseurl}}/images/about/chonk.JPG" alt="Image 3">
  <img src="{{site.baseurl}}/images/about/sprite.JPG" alt="Image 4">
  <img src="{{site.baseurl}}/images/about/67.JPEG" alt="Image 5">
  <img src="{{site.baseurl}}/images/about/band.jpeg" alt="Image 6">
  <img src="{{site.baseurl}}/images/about/knight.jpeg" alt="Image 7">
  <img src="{{site.baseurl}}/images/about/blorb Background Removed.png" alt="Image 8">
</div>
