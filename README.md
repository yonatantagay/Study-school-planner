# Study-school-planner
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Study Planner – Masterpiece v2</title>
<style>
:root{
 --bg:#f1f5f9;--card:#fff;--text:#020617;
 --accent:#2563eb;--task:#e0e7ff;--done:#dcfce7;
}
.dark{
 --bg:#020617;--card:#0f172a;--text:#e5e7eb;
 --accent:#38bdf8;--task:#1e293b;--done:#14532d;
}
body{margin:0;font-family:Arial,sans-serif;background:var(--bg);color:var(--text);padding:15px;transition:.3s;}
h1{text-align:center;}
.container{max-width:900px;margin:auto;}
.card{background:var(--card);padding:20px;margin-bottom:15px;border-radius:16px;box-shadow:0 10px 30px rgba(0,0,0,.15);}
button{background:var(--accent);color:#fff;border:none;padding:10px 14px;border-radius:10px;cursor:pointer;}
input{width:100%;padding:10px;margin:5px 0;border-radius:10px;border:none;}
li{background:var(--task);padding:10px;border-radius:10px;margin-bottom:8px;display:flex;gap:10px;align-items:center;}
li.done{background:var(--done);text-decoration:line-through;}
.progress{height:8px;background:#334155;border-radius:10px;}
.progress-bar{height:100%;background:var(--accent);}
.timer{text-align:center;font-size:18px;font-weight:bold;padding:10px;border-radius:12px;}
.warn{background:#f59e0b;color:#000;}
.danger{background:#dc2626;color:#fff;animation:pulse 1s infinite;}
@keyframes pulse{0%{transform:scale(1);}50%{transform:scale(1.05);}100%{transform:scale(1);}}
.flex{display:flex;gap:10px;flex-wrap:wrap;}
</style>
</head>
<body>

<h1>🔥 Study planner</h1>

<div class="container">

<div class="card flex">
  <button onclick="toggleDark()">🌙 Dark</button>
  <div id="countdown" class="timer">⏳ Set exam date</div>
</div>

<div class="card">
  <input type="date" id="examDate">
  <button onclick="setExam()">Set Exam Date</button>
</div>

<div class="card">
  <h3>⏳ Pomodoro Focus</h3>
  <div class="timer" id="pomo">25:00</div>
  <div class="flex">
    <button onclick="startPomo()">▶ Start</button>
    <button onclick="resetPomo()">🔁 Reset</button>
  </div>
</div>

<div class="card">
  <h3>📊 Score Predictor</h3>
  <input id="current" placeholder="Current average">
  <input id="finalWeight" placeholder="Final exam %">
  <input id="target" placeholder="Target score">
  <button onclick="predict()">Predict</button>
  <p id="result"></p>
</div>

<div class="card">
  <input id="dayName" placeholder="Day name">
  <input id="t1" placeholder="Task 1">
  <input id="t2" placeholder="Task 2">
  <input id="t3" placeholder="Task 3">
  <button onclick="addDay()">➕ Add Day</button>
</div>

<div id="planner"></div>
</div>

<script>
let days=JSON.parse(localStorage.getItem("days"))||[
 {name:"Day 1: Maths",tasks:["Functions","Logs & signum","Past paper"]},
 {name:"Day 2: Agriculture & English",tasks:["Agri notes","Grammar","Practice"]},
 {name:"Day 3: Physics, Chemistry, ICT",tasks:["Physics","Chemistry","ICT/Web"]}
];

function save(){localStorage.setItem("days",JSON.stringify(days));}

function render(){
 planner.innerHTML="";
 days.forEach((d,i)=>{
  let done=0,list="";
  d.tasks.forEach((t,j)=>{
   if(typeof t==="string") d.tasks[j]={text:t,done:false};
   if(d.tasks[j].done) done++;
   list+=`<li class="${d.tasks[j].done?'done':''}" onclick="toggle(${i},${j})">
   <input type="checkbox" ${d.tasks[j].done?'checked':''}> ${d.tasks[j].text}</li>`;
  });
  let per=d.tasks.length?(done/d.tasks.length)*100:0;
  planner.innerHTML+=`
  <div class="card">
   <h3>${d.name}</h3>
   <ul>${list}</ul>
   <div class="progress"><div class="progress-bar" style="width:${per}%"></div></div>
   <button onclick="removeDone(${i})">🧹 Remove completed</button>
  </div>`;
 });
 save();
}

function toggle(i,j){days[i].tasks[j].done=!days[i].tasks[j].done;render();}
function removeDone(i){days[i].tasks=days[i].tasks.filter(t=>!t.done);render();}
function addDay(){days.push({name:dayName.value,tasks:[t1.value,t2.value,t3.value]});render();}
function toggleDark(){document.body.classList.toggle("dark");}

/* Countdown with urgency colors */
function setExam(){localStorage.setItem("exam",examDate.value);}
function updateCountdown(){
 const d=localStorage.getItem("exam"); if(!d)return;
 const now=new Date(),exam=new Date(d);
 let diff=exam-now;
 const box=countdown;
 box.className="timer";
 if(diff<=0){box.innerText="🔥 EXAM TIME";return;}
 const days=Math.floor(diff/86400000);
 diff%=86400000;
 const hrs=Math.floor(diff/3600000);
 diff%=3600000;
 const mins=Math.floor(diff/60000);
 box.innerText=`⏳ ${days}d ${hrs}h ${mins}m remaining`;
 if(days<2) box.classList.add("warn");
 if(days<1) box.classList.add("danger");
}
setInterval(updateCountdown,1000);

/* Pomodoro */
let time=1500,intv;
function startPomo(){clearInterval(intv);intv=setInterval(()=>{if(time<=0){clearInterval(intv);alert("Break time 💥");return;}time--;updatePomo();},1000);}
function resetPomo(){clearInterval(intv);time=1500;updatePomo();}
function updatePomo(){let m=Math.floor(time/60),s=time%60;pomo.innerText=`${m}:${s<10?"0":""}${s}`;}
updatePomo();

/* Score Predictor */
function predict(){
 let c=+current.value,f=+finalWeight.value,t=+target.value;
 let need=(t-(c*(100-f)/100))/(f/100);
 result.innerText=`You need ≈ ${need.toFixed(1)}% in final`;
}

render();updateCountdown();
</script>

</body>
</html>
