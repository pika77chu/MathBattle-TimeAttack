# MathBattle-TimeAttack
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Math Battle - Time Attack</title>
</head>
<body>
<canvas id="canvas"></canvas>
<script>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
let mode = 0;
let difficult = 1;
let shiki = "";
let result = 0;
let difficultselected = 0;
let time2 = 3;
let time = 0;
let combo = 0;
let damageText = "";
let score = 0;
let choiceXY = [{x: canvas.width*0.05,y: canvas.height*0.6,w: canvas.width*0.4,h: canvas.height*0.08},
{x: canvas.width*0.55,y: canvas.height*0.6,w: canvas.width*0.4,h: canvas.height*0.08},
{x: canvas.width*0.05,y: canvas.height*0.75,w: canvas.width*0.4,h: canvas.height*0.08},
{x: canvas.width*0.55,y: canvas.height*0.75,w: canvas.width*0.4,h: canvas.height*0.08}];
let timerID;
let timerID2;
let timerID3;
let timerID4;
let timerID5;
let pressTimer;
let time4 = 10;
let choices = [];
let selectedchoice = 0;
let enemies = [{name: "Monster",emoji: "👾",hp: 200,realhp: 200,maxhp: 200},
{name: "Snake",emoji: "🐍",hp: 200,realhp: 200,maxhp: 200},
{name: "Dragon",emoji: "🐉",hp: 200,realhp: 200,maxhp: 200}];
let problem = 0;
let clear = 0;
let unclear = 0;
let choice2XY = null;
let choice3XY = null;
let damageTimer = 30;
let hiscore = JSON.parse(localStorage.getItem("mathhiscore")) || 0;

window.addEventListener("resize",(e) => {
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
choiceXY = [{x: canvas.width*0.05,y: canvas.height*0.6,w: canvas.width*0.4,h: canvas.height*0.08},
{x: canvas.width*0.55,y: canvas.height*0.6,w: canvas.width*0.4,h: canvas.height*0.08},
{x: canvas.width*0.05,y: canvas.height*0.75,w: canvas.width*0.4,h: canvas.height*0.08},
{x: canvas.width*0.55,y: canvas.height*0.75,w: canvas.width*0.4,h: canvas.height*0.08}];
});

function makeAnswer() {
let kigous = ["×","÷","+","-"];
let num = difficult*2;
let answer = [];
let usednum = 0;
while (usednum < num) {
if (usednum === 0) {
let a = Math.floor(Math.random()*100)+1;
answer.push(a);
usednum++;
}
let kigou = kigous[Math.floor(Math.random()*4)];
if (kigou === "÷" && answer[answer.length-2] === "÷") kigou = "×";
answer.push(kigou);
let b = Math.floor(Math.random()*100)+1;
if (kigou === "÷") {
let a = b*(Math.floor(Math.random()*10)+1);
answer[answer.length-2] = a;
}
if (kigou === "×" && answer[answer.length-3] !== "÷") {
let a = Math.floor(Math.random()*20)+1;
answer[answer.length-2] = a;
b = Math.floor(Math.random()*20)+1;
}
answer.push(b);
usednum++;
}
if (difficult === 3) {
let r = 2*Math.floor(Math.random()*(difficult*2-1));
let answer2 = [...answer];
if (!isNaN(answer2[r])) answer2[r] = "("+answer2[r];
r += 2;
if (!isNaN(answer2[r])) answer2[r] = answer2[r]+")";
let answer3 = answer2.join("").replace(/×/g,"*");
answer3 = answer3.replace(/÷/g,"/");
let answer4 = answer.join("").replace(/×/g,"*");
answer4 = answer4.replace(/÷/g,"/");
if (Number.isInteger(eval(answer3)) && eval(answer3) !== eval(answer4)) answer = [...answer2];
}
return answer.join("");
}

function getResult(answer) {
answer = answer.replace(/×/g,"*");
answer = answer.replace(/÷/g,"/");
return eval(answer);
}

