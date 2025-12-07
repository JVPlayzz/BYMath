<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>BYMath — Kindergarten Quiz</title>
<style>
  :root{
    --bg:#f7fbff; --card:#ffffff; --accent:#2b7cff; --muted:#6b7280;
    --success:#16a34a; --danger:#ef4444; font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  }
  body{
    margin:0; padding:32px; background:linear-gradient(180deg,#eaf4ff 0%, #f7fbff 100%); color:#0f172a;
  }
  .container{max-width:900px; margin:0 auto;}
  header{display:flex; align-items:center; gap:16px; margin-bottom:18px;}
  h1{margin:0; font-size:28px;}
  .brand{display:flex;align-items:center;gap:12px}
  .logo{width:56px;height:56px;border-radius:10px;background:linear-gradient(135deg,var(--accent),#7cc6ff);display:flex;align-items:center;justify-content:center;color:white;font-weight:700;font-size:18px;}
  .card{background:var(--card); padding:22px; border-radius:12px; box-shadow:0 6px 18px rgba(11,22,50,0.06); margin-bottom:18px;}
  label{display:block;margin-top:8px;color:var(--muted);font-size:14px}
  input[type=text], input[type=password], textarea{
    width:100%; padding:10px 12px; margin-top:6px; border-radius:8px; border:1px solid #e6eefc; font-size:16px;
    box-sizing:border-box;
  }
  button{background:var(--accent); color:white; border:none; padding:10px 14px; border-radius:10px; font-weight:600; cursor:pointer;}
  small{color:var(--muted)}
  .center{display:flex; justify-content:center; align-items:center}
  .unit-grid{display:flex; gap:12px; flex-wrap:wrap}
  .unit-btn{padding:16px 20px; border-radius:12px; background:#f0f7ff; border:1px dashed #cfe7ff; cursor:pointer; min-width:140px; text-align:center; font-weight:700}
  .quiz-header{display:flex; justify-content:space-between; align-items:center; gap:16px; flex-wrap:wrap}
  .timer{font-weight:800; font-size:18px; color:var(--accent)}
  .score{font-weight:700; color:var(--muted)}
  .question-area{margin-top:18px; padding:16px; border-radius:8px; background:linear-gradient(180deg,#ffffff,#fbfdff); border:1px solid #eef7ff}
  .prompt{font-size:20px; margin-bottom:12px}
  .feedback{margin-top:10px; font-weight:700}
  .feedback.ok{color:var(--success)}
  .feedback.bad{color:var(--danger)}
  .controls{display:flex; gap:8px; margin-top:12px; align-items:center}
  .small-btn{background:#eef6ff;color:var(--accent); padding:8px 10px; border-radius:8px; border:1px solid #d6edff; cursor:pointer}
  footer{margin-top:18px; text-align:center; color:var(--muted)}
  @media (max-width:520px){ .brand h1{font-size:20px} .logo{width:48px;height:48px} }
</style>
</head>
<body>
<div class="container">
  <header>
    <div class="brand">
      <div class="logo">BYM</div>
      <div>
        <h1>BYMath</h1>
        <small>Beginner Kindergarten Math — Unit 1</small>
      </div>
    </div>
  </header>

  <!-- Login card -->
  <section id="login-screen" class="card">
    <h2>Sign in to BYMath</h2>
    <p><small>Enter your name and a password to begin. (This is client-side — any non-empty password will work.)</small></p>
    <label for="name">Name</label>
    <input id="name" type="text" placeholder="Type your name..." />
    <label for="password">Password</label>
    <input id="password" type="password" placeholder="Type a password..." />
    <div style="margin-top:12px; display:flex; gap:10px;">
      <button id="loginBtn">Submit</button>
      <button id="demoBtn" class="small-btn">Quick Demo</button>
    </div>
  </section>

  <!-- Unit selection -->
  <section id="unit-screen" class="card" style="display:none;">
    <h2>Hello, <span id="player-name"></span> — choose a unit</h2>
    <p><small>Select a unit to start. For now Unit 1 has beginner questions (0–20).</small></p>
    <div class="unit-grid" style="margin-top:12px;">
      <div class="unit-btn" data-unit="1">Unit 1 — Numbers 0–20</div>
      <!-- future units could appear -->
    </div>
    <div style="margin-top:12px;"><button id="startUnitBtn">Start Unit 1</button></div>
  </section>

  <!-- Quiz -->
  <section id="quiz-screen" class="card" style="display:none;">
    <div class="quiz-header">
      <div>
        <div class="timer" id="timer">10:00</div>
        <div class="score" id="score">Answered: 0 | Correct: 0</div>
      </div>
      <div>
        <small id="unit-label">Unit 1</small>
      </div>
    </div>

    <div class="question-area">
      <div class="prompt" id="prompt">Ready?</div>
      <input id="answer" type="text" placeholder="Type your answer here (for sequences use spaces or commas)" />
      <div class="controls">
        <button id="submitAnswer">Submit</button>
        <button id="skipBtn" class="small-btn">Skip</button>
        <button id="endBtn" class="small-btn">End Quiz</button>
      </div>
      <div id="feedback" class="feedback" aria-live="polite"></div>
    </div>
  </section>

  <!-- Results -->
  <section id="results-screen" class="card" style="display:none;">
    <h2>Time's up — Results</h2>
    <p id="results-summary"></p>
    <div style="margin-top:12px;">
      <button id="retryBtn">Play again (Unit 1)</button>
      <button id="backToUnits" class="small-btn">Back to units</button>
    </div>
  </section>

  <footer><small>Made with ❤️ — BYMath (client-only demo)</small></footer>
</div>

<script>
/* BYMath — client-side only quiz
   - Save as HTML and open in browser
   - Any non-empty password accepted (no backend)
*/

const loginScreen = document.getElementById('login-screen');
const unitScreen = document.getElementById('unit-screen');
const quizScreen = document.getElementById('quiz-screen');
const resultsScreen = document.getElementById('results-screen');

const nameInput = document.getElementById('name');
const passwordInput = document.getElementById('password');
const loginBtn = document.getElementById('loginBtn');
const demoBtn = document.getElementById('demoBtn');
const playerNameSpan = document.getElementById('player-name');
const startUnitBtn = document.getElementById('startUnitBtn');

const promptEl = document.getElementById('prompt');
const answerEl = document.getElementById('answer');
const submitAnswerBtn = document.getElementById('submitAnswer');
const skipBtn = document.getElementById('skipBtn');
const endBtn = document.getElementById('endBtn');
const feedbackEl = document.getElementById('feedback');
const timerEl = document.getElementById('timer');
const scoreEl = document.getElementById('score');
const resultsSummary = document.getElementById('results-summary');

const retryBtn = document.getElementById('retryBtn');
const backToUnits = document.getElementById('backToUnits');

let state = {
  playerName: '',
  unit: 1,
  timeLeft: 600, // 10 minutes in seconds
  timerInterval: null,
  totalAsked: 0,
  totalCorrect: 0,
  currentQuestion: null
};

// --- Login handlers ---
loginBtn.addEventListener('click', () => {
  const nm = nameInput.value.trim();
  const pw = passwordInput.value;
  if (!nm) {
    alert('Please type your name.');
    return;
  }
  if (!pw) {
    alert('Please type a password (it can be any non-empty value).');
    return;
  }
  startSession(nm);
});
demoBtn.addEventListener('click', ()=>{ // quick demo
  startSession('Demo Student');
});

function startSession(name){
  state.playerName = name;
  playerNameSpan.textContent = name;
  loginScreen.style.display = 'none';
  unitScreen.style.display = 'block';
  resultsScreen.style.display = 'none';
  quizScreen.style.display = 'none';
}

// --- Unit start ---
startUnitBtn.addEventListener('click', ()=> {
  startUnit(1);
});

function startUnit(unitNumber){
  state.unit = unitNumber;
  unitScreen.style.display = 'none';
  quizScreen.style.display = 'block';
  resetQuizState();
  startTimer();
  nextQuestion();
}

// --- Timer ---
function startTimer(){
  state.timeLeft = 600; // 10 minutes
  updateTimerDisplay();
  if(state.timerInterval) clearInterval(state.timerInterval);
  state.timerInterval = setInterval(() => {
    state.timeLeft--;
    updateTimerDisplay();
    if (state.timeLeft <= 0){
      clearInterval(state.timerInterval);
      endQuiz();
    }
  }, 1000);
}
function updateTimerDisplay(){
  const mm = Math.floor(state.timeLeft / 60).toString().padStart(2,'0');
  const ss = (state.timeLeft % 60).toString().padStart(2,'0');
  timerEl.textContent = `${mm}:${ss}`;
}

// --- Quiz logic ---
function resetQuizState(){
  state.totalAsked = 0;
  state.totalCorrect = 0;
  state.currentQuestion = null;
  feedbackEl.textContent = '';
  updateScore();
}

function updateScore(){
  scoreEl.textContent = `Answered: ${state.totalAsked} | Correct: ${state.totalCorrect}`;
}

// generate a random integer inclusive
function randInt(a,b){ return Math.floor(Math.random()*(b-a+1))+a; }

// question generators (0-20 only)
function generateQuestionUnit1(){
  // choose among templates with weights
  const templates = [
    genAfterBefore, // after / before single number
    genAfterBefore,
    genCountForward,
    genCountBackward,
    genAfterBefore
  ];
  const gen = templates[Math.floor(Math.random()*templates.length)];
  return gen();
}

function genAfterBefore(){
  const isAfter = Math.random() < 0.6; // more likely after
  const num = randInt(0,20);
  if (isAfter){
    // avoid after 20 (no 21) => if 20, pick lower
    const n = (num === 20 ? num-1 : num);
    return {
      prompt: `What is after ${n}?`,
      type: 'after',
      validate: (ans) => Number(ans) === (n+1),
      answerText: `${n+1}`
    };
  } else {
    // avoid before 0
    const n = (num === 0 ? num+1 : num);
    return {
      prompt: `What is before ${n}?`,
      type: 'before',
      validate: (ans) => Number(ans) === (n-1),
      answerText: `${n-1}`
    };
  }
}

function genCountForward(){
  // choose start and end such that 0 <= start < end <= 20 and length between 2 and 7
  const len = randInt(2,7);
  const start = randInt(0, 20 - len);
  const end = start + randInt(1, Math.min(7, 20-start));
  const list = [];
  for(let i=start;i<=end;i++) list.push(i);
  return {
    prompt: `Count forward from ${start} to ${end}. Type all numbers separated by a space.`,
    type: 'count-forward',
    validate: (ans) => normalizeSequence(ans).join(' ') === list.join(' '),
    answerText: list.join(' ')
  };
}

function genCountBackward(){
  // choose start > end
  const len = randInt(2,7);
  const end = randInt(0, 20 - 1);
  const start = randInt(end + 1, Math.min(20, end + 7));
  const list = [];
  for(let i=start;i>=end;i--) list.push(i);
  return {
    prompt: `Count backward from ${start} down to ${end}. Type all numbers separated by a space.`,
    type: 'count-backward',
    validate: (ans) => normalizeSequence(ans).join(' ') === list.join(' '),
    answerText: list.join(' ')
  };
}

// helper to normalize sequence answers (accept commas or spaces)
function normalizeSequence(txt){
  if (!txt) return [];
  // split by comma or space, remove empties, convert to numbers, then back to strings
  const parts = txt.split(/[, ]+/).map(s=>s.trim()).filter(Boolean);
  // filter out things that aren't numeric
  const nums = parts.map(p => {
    // remove occasional punctuation
    const cleaned = p.replace(/[^\d\-]/g,'');
    // accept numbers only
    const n = Number(cleaned);
    if (Number.isFinite(n)) return n;
    return NaN;
  }).filter(n => !Number.isNaN(n));
  return nums.map(n => String(n));
}

// generate next question and show prompt
function nextQuestion(){
  state.currentQuestion = generateQuestionUnit1();
  promptEl.textContent = state.currentQuestion.prompt;
  answerEl.value = '';
  answerEl.focus();
  feedbackEl.textContent = '';
}

// submit logic
submitAnswerBtn.addEventListener('click', submitAnswer);
answerEl.addEventListener('keydown', (e) => { if (e.key === 'Enter') submitAnswer(); });

function submitAnswer(){
  const raw = answerEl.value.trim();
  if (raw === '') { feedbackEl.textContent = 'Please type an answer (or Skip).'; feedbackEl.className='feedback bad'; return; }
  const q = state.currentQuestion;
  state.totalAsked++;
  let gotIt = false;
  try {
    gotIt = q.validate(raw);
  } catch(e){
    gotIt = false;
  }
  if (gotIt){
    state.totalCorrect++;
    feedbackEl.textContent = 'Correct! ✅';
    feedbackEl.className = 'feedback ok';
  } else {
    feedbackEl.textContent = `Not quite — correct answer: ${q.answerText}`;
    feedbackEl.className = 'feedback bad';
  }
  updateScore();
  // brief pause then next question
  setTimeout(() => {
    // If time left, continue
    if (state.timeLeft <= 0) { endQuiz(); return; }
    nextQuestion();
  }, 900);
}

skipBtn.addEventListener('click', () => {
  // mark as asked but not correct
  state.totalAsked++;
  updateScore();
  feedbackEl.textContent = `Skipped. Answer would be: ${state.currentQuestion.answerText}`;
  feedbackEl.className = 'feedback bad';
  setTimeout(()=> {
    if (state.timeLeft <= 0) { endQuiz(); return; }
    nextQuestion();
  }, 900);
});

endBtn.addEventListener('click', () => {
  if (confirm('End the quiz now?')) endQuiz();
});

function endQuiz(){
  // stop timer
  if (state.timerInterval) clearInterval(state.timerInterval);
  quizScreen.style.display = 'none';
  resultsScreen.style.display = 'block';
  const pct = state.totalAsked === 0 ? 0 : Math.round(state.totalCorrect / state.totalAsked * 100);
  resultsSummary.innerHTML = `<strong>${state.playerName}</strong>, you answered <strong>${state.totalAsked}</strong> questions, and got <strong>${state.totalCorrect}</strong> correct. (${pct}%)`;
}

// --- Retry / Back ---
retryBtn.addEventListener('click', () => {
  resultsScreen.style.display = 'none';
  startUnit(state.unit);
});
backToUnits.addEventListener('click', () => {
  resultsScreen.style.display = 'none';
  unitScreen.style.display = 'block';
});

// --- Initialize a first-time friendly message ---
(function init(){
  // optional: prefill name with stored name
  const stored = localStorage.getItem('bymath_name');
  if (stored) nameInput.value = stored;
})();
  
// store name on blur for convenience
nameInput.addEventListener('blur', ()=> {
  const nm = nameInput.value.trim();
  if (nm) localStorage.setItem('bymath_name', nm);
});

</script>
</body>
</html>
