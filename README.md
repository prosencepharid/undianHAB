<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Undian HAB Kemenag</title>

<style>
body {
  font-family: Arial, sans-serif;
  background: #0b3c5d;
  color: white;
  text-align: center;
  padding-top: 50px;
}

h1 { font-size: 48px; }
#countdown { font-size: 40px; margin: 20px; }
#rolling { font-size: 36px; margin: 30px; height: 50px; }
#winner {
  font-size: 48px;
  color: #ffd700;
  margin-top: 30px;
}

button {
  font-size: 22px;
  padding: 15px 30px;
  margin: 10px;
  cursor: pointer;
}

#history {
  margin-top: 40px;
  max-height: 200px;
  overflow-y: auto;
  text-align: left;
  padding: 0 25%;
}
</style>
</head>

<body>

<h1>🎉 UNDIAN HAB KEMENAG 🎉</h1>

<div id="countdown">Siap Undi</div>
<div id="rolling">—</div>
<div id="winner"></div>

<button onclick="startLottery()">🎰 UNDI</button>
<button onclick="goFullscreen()">🖥️ FULLSCREEN</button>

<div id="history">
  <h3>🏆 Daftar Pemenang</h3>
  <ol id="winnerList"></ol>
</div>

<script>
const SHEET_URL = "https://docs.google.com/spreadsheets/d/1tOFJI3BRPlTCsuq6GWk7aZ-ygrscOXl9/export?format=csv";

let participants = [];
let winners = JSON.parse(localStorage.getItem("winners")) || [];

fetch(SHEET_URL)
  .then(res => res.text())
  .then(text => {
    const rows = text.split("\n").slice(1);
    participants = rows.map(r => {
      const c = r.split(",");
      return { nomor: c[2], nama: c[1] };
    }).filter(p => p.nama);
    participants = participants.filter(p =>
      !winners.find(w => w.nomor === p.nomor)
    );
    renderWinners();
  });

function startLottery() {
  if (participants.length === 0) {
    alert("Peserta habis!");
    return;
  }

  let time = 10;
  document.getElementById("winner").innerText = "";

  const countdown = setInterval(() => {
    document.getElementById("countdown").innerText = `⏳ ${time}`;
    time--;
    if (time < 0) {
      clearInterval(countdown);
      pickWinner();
    }
  }, 1000);

  const rolling = setInterval(() => {
    const r = participants[Math.floor(Math.random() * participants.length)];
    document.getElementById("rolling").innerText =
      `${r.nomor} - ${r.nama}`;
  }, 100);

  setTimeout(() => clearInterval(rolling), 10000);
}

function pickWinner() {
  const i = Math.floor(Math.random() * participants.length);
  const winner = participants[i];

  document.getElementById("winner").innerText =
    `🏆 ${winner.nomor} - ${winner.nama}`;

  winners.push(winner);
  localStorage.setItem("winners", JSON.stringify(winners));

  participants.splice(i, 1);
  renderWinners();
}

function renderWinners() {
  const list = document.getElementById("winnerList");
  list.innerHTML = "";
  winners.forEach(w => {
    const li = document.createElement("li");
    li.innerText = `${w.nomor} - ${w.nama}`;
    list.appendChild(li);
  });
}

function goFullscreen() {
  document.documentElement.requestFullscreen();
}
</script>

</body>
</html>