function mode0Action() {
ctx.fillStyle = "blue";
ctx.font = `${canvas.height*0.12}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillText("Math Battle - Time Attack",canvas.width/2,canvas.height*0.1);
for (let i = 0; i < 3; i++) {
let x = canvas.width*0.1;
let w = canvas.width*0.8;
let h = canvas.height*0.08;
let spaceY = canvas.height*0.2;
let y = spaceY+canvas.height*0.15*i;
if (difficultselected === i) ctx.strokeStyle = "red";
else ctx.strokeStyle = "blue";
ctx.lineWidth = 3;
ctx.strokeRect(x,y,w,h);
ctx.font = `${canvas.height*0.04}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillStyle = "black";
ctx.fillText(i === 0? "Easy" : i === 1? "Normal" : "Hard",x+w/2,y+h/2);
}
ctx.fillText(`Hold for 1 second to quit!`,canvas.width/2,canvas.height*0.92);
ctx.font = `${canvas.height*0.06}px sans-serif`;
ctx.fillText(hiscore === 0? "" : `HISCORE: ${hiscore}`,canvas.width/2,canvas.height*0.8);
}
function draw() {
if (mode !== 2) ctx.clearRect(0,0,canvas.width,canvas.height);
if (mode === 0) {
mode0Action();
}else if (mode === 1) {
mode0Action();
ctx.fillStyle = "rgba(255,255,255,0.6)";
ctx.fillRect(0,0,canvas.width,canvas.height);
let w = canvas.width/4*3;
let h = canvas.height/2;
let centerX = canvas.width/2;
let centerY = canvas.height/2;
let x = centerX-w/2;
let y = centerY-h/2;
ctx.strokeStyle = "blue";
ctx.lineWidth = 0.1;
ctx.beginPath();
ctx.moveTo(x+w-h/2,y+h/2);
ctx.arc(x+w-h/2,y+h/2,h/2,-Math.PI/2,Math.PI/2);
ctx.moveTo(x+h/2,y+h/2);
ctx.arc(x+h/2,y+h/2,h/2,Math.PI/2,Math.PI/2*3);
ctx.fillStyle = "blue";
ctx.fill();
ctx.stroke();
ctx.fillRect(x+h/2,y,w-h,h);
ctx.font = `${h/4*3}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillStyle = "white";
ctx.fillText("START",centerX,centerY);
}else if (mode === 3) {
ctx.font = `${canvas.height/2}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillStyle = "red";
ctx.fillText(time2 <= 0? "START!!" : time2,canvas.width/2,canvas.height/2);
}else if (mode === 4) {
ctx.fillStyle = "black";
ctx.fillRect(0,0,canvas.width,canvas.height);
}else if (mode === 5) {
ctx.font = `${canvas.height*0.05}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillStyle = "black";
ctx.fillText(problem > 0? `${clear}/${problem} Correct!` : "",canvas.width/8*7,canvas.height*0.05);
ctx.fillText(`Time: ${time4 < 10? "0"+time4 : time4}`,canvas.width*0.875,canvas.height*0.15);
ctx.fillText(enemies[difficult-1].name,canvas.width/2,canvas.height*0.05);
let x = canvas.width/4;
let y = canvas.height*0.13;
let w = canvas.width/2;
let h = canvas.height*0.03;
ctx.fillStyle = "gray";
ctx.fillRect(x,y,w,h);
let hpa = enemies[difficult-1].hp/enemies[difficult-1].maxhp;
if (hpa <= 0.2) ctx.fillStyle = "red";
else if (hpa <= 0.5) ctx.fillStyle = "blue";
else ctx.fillStyle = "green";
ctx.fillRect(x,y,w*hpa,h);
ctx.strokeStyle = "black";
ctx.lineWidth = 0.1;
ctx.strokeRect(x,y,w,h);
ctx.font = `${canvas.height*0.3}px sans-serif`;
ctx.fillText(enemies[difficult-1].emoji,canvas.width/2,canvas.height*0.33);
ctx.font = `${canvas.height*0.08}px sans-serif`;
ctx.fillStyle = "black";
ctx.fillText(`Q${problem+1}: ${shiki}`,canvas.width/2,canvas.height*0.57);
for (let i = 0; i < 2; i++) {
for (let j = 0; j < 2; j++) {
let number = i*2+j;
if (selectedchoice === number) ctx.strokeStyle = "red";
else ctx.strokeStyle = "blue";
ctx.lineWidth = 3;
let cw = canvas.width*0.4;
let cx = canvas.width*0.05+(canvas.width*0.5)*j;
let ch = canvas.height*0.08;
let cy = canvas.height*0.6+(canvas.height*0.15)*i;
ctx.strokeRect(cx,cy,cw,ch);
ctx.font = `${canvas.height*0.05}px sans-serif`;
ctx.fillText(choices[number],cx+cw/2,cy+ch/2);
}
}
ctx.font = `${canvas.height/4}px sans-serif`;
ctx.fillStyle = "rgba(255,0,0,0.6)";
ctx.fillText(time4 <= 0? "Time Over!!" : time4 <= 3? time4 : "",canvas.width/2,canvas.height/2);
}else if (mode === 6) {
ctx.font = `${canvas.height*0.05}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillStyle = "black";
ctx.fillText(problem > 0? `${clear}/${problem} Correct!` : "",canvas.width/8*7,canvas.height*0.05);
ctx.fillText(enemies[difficult-1].name,canvas.width/2,canvas.height*0.05);
let x = canvas.width/4;
let y = canvas.height*0.13;
let w = canvas.width/2;
let h = canvas.height*0.03;
ctx.fillStyle = "gray";
ctx.fillRect(x,y,w,h);
let hpa = enemies[difficult-1].hp/enemies[difficult-1].maxhp;
if (hpa <= 0.2) ctx.fillStyle = "red";
else if (hpa <= 0.5) ctx.fillStyle = "blue";
else ctx.fillStyle = "green";
ctx.fillRect(x,y,w*hpa,h);
ctx.strokeStyle = "black";
ctx.lineWidth = 0.1;
ctx.strokeRect(x,y,w,h);
ctx.font = `${canvas.height*0.3}px sans-serif`;
ctx.fillText(enemies[difficult-1].emoji,canvas.width/2,canvas.height*0.33);
ctx.font = `${canvas.height*0.08}px sans-serif`;
ctx.fillStyle = "black";
ctx.fillText(`Q${problem}: ${shiki}`,canvas.width/2,canvas.height*0.57);
for (let i = 0; i < 2; i++) {
for (let j = 0; j < 2; j++) {
let number = i*2+j;
if (selectedchoice === number) continue;
ctx.strokeStyle = "blue";
ctx.lineWidth = 3;
let cw = canvas.width*0.4;
let cx = canvas.width*0.05+(canvas.width*0.5)*j;
let ch = canvas.height*0.08;
let cy = canvas.height*0.6+(canvas.height*0.15)*i;
ctx.strokeRect(cx,cy,cw,ch);
ctx.strokeRect(cx,cy,cw,ch);
ctx.font = `${canvas.height*0.05}px sans-serif`;
ctx.fillText(choices[number],cx+cw/2,cy+ch/2);
}
}
let chx = choice3XY.x;
let chy = choice3XY.y;
let chw = choice3XY.w;
let chh = choice3XY.h;
ctx.lineWidth = 1;
ctx.strokeStyle = "red";
ctx.strokeRect(chx,chy,chw,chh);
}else if (mode === 7) {
ctx.font = `${canvas.height*0.05}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillStyle = "black";
ctx.fillText(problem > 0? `${clear}/${problem} CorrectS` : "",canvas.width/8*7,canvas.height*0.05);
ctx.fillText(enemies[difficult-1].name,canvas.width/2,canvas.height*0.05);
let x = canvas.width/4;
let y = canvas.height*0.13;
let w = canvas.width/2;
let h = canvas.height*0.03;
ctx.fillStyle = "gray";
ctx.fillRect(x,y,w,h);
let hpa = enemies[difficult-1].hp/enemies[difficult-1].maxhp;
if (hpa <= 0.2) ctx.fillStyle = "red";
else if (hpa <= 0.5) ctx.fillStyle = "blue";
else ctx.fillStyle = "green";
ctx.fillRect(x,y,w*hpa,h);
ctx.strokeStyle = "black";
ctx.lineWidth = 0.1;
ctx.strokeRect(x,y,w,h);
ctx.font = `${canvas.height*0.3}px sans-serif`;
ctx.fillText(enemies[difficult-1].emoji,canvas.width/2,canvas.height*0.33);
ctx.font = `${canvas.height*0.08}px sans-serif`;
ctx.fillStyle = "black";
ctx.fillText(`Q${problem}: ${shiki}`,canvas.width/2,canvas.height*0.57);
for (let i = 0; i < 2; i++) {
for (let j = 0; j < 2; j++) {
let number = i*2+j;
if (selectedchoice === number && choices[selectedchoice] === result) ctx.strokeStyle = "green";
else if (selectedchoice === number) ctx.strokeStyle = "red";
else if (choices[number] === result) ctx.strokeStyle = "green";
else ctx.strokeStyle = "blue";
ctx.lineWidth = 3;
let cw = canvas.width*0.4;
let cx = canvas.width*0.05+(canvas.width*0.5)*j;
let ch = canvas.height*0.08;
let cy = canvas.height*0.6+(canvas.height*0.15)*i;
ctx.strokeRect(cx,cy,cw,ch);
ctx.font = `${canvas.height*0.05}px sans-serif`;
ctx.fillText(choices[number],cx+cw/2,cy+ch/2);
}
}
if (damageTimer > 0) {
ctx.font = `${canvas.height*0.1}px sans-serif`;
ctx.fillStyle = damageText === "MISS" ? "blue" : "red";
ctx.fillText(damageText, canvas.width/2, canvas.height*0.2);
damageTimer--;
if (combo > 1) {
ctx.font = `${canvas.height*0.05}px sans-serif`;
ctx.fillStyle = "orange";
ctx.fillText(`${combo} COMBO`, canvas.width/2, canvas.height*0.1);
}
}
ctx.font = `${canvas.height/4*3}px sans-serif`;
ctx.fillStyle = "rgba(255,0,0,1)";
ctx.globalAlpha = 0.6;
ctx.fillText(choices[selectedchoice] === result? "⭕️" : "❌️",canvas.width/2,canvas.height/2);
ctx.globalAlpha = 1.0;
}else if (mode === 8) {
ctx.font = `${canvas.height*0.1}px sans-serif`;
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillStyle = "black";
ctx.fillText(`VICTORY!!`,canvas.width/2,canvas.height*0.4);
ctx.fillText(`Correct Answers: ${clear}/${problem}`,canvas.width/2,canvas.height*0.6);
ctx.fillText(`SCORE: ${score}`,canvas.width/2,canvas.height*0.75);
}
requestAnimationFrame(draw);
}

window.onload = () => {draw();}

document.addEventListener("keydown",(e) => {
const key = e.key;
if (["ArrowLeft","ArrowRight","ArrowDown","ArrowUp"].includes(e.key)) {
e.preventDefault();
}
if (mode === 0 && key === "ArrowDown") {
if (difficultselected+1 < 3) difficultselected++;
}else if (mode === 0 && key === "ArrowUp") {
if (difficultselected-1 >= 0) difficultselected--;
}else if (mode === 0 && key === " ") {
mode = 1;
difficult = difficultselected+1;
}else if (mode === 1 && key === "Escape") {
mode = 0;
}else if (mode === 1 && key === " ") {
time = 0;
mode = 2;
timerID = setInterval(() => {
ctx.fillStyle = "rgba(255,255,255,0.6)";
ctx.fillRect(0,0,canvas.width,canvas.height);
time += 1;
if (time > 10) {
mode = 3;
clear = 0;
problem = 0;
unclear = 0;
score = 0;
combo = 0;
timer2();
clearInterval(timerID);
}
},100);
}else if (mode >= 3 && mode <= 7 && mode !== 4 && key === "Escape") {
mode = 4;
clearInterval(timerID2);
clearInterval(timerID3);
clearInterval(timerID4);
clearInterval(timerID5);
enemies[difficult-1].hp = enemies[difficult-1].maxhp;
enemies[difficult-1].realhp = enemies[difficult-1].maxhp;
setTimeout(() => {mode = 0;},1000);
}else if (mode === 5 && key === "ArrowRight") {
let x = selectedchoice%2;
let y = Math.floor(selectedchoice/2);
x = 1;
selectedchoice = y*2+x;
}else if (mode === 5 && key === "ArrowLeft") {
let x = selectedchoice%2;
let y = Math.floor(selectedchoice/2);
x = 0;
selectedchoice = y*2+x;
}else if (mode === 5 && key === "ArrowDown") {
let x = selectedchoice%2;
let y = Math.floor(selectedchoice/2);
y = 1;
selectedchoice = y*2+x;
}else if (mode === 5 && key === "ArrowUp") {
let x = selectedchoice%2;
let y = Math.floor(selectedchoice/2);
y = 0;
selectedchoice = y*2+x;
}else if (mode === 5 && key === " ") {
clearInterval(timerID4);
mode = 6;
problem++;
damageTimer = 30;
if (choices[selectedchoice] === result) {
clear++;
combo++;
let damage = Math.floor(10*(time4**0.25)) + combo*2;
damageText = `-${damage}`;
let gain = 0;
gain += 100;
gain += Math.max(time4,1)*10;
gain += combo*20;
score += Math.floor(gain*(1+combo*0.1));
}else {
damageText = "MISS";
combo = 0;
unclear++;
score -= 50+combo*10;
score = Math.max(0,score);
}
choice2XY = JSON.parse(JSON.stringify(choiceXY[selectedchoice]));
choice3XY = JSON.parse(JSON.stringify(choiceXY[selectedchoice]));
timer5();
}else if (mode === 7 && key === " ") {
if (enemies[difficult-1].realhp > 0) {
clearInterval(timerID3);
mode = 5;
enemies[difficult-1].hp = enemies[difficult-1].realhp;
shiki = makeAnswer();
result = getResult(shiki);
let r = Math.floor(Math.random()*4);
let first = result-r;
choices = new Array(4);
for (let i = 0; i < 4; i++) {
choices[i] = first+i;
}
selectedchoice = 0;
timer4();
}else{
enemies[difficult-1].hp = enemies[difficult-1].maxhp;
enemies[difficult-1].realhp = enemies[difficult-1].maxhp;
mode = 8;
let clearBonus = clear * 50;
let accuracy = clear / problem;
let accuracyBonus = Math.floor(accuracy * 500);
score += clearBonus + accuracyBonus;
score *= difficult;
score = Math.max(10,score);
hiscore = Math.max(hiscore,score);
localStorage.setItem("mathhiscore", hiscore);
}
}else if (mode === 8 && key === " ") {
mode = 0;
}
});

canvas.addEventListener("click",(e) => {
const rect = canvas.getBoundingClientRect();
let tx = e.clientX - rect.left;
let ty = e.clientY - rect.top;
if (mode === 0) {
for (let i = 0; i < 3; i++) {
let x = canvas.width*0.1;
let w = canvas.width*0.8;
let h = canvas.height*0.08;
let spaceY = canvas.height*0.2;
let y = spaceY+canvas.height*0.15*i;
if (tx >= x && tx <= x+w && ty >= y && ty <= y+h) {
mode = 1;
difficult = i+1;
difficultselected = i;
}
}
}else if (mode === 1) {
let w = canvas.width/4*3;
let h = canvas.height/2;
let centerX = canvas.width/2;
let centerY = canvas.height/2;
let x = centerX-w/2;
let y = centerY-h/2;
if (tx >= x && tx <= x+w && ty >= y && ty <= y+h) {
time = 0;
mode = 2;
timerID = setInterval(() => {
ctx.fillStyle = "rgba(255,255,255,0.6)";
ctx.fillRect(0,0,canvas.width,canvas.height);
time += 1;
if (time > 10) {
mode = 3;
clear = 0;
problem = 0;
unclear = 0;
combo = 0;
score = 0;
timer2();
clearInterval(timerID);
}
},100);
}else mode = 0;
}else if (mode === 5) {
for (let i = 0; i < 2; i++) {
for (let j = 0; j < 2; j++) {
let w = canvas.width*0.4;
let x = canvas.width*0.05+(canvas.width*0.5)*j;
let h = canvas.height*0.08;
let y = canvas.height*0.6+(canvas.height*0.15)*i;
if (tx >= x && tx <= x+w && ty >= y && ty <= y+h) {
clearInterval(timerID4);
mode = 6;
selectedchoice = i*2+j;
problem++;
damageTimer = 30;
if (choices[selectedchoice] === result) {
clear++;
combo++;
let damage = Math.floor(10*(time4**0.25)) + combo*2;
damageText = `-${damage}`;
let gain = 0;
gain += 100;
gain += Math.floor(time4,1)*10;
gain += combo*20;
score += Math.floor(gain*(1+combo*0.1));
}else {
combo = 0;
damageText = "MISS";
unclear++;
score -= 50+combo*10;
score = Math.max(0,score);
}
choice2XY = JSON.parse(JSON.stringify(choiceXY[selectedchoice]));
choice3XY = JSON.parse(JSON.stringify(choiceXY[selectedchoice]));
timer5();
}
}
}
}else if (mode === 7) {
if (enemies[difficult-1].realhp > 0) {
clearInterval(timerID3);
mode = 5;
enemies[difficult-1].hp = enemies[difficult-1].realhp;
shiki = makeAnswer();
result = getResult(shiki);
let r = Math.floor(Math.random()*4);
let space = Math.floor(Math.random()*4)+1;
let first = result-r*space;
choices = new Array(4);
for (let i = 0; i < 4; i++) {
choices[i] = first+i*space;
}
selectedchoice = 0;
timer4();
}else{
enemies[difficult-1].hp = enemies[difficult-1].maxhp;
enemies[difficult-1].realhp = enemies[difficult-1].maxhp;
mode = 8;
let clearBonus = clear * 50;
let accuracy = clear / problem;
let accuracyBonus = Math.floor(accuracy * 500);
score += clearBonus + accuracyBonus;
score *= difficult;
score = Math.max(10,score);
hiscore = Math.max(hiscore,score);
localStorage.setItem("mathhiscore", hiscore);
}
}else if (mode === 8) {
mode = 0;
}
});

canvas.addEventListener("touchstart",startPress);
canvas.addEventListener("mousedown",startPress);
canvas.addEventListener("touchend",endPress);
canvas.addEventListener("mouseup",endPress);

function startPress() {
pressTimer = setTimeout(() => {
if (mode >= 3 && mode <= 7 && mode !== 4) {
mode = 4;
clearInterval(timerID2);
clearInterval(timerID3);
clearInterval(timerID4);
clearInterval(timerID5);
enemies[difficult-1].hp = enemies[difficult-1].maxhp;
enemies[difficult-1].realhp = enemies[difficult-1].maxhp;
setTimeout(() => {mode = 0;},1000);
}
},1000);
}

function endPress() {
if (mode >= 3 && mode <= 7 && mode !== 4) clearTimeout(pressTimer);
}

function timer3() {
let damage = 0;
time4 = Math.max(1,time4);
if (choices[selectedchoice] === result) damage = Math.floor(10*(Math.max(1,time4)**0.25))+combo*2;
else damage = -15;
enemies[difficult-1].realhp = Math.min(enemies[difficult-1].maxhp,Math.max(0,enemies[difficult-1].realhp-damage));
timerID3 = setInterval(() => {
enemies[difficult-1].hp -= (enemies[difficult-1].hp-enemies[difficult-1].realhp)*0.1;
if (Math.abs(enemies[difficult-1].hp-enemies[difficult-1].realhp) < 0.01) {
enemies[difficult-1].hp = enemies[difficult-1].realhp;
clearInterval(timerID3);
}
},50);
}

function timer2() {
time2 = 3;
timerID2 = setInterval(() => {
time2--;
if (time2 < 0) {
mode = 5;
shiki = makeAnswer();
result = getResult(shiki);
let r = Math.floor(Math.random()*4);
let space = Math.floor(Math.random()*4)+1;
let first = result-r*space;
choices = new Array(4);
for (let i = 0; i < 4; i++) {
choices[i] = first+i*space;
}
selectedchoice = 0;
timer4();
clearInterval(timerID2);
}
},1000);
}

function timer5() {
timerID5 = setInterval(() => {
choice3XY.y -= 20;
if (choice3XY.y <= canvas.height*0.48) {
mode = 7;
choice2XY = null;
choice3XY = null;
timer3();
clearInterval(timerID5);
}
let orX = choice2XY.x;
let orY = choice2XY.y;
let orW = choice2XY.w;
let orH = choice2XY.h;
let nowY = choice3XY.y;
let goalX = canvas.width/2;
let goalY = canvas.height*0.48;
let a = (nowY-goalY)*(goalX-orX)/(orY-goalY);
let nowX = goalX-a;
let nowW = orW*(nowY-goalY)/(orY-goalY);
let nowH = orH*nowW/orW;
choice3XY = {x: nowX,y: nowY,w: nowW,h: nowH};
},50);
}

function timer4() {
time4 = difficult*10;
timerID4 = setInterval(() => {
time4--;
if (time4 < 0) {
mode = 6;
problem++;
damageTimer = 30;
if (choices[selectedchoice] === result) {
clear++;
combo++;
let damage = Math.floor(10*(time4**0.25)) + combo*2;
damageText = `-${damage}`;
let gain = 0;
gain += 100;
gain += combo*20;
gain += Math.max(1,time4)*10;
score += Math.floor(gain*(1+combo*0.1));
}else {
unclear++;
combo = 0;
damageText = "MISS";
score -= 50+combo*10;
}
choice2XY = JSON.parse(JSON.stringify(choiceXY[selectedchoice]));
choice3XY = JSON.parse(JSON.stringify(choiceXY[selectedchoice]));
timer5();
clearInterval(timerID4);
}
},1000);
}
</script>
</body>
</html>

