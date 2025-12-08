<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>BYMath — Full Quiz</title>
<style>
body{font-family:'Inter',sans-serif;background:#f7fbff;margin:0;padding:20px;color:#0f172a;}
header{text-align:center;margin-bottom:20px;}
h1{margin:0;font-size:32px;color:#2b7cff;}
h2{margin:10px 0;}
.container{max-width:900px;margin:0 auto;}
.units-grid{display:flex;flex-wrap:wrap;gap:12px;justify-content:center;}
.unit-btn{padding:16px 20px;background:#eef6ff;border:1px dashed #cfe7ff;border-radius:12px;cursor:pointer;font-weight:700;min-width:160px;text-align:center;}
.chapter-btn{padding:12px 16px;background:#f0f7ff;border-radius:8px;margin:6px 0;border:1px solid #cfe7ff;cursor:pointer;width:100%;text-align:left;}
.back-btn{margin-top:12px;background:#d6edff;border:1px solid #b0dcff;}
#quiz-screen{display:none;margin-top:20px;}
#question{font-size:20px;margin-bottom:10px;}
#answer{width:100%;padding:10px;margin-bottom:10px;border-radius:8px;border:1px solid #cfe7ff;font-size:16px;}
#submit-answer{background:#2b7cff;color:white;padding:10px 14px;border:none;border-radius:8px;cursor:pointer;font-weight:700;}
#feedback{margin-top:10px;font-weight:700;}
#score{margin-top:10px;font-weight:700;}
.timer{font-weight:800;color:#2b7cff;margin-top:10px;}
#login-screen{margin-top:50px;text-align:center;}
input, select{padding:10px;margin:6px 0;border-radius:8px;border:1px solid #cfe7ff;font-size:16px;}
button{padding:10px 14px;border:none;border-radius:8px;cursor:pointer;}
</style>
</head>
<body>
<div class="container">
<header>
<h1>BYMath</h1>
<small>Kindergarten Random Quiz</small>
</header>

<!-- LOGIN SCREEN -->
<section id="login-screen">
<h2>Enter Name & Password</h2>
<select id="name-select">
  <option value="">Select your name</option>
  <option>Aiden Cole</option>
  <option>Austin Ward</option>
  <option>Blake Turner</option>
  <option>Carter Flynn</option>
  <option>Caleb Foster</option>
  <option>Chase Bennett</option>
  <option>Dylan Hayes</option>
  <option>Ethan Carter</option>
  <option>Gavin Price</option>
  <option>Hunter Rhodes</option>
  <option>Jace Howard</option>
  <option>Jordan Wells</option>
  <option>Logan Pierce</option>
  <option>Lucas Grant</option>
  <option>Mackenzie Voto</option>
  <option>Mason Ellis</option>
  <option>Marcus Lane</option>
  <option>Owen Parker</option>
  <option>Ryan Mitchell</option>
  <option>Tyler Brooks</option>
</select>
<br>
<input type="password" id="password" placeholder="Enter password">
<br>
<button id="login-btn">Submit</button>
<div id="login-feedback" style="color:red;margin-top:10px;"></div>
</section>

<!-- UNITS -->
<section id="unit-screen" style="display:none;">
<h2>Select a Unit</h2>
<div class="units-grid">
  <div class="unit-btn" data-unit="1">Unit 1: Counting Up & Down</div>
  <div class="unit-btn" data-unit="2">Unit 2: Addition (Intro)</div>
  <div class="unit-btn" data-unit="3">Unit 3: Numbers in the Real World</div>
  <div class="unit-btn" data-unit="4">Unit 4: Subtraction (Intro)</div>
</div>
</section>

<!-- CHAPTERS -->
<section id="chapter-screen" style="display:none;">
<h2 id="unit-title"></h2>
<div id="chapters-list"></div>
<button class="back-btn" id="back-to-units">Back to Units</button>
</section>

<!-- QUIZ -->
<section id="quiz-screen">
<h2 id="chapter-title"></h2>
<div class="timer" id="timer">10:00</div>
<div id="question"></div>
<input type="text" id="answer" placeholder="Type your answer">
<button id="submit-answer">Submit</button>
<div id="feedback"></div>
<div id="score">Score: 0</div>
<button class="back-btn" id="back-to-chapters">Back to Chapters</button>
</section>
</div>

<script>
// --- LOGIN ---
const loginBtn = document.getElementById('login-btn');
const nameSelect = document.getElementById('name-select');
const passwordInput = document.getElementById('password');
const loginScreen = document.getElementById('login-screen');
const loginFeedback = document.getElementById('login-feedback');
const unitScreen = document.getElementById('unit-screen');

loginBtn.addEventListener('click',()=>{
  const name=nameSelect.value;
  const pass=passwordInput.value.trim();
  if(!name){loginFeedback.textContent="Please select a name."; return;}
  if(pass==="71613" || pass==="24689"){
    loginScreen.style.display='none';
    unitScreen.style.display='block';
  }else{
    loginFeedback.textContent="Wrong password!";
  }
});

// --- UNITS & CHAPTERS ---
const unitsData = [
  {name:"Unit 1: Counting Up & Down", chapters:Array.from({length:10},(_,i)=>`Chapter ${i+1}`)},
  {name:"Unit 2: Addition (Intro)", chapters:Array.from({length:10},(_,i)=>`Chapter ${i+1}`)},
  {name:"Unit 3: Numbers in the Real World", chapters:Array.from({length:10},(_,i)=>`Chapter ${i+1}`)},
  {name:"Unit 4: Subtraction (Intro)", chapters:Array.from({length:10},(_,i)=>`Chapter ${i+1}`)}
];

const unitBtns = document.querySelectorAll('.unit-btn');
const chapterScreen = document.getElementById('chapter-screen');
const chaptersList = document.getElementById('chapters-list');
const unitTitle = document.getElementById('unit-title');
const backToUnits = document.getElementById('back-to-units');

const quizScreen = document.getElementById('quiz-screen');
const chapterTitle = document.getElementById('chapter-title');
const questionEl = document.getElementById('question');
const answerEl = document.getElementById('answer');
const submitAnswerBtn = document.getElementById('submit-answer');
const feedbackEl = document.getElementById('feedback');
const scoreEl = document.getElementById('score');
const timerEl = document.getElementById('timer');
const backToChaptersBtn = document.getElementById('back-to-chapters');

let currentUnit,currentChapter,score=0,currentQuestion,timeLeft=600,timerInterval;

// --- Unit selection ---
unitBtns.forEach(btn=>{
  btn.addEventListener('click',()=>{
    const unitIndex = Number(btn.dataset.unit)-1;
    showChapters(unitIndex);
  });
});

function showChapters(unitIndex){
  currentUnit = unitsData[unitIndex];
  unitTitle.textContent = currentUnit.name;
  chaptersList.innerHTML = '';
  currentUnit.chapters.forEach(ch=>{
    const btn=document.createElement('button');
    btn.textContent=ch;
    btn.className='chapter-btn';
    btn.addEventListener('click',()=>startQuiz(ch));
    chaptersList.appendChild(btn);
  });
  unitScreen.style.display='none';
  chapterScreen.style.display='block';
}

backToUnits.addEventListener('click',()=>{
  chapterScreen.style.display='none';
  unitScreen.style.display='block';
});

// --- Quiz ---
function startQuiz(chapter){
  currentChapter = chapter;
  chapterTitle.textContent=`${currentUnit.name} - ${chapter}`;
  chapterScreen.style.display='none';
  quizScreen.style.display='block';
  score=0;
  scoreEl.textContent=`Score: ${score}`;
  timeLeft=600;
  updateTimer();
  if(timerInterval) clearInterval(timerInterval);
  timerInterval=setInterval(()=>{
    timeLeft--;
    updateTimer();
    if(timeLeft<=0){
      clearInterval(timerInterval);
      endQuiz();
    }
  },1000);
  nextQuestion();
}

function updateTimer(){
  const m=Math.floor(timeLeft/60).toString().padStart(2,'0');
  const s=(timeLeft%60).toString().padStart(2,'0');
  timerEl.textContent=`${m}:${s}`;
}

function nextQuestion(){
  let q;
  switch(currentUnit.name){
    case "Unit 1: Counting Up & Down": q=generateCountingQuestion(); break;
    case "Unit 2: Addition (Intro)": q=generateAdditionQuestion(); break;
    case "Unit 3: Numbers in the Real World": q=generateRealWorldQuestion(); break;
    case "Unit 4: Subtraction (Intro)": q=generateSubtractionQuestion(); break;
  }
  currentQuestion=q;
  questionEl.textContent=q.prompt;
  answerEl.value='';
  feedbackEl.textContent='';
  answerEl.focus();
}

// --- Question generators ---
function generateCountingQuestion(){
  const type=Math.random()<0.5?'after':'before';
  let n=Math.floor(Math.random()*21);
  if(type==='after'){ if(n===20)n=19; return {prompt:`What comes after ${n}?`,answer:(n+1).toString()}; }
  else{ if(n===0)n=1; return {prompt:`What comes before ${n}?`,answer:(n-1).toString()}; }
}

function generateAdditionQuestion(){
  const a=Math.floor(Math.random()*6);
  const b=Math.floor(Math.random()*6);
  return {prompt:`${a} + ${b} = ?`,answer:(a+b).toString()};
}

function generateSubtractionQuestion(){
  const a=Math.floor(Math.random()*11);
  const b=Math.floor(Math.random()*(a+1));
  return {prompt:`${a} - ${b} = ?`,answer:(a-b).toString()};
}

function generateRealWorldQuestion(){
  const a=Math.floor(Math.random()*11);
  const b=Math.floor(Math.random()*11);
  const type=Math.random()<0.5?'compare':'count';
  if(type==='compare'){ return {prompt:`Which number is bigger: ${a} or ${b}?`,answer:(a>=b?a:b).toString()}; }
  else{ return {prompt:`If we have ${a} objects and ${b} more, how many in total?`,answer:(a+b).toString()}; }
}

// --- Answer submission ---
submitAnswerBtn.addEventListener('click',submitAnswer);
answerEl.addEventListener('keydown',e=>{if(e.key==='Enter') submitAnswer();});

function submitAnswer(){
  const user=answerEl.value.trim();
  if(user===currentQuestion.answer){
    feedbackEl.textContent='✅ Correct!';
    score++;
  }else{
    feedbackEl.textContent=`❌ Wrong! Correct answer: ${currentQuestion.answer}`;
  }
  scoreEl.textContent=`Score: ${score}`;
  setTimeout(nextQuestion,800);
}

function endQuiz(){
  quizScreen.style.display='none';
  alert(`Time's up! You scored ${score} in ${currentChapter}`);
  chapterScreen.style.display='block';
}

backToChaptersBtn.addEventListener('click',()=>{
  if(timerInterval) clearInterval(timerInterval);
  quizScreen.style.display='none';
  chapterScreen.style.display='block';
});
</script>
</body>
</html>
