
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>MAA LEAGUE</title>

<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:Arial,sans-serif}
body{background:#0e1217;color:#fff}

/* HEADER */
header{
  background:#9B870C;
  padding:15px;
  text-align:center
}
header img{
  width:60px;
  height:60px;
  border-radius:50%;
  object-fit:cover
}
header h1{
  color:blue;
  font-size:18px;
  margin-top:5px
}

/* TABS */
.tabs{
  display:flex;
  justify-content:center;
  gap:8px;
  margin-top:10px
}
.tabs button{
  background:#1c2330;
  color:#aaa;
  border:none;
  padding:8px 14px;
  border-radius:20px;
  display:flex;
  align-items:center;
  gap:5px;
}
.tabs button.active{
  background:#2563eb;
  color:#fff
}

/* CONTENT */
main{padding:10px}
.section{display:none}
.section.active{display:block}

/* MATCH */
.match{
  background:#161c27;
  border-radius:10px;
  padding:12px;
  margin-bottom:8px;
  cursor:pointer;
  display:flex;
  flex-direction:column;
}
.match:active{opacity:.7}
.time{
  text-align:center;
  color:#aaa;
  font-size:13px;
  margin-bottom:6px
}
.teams{
  display:flex;
  justify-content:space-between;
  align-items:center;
}
.team{
  display:flex;
  align-items:center;
  gap:6px;
}
.team img{
  width:25px;
  height:25px;
  border-radius:50%;
  object-fit:cover;
}
.score{
  font-weight:bold;
  color:#60a5fa;
  text-align:center;
  margin-top:6px;
}

/* TABLE */
.table{
  background:#161c27;
  border-radius:10px
}
.row{
  display:grid;
  grid-template-columns:1fr 40px 40px;
  padding:10px;
  border-bottom:1px solid #222;
  align-items:center;
  gap:5px;
}
.row.header{
  background:#1c2330;
  color:#aaa;
  font-weight:bold;
}
.row .team-cell{
  display:flex;
  align-items:center;
  gap:6px;
}
.row img{
  width:25px;
  height:25px;
  border-radius:50%;
  object-fit:cover;
}

/* LINEUP */
.lineup-box{
  background:#161c27;
  padding:15px;
  border-radius:10px;
  margin-bottom:10px;
  text-align:center
}
.lineup-box img{
  width:100%;
  max-width:100%;
  border-radius:10px;
  margin-top:10px;
  object-fit:cover;
}
.back{
  color:#60a5fa;
  margin-bottom:10px;
  cursor:pointer
}
</style>
</head>

<body>

<header>
  <img id="leagueLogo">
  <h1>MAA LEAGUE</h1>

  <div class="tabs">
    <button class="active" onclick="showOnly('matches')">Matches</button>
    <button onclick="showOnly('standings')">Standings</button>
  </div>
</header>

<main>
  <div id="matches" class="section active"></div>
  <div id="standings" class="section"></div>
  <div id="lineupView" class="section"></div>
</main>

<script>
/* ==================================================
   ADMIN AREA (CODE ONLY)
   ================================================== */

/* LEAGUE LOGO */
const LEAGUE_LOGO_JPG = "ma.jpg";

/* CLUB LOGOS (SMALL & ROUND) */
const CLUB_LOGOS_JPG = {
  "HAMA STAR": "rs.jpg",
  "AHMED88": "bv.jpg",
  "MEER": "ns.jpg"
};

/* LINEUP PHOTOS (BIG & FULL IMAGE) */
const LINEUP_PHOTOS_JPG = {
  "HAMA STAR": "hama.jpg",
  "AHMED88": "ahm.jpg",
  "MEER": "meerr.jpg"
};

/* TEAMS */
const teams = ["HAMA STAR","AHMED88","MEER"];

/* MATCHES */
const matches = [
  {home:"HAMA STAR", away:"AHMED88", time:"12:30", scoreHome:3, scoreAway:2},
  {home:"MEER", away:"HAMA STAR", time:"14:00", scoreHome:3, scoreAway:6},
  {home:"AHMED88", away:"MEER", time:"16:15", scoreHome:5, scoreAway:4},

  {home:"AHMED88", away:"MEER", time:"16:15", scoreHome:5, scoreAway:4},
  {home:"AHMED88", away:"MEER", time:"16:15", scoreHome:5, scoreAway:6},

  {home:"HAMA STAR", away:"MEER", time:"18:00", scoreHome:5, scoreAway:4},
  {home:"HAMA STAR", away:"MEER", time:"18:00", scoreHome:2, scoreAway:3},

  {home:"AHMED88", away:"HAMA STAR", time:"20:30", scoreHome:2, scoreAway:2},
  {home:"HAMA STAR", away:"AHMED88", time:"12:30", scoreHome:2, scoreAway:3},
  {home:"HAMA STAR", away:"AHMED88", time:"12:30", scoreHome:5, scoreAway:0},
  
  {home:"HAMA STAR", away:"AHMED88", time:"12:30", scoreHome:2, scoreAway:7},
  
  {home:"AHMED88", away:"MEER", time:"16:15", scoreHome:4, scoreAway:2},
  
  {home:"AHMED88", away:"MEER", time:"16:15", scoreHome:3, scoreAway:1},

  /* NEXT MATCHES (ADDED ONLY) */
  {home:"MEER", away:"HAMA STAR", time:"21:15", scoreHome:"Next Match", scoreAway:""},
  {home:"AHMED88", away:"MEER", time:"22:30", scoreHome:"Next Match", scoreAway:""},
  {home:"HAMA STAR", away:"AHMED88", time:"23:45", scoreHome:"Next Match", scoreAway:""}
];

/* STANDINGS */
const standings = [
   {team:"AHMED88",p:10,pts:19},
   {team:"HAMA STAR",p:8,pts:16},

 

  {team:"MEER",p:8,pts:3}
];

/* LOAD LEAGUE LOGO */
document.getElementById("leagueLogo").src = LEAGUE_LOGO_JPG;

/* LOAD MATCHES */
const matchesDiv = document.getElementById("matches");
matches.forEach(m=>{
  matchesDiv.innerHTML += `
    <div class="match" onclick="openLineup('${m.home}','${m.away}')">
      <div class="time">${m.time}</div>
      <div class="teams">
        <div class="team">
          <img src="${CLUB_LOGOS_JPG[m.home]}">
          <span>${m.home}</span>
        </div>
        <div class="team">
          <img src="${CLUB_LOGOS_JPG[m.away]}">
          <span>${m.away}</span>
        </div>
      </div>
      <div class="score">${m.scoreHome} - ${m.scoreAway}</div>
    </div>
  `;
});

/* LOAD STANDINGS */
document.getElementById("standings").innerHTML = `
<div class="table">
  <div class="row header">
    <span>Team</span><span>P</span><span>Pts</span>
  </div>
  ${standings.map(s=>`
    <div class="row">
      <span class="team-cell">
        <img src="${CLUB_LOGOS_JPG[s.team]}">
        ${s.team}
      </span>
      <span>${s.p}</span>
      <span>${s.pts}</span>
    </div>
  `).join("")}
</div>
`;

/* OPEN LINEUPS */
function openLineup(home,away){
  showOnly("lineupView");
  lineupView.innerHTML = `
    <div class="back" onclick="showOnly('matches')">← Back</div>
    ${lineupHTML(home)}
    ${lineupHTML(away)}
  `;
}

/* LINEUP HTML */
function lineupHTML(team){
  return `
  <div class="lineup-box">
    <h3>${team}</h3>
    <img src="${LINEUP_PHOTOS_JPG[team]}">
  </div>
  `;
}

/* SHOW SECTION */
function showOnly(id){
  document.querySelectorAll(".section").forEach(s=>s.classList.remove("active"));
  document.getElementById(id).classList.add("active");
}
</script>

</body>
</html>
