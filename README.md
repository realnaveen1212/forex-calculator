<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Language Trainer Dark</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">

<style>
:root{
  --bg:#0f172a;
  --card:#1e293b;
  --primary:#3b82f6;
  --text:#e2e8f0;
}

body{
  margin:0;
  font-family:Poppins;
  background:var(--bg);
  color:var(--text);
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
}

/* MAIN CONTAINER */
.container{
  width:90%;
  max-width:700px;
}

/* INPUT AREA */
.input-box{
  background:var(--card);
  padding:20px;
  border-radius:15px;
  box-shadow:0 10px 30px rgba(0,0,0,0.4);
}

textarea{
  width:100%;
  height:90px;
  border-radius:10px;
  padding:10px;
  border:none;
  background:#0f172a;
  color:white;
}

input{
  width:100%;
  padding:10px;
  margin-top:10px;
  border-radius:10px;
  border:none;
  background:#0f172a;
  color:white;
}

button{
  margin-top:10px;
  padding:10px;
  border:none;
  border-radius:10px;
  background:var(--primary);
  color:white;
  cursor:pointer;
  width:100%;
}

/* VIEWER */
.viewer{
  margin-top:20px;
  height:320px;
  border-radius:20px;
  background:#020617;
  display:flex;
  justify-content:center;
  align-items:center;
  border:2px solid #1e40af;
}

.viewer img{
  max-height:100%;
}

.word{
  font-size:70px;
  font-weight:600;
  letter-spacing:2px;
}

/* SAVED AREA */
.saved{
  margin-top:15px;
}

.item{
  background:#1e293b;
  padding:10px;
  border-radius:10px;
  margin-top:5px;
  display:flex;
  justify-content:space-between;
}
</style>
</head>

<body>

<div class="container">

<div class="input-box">

<label>Enter words (one per line)</label>
<textarea id="wordsInput">cat
dog</textarea>

<input type="file" id="imageInput" multiple>

<label>Speed (seconds)</label>
<input type="number" id="speed" value="2">

<button onclick="start()">Start</button>
<button onclick="save()">Save Set</button>

</div>

<div class="viewer" id="viewer">Preview</div>

<div class="saved" id="savedList"></div>

</div>

<script>
let words = [];
let images = [];
let interval;

/* LOAD IMAGES */
document.getElementById("imageInput").addEventListener("change", (e)=>{
  images = [];
  for(let file of e.target.files){
    images.push(URL.createObjectURL(file));
  }
});

/* START */
function start(){
  clearInterval(interval);

  words = document.getElementById("wordsInput").value
    .split("\n")
    .filter(w => w.trim() !== "");

  let speed = document.getElementById("speed").value * 1000;

  interval = setInterval(showRandom, speed);
}

/* SHOW RANDOM */
function showRandom(){
  let viewer = document.getElementById("viewer");
  viewer.innerHTML = "";

  let useImage = Math.random() > 0.5;

  if(useImage && images.length){
    let img = document.createElement("img");
    img.src = images[Math.floor(Math.random()*images.length)];
    viewer.appendChild(img);
  } 
  else if(words.length){
    let div = document.createElement("div");
    div.className="word";
    div.innerText = words[Math.floor(Math.random()*words.length)];
    viewer.appendChild(div);
  }
}

/* SAVE SYSTEM */
function getData(){
  return JSON.parse(localStorage.getItem("sets") || "[]");
}

function setData(data){
  localStorage.setItem("sets", JSON.stringify(data));
}

function save(){
  let name = prompt("Set name?");
  if(!name) return;

  let data = getData();
  data.push({name, words, images});
  setData(data);
  loadSets();
}

function loadSets(){
  let data = getData();
  let container = document.getElementById("savedList");
  container.innerHTML = "";

  data.forEach((set,i)=>{
    let div = document.createElement("div");
    div.className="item";

    div.innerHTML = `
      ${set.name}
      <div>
        <button onclick="loadSet(${i})">Load</button>
        <button onclick="deleteSet(${i})">X</button>
      </div>
    `;

    container.appendChild(div);
  });
}

function loadSet(i){
  let data = getData();
  let set = data[i];

  words = set.words;
  images = set.images;

  document.getElementById("wordsInput").value = words.join("\n");

  alert("Loaded!");
}

function deleteSet(i){
  let data = getData();
  data.splice(i,1);
  setData(data);
  loadSets();
}

loadSets();
</script>

</body>
</html>
