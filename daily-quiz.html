<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Daily Medical Quiz</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    *{margin:0;padding:0;box-sizing:border-box}
    body{font-family:Arial,sans-serif;background:#667eea;min-height:100vh;padding:20px;color:#111}
    h1{text-align:center;color:#fff;margin:10px 0 20px}
    .container{max-width:700px;margin:0 auto;background:#fff;border-radius:12px;padding:20px;box-shadow:0 4px 10px rgba(0,0,0,0.15)}
    .top-row{display:flex;justify-content:space-between;align-items:center;gap:10px;flex-wrap:wrap}
    .stat{text-align:center;font-size:12px}
    .stat span{display:block;font-size:22px;font-weight:bold;color:#667eea}
    .progress{margin:15px 0}
    .bar{height:8px;border-radius:4px;background:#e5e7eb;overflow:hidden}
    .fill{height:100%;width:0;background:#667eea;transition:width .3s}
    .menu{text-align:center;margin-top:10px}
    .menu p{margin:10px 0;color:#555}
    .question-box{margin-top:10px}
    .q-header{font-size:14px;color:#666;margin-bottom:5px}
    .q-text{background:#f9fafb;border-left:4px solid #667eea;border-radius:8px;padding:15px;font-size:15px;line-height:1.5}
    .options{margin-top:15px;display:flex;flex-direction:column;gap:8px}
    .option{border:2px solid #e5e7eb;border-radius:8px;padding:10px;cursor:pointer;font-size:14px;background:#fff;display:flex;align-items:flex-start;gap:8px;transition:all .2s}
    .option:hover{border-color:#667eea;background:#eff6ff}
    .option.selected{border-color:#667eea;background:#eef2ff}
    .option.correct{border-color:#16a34a;background:#dcfce7}
    .option.wrong{border-color:#dc2626;background:#fee2e2}
    .feedback{margin-top:10px;padding:10px;border-radius:8px;font-size:14px;display:none}
    .feedback.show{display:block}
    .feedback.ok{background:#dcfce7;color:#166534}
    .feedback.bad{background:#fee2e2;color:#991b1b}
    button{width:100%;padding:12px;border:none;border-radius:8px;font-size:15px;font-weight:bold;cursor:pointer;margin-top:12px}
    .btn-main{background:#667eea;color:#fff}
    .btn-main:disabled{opacity:.5;cursor:not-allowed}
    .btn-secondary{background:#e5e7eb}
    select{padding:6px 10px;border-radius:8px;border:1px solid #d1d5db;font-size:14px}
    @media(max-width:600px){.container{padding:15px}.q-text{font-size:14px}}
  </style>
</head>
<body>
<h1>📚 Daily Medical Quiz</h1>
<div class="container">
  <div class="top-row">
    <div class="stat">Today<span id="statToday">-</span></div>
    <div class="stat">Streak<span id="statStreak">0</span></div>
    <div class="stat">Avg<span id="statAvg">-</span></div>
    <div>
      <label style="font-size:12px;color:#555">Questions:
        <select id="qCount">
          <option value="5">5</option>
          <option value="10">10</option>
          <option value="20">20</option>
          <option value="50">50</option>
          <option value="100">100</option>
          <option value="150">150</option>
          <option value="1000">1000</option>
        </select>
      </label>
    </div>
  </div>

  <div class="progress">
    <div class="bar"><div class="fill" id="progressFill"></div></div>
  </div>

  <div id="content" class="menu">
    <h2 style="color:#667eea;margin-bottom:10px">Daily Medical Quiz</h2>
    <p>Choose how many questions you want and press Start.</p>
    <button class="btn-main" onclick="startQuiz()">Start Quiz</button>
  </div>
</div>

<script>
const allQuestions = [
  {q:"10‑month‑old boy poorly gaining weight, persistent cough, thick sputum, 3x pneumonia, sweat chloride >80 mEq/L. Diagnosis?",
   o:["Mucoviscidosis (cystic fibrosis)","Bronchial asthma","Congenital lung abnormality","Foreign body in bronchi","Chronic bronchitis"],c:0},
  {q:"42‑year‑old man, 20‑year duodenal ulcer, heaviness after eating, rotten eructation, vomiting of yesterday’s food, weight loss. Complication?",
   o:["Ulcer penetration","Chronic pancreatitis","Ulcerative stenosis of pylorus","Covered perforation","Stomach cancer"],c:2},
  {q:"22‑year‑old pregnant woman, edema, headache, nausea, BP 160/130, tonic‑clonic seizures with cyanosis. Diagnosis?",
   o:["Hypertensive crisis","Diabetic coma","Chorea","Eclampsia","Epilepsy"],c:3},
  {q:"45‑year‑old with papular rash and silvery scales, stearin spot sign. Diagnosis?",
   o:["Dermatophytosis","Eczema","Lichen planus","Allergic dermatitis","Psoriasis"],c:4},
  {q:"23‑year‑old swallowed brake fluid, anuria 5 days, creatinine 0.569 mmol/L. Best treatment?",
   o:["Diuretics","Antidotal therapy","Hemodialysis","Plasmapheresis","Detoxication"],c:2},
  {q:"19‑year‑old woman after massive postpartum hemorrhage, amenorrhea, weight loss, no lactation. Diagnosis?",
   o:["Stein‑Leventhal syndrome","Hypothyroidism","Astheno‑vegetative syndrome","Genital tuberculosis","Sheehan syndrome"],c:4},
  {q:"64‑year‑old with sigmoid cancer and chronic DVT, pre‑op prophylaxis of DVT?",
   o:["Phenindione","Low‑molecular‑weight heparin","Regular heparin","Aspirin","Rheopolyglucin"],c:1},
  {q:"40‑year‑old smoker, chronic morning cough with mucopurulent sputum, mild temp, purulent endobronchitis. Diagnosis?",
   o:["Lung cancer","Bronchial asthma","Community‑acquired pneumonia","Pulmonary TB","Chronic bronchitis"],c:4},
  {q:"5‑day‑old newborn, physiologic status good, indirect bilirubin 140 µmol/L, direct 0. Diagnosis?",
   o:["Hemolytic anemia","Congenital hepatitis","Biliary atresia","Hemolytic disease of newborn","Physiologic jaundice"],c:4},
  {q:"57‑year‑old miner, heavy smoker, X‑ray triangular shadow in right middle lobe pointing to hilum. Diagnosis?",
   o:["Pneumoconiosis","TB of right lung","Cancer of right lung","Chronic bronchitis","Right‑sided pneumonia"],c:2},
  {q:"25‑year‑old, hematuria after chilling, BP 160/110, proteinuria 3.5 g/L, RBCs plentiful. Most informative test?",
   o:["Excretory urography","Renal US","Cystoscopy","Tomography","Kidney biopsy"],c:4},
  {q:"37‑year‑old woman lost vision at work, needs retraining. Type of rehabilitation?",
   o:["Occupational","Social","Psychological","Medical","Target"],c:0},
  {q:"44‑year‑old drinker, 6‑day binge, insomnia, visual hallucinations, autonomic signs. Diagnosis?",
   o:["Somatogenic psychosis","Delirium tremens","Obsessive‑phobic neurosis","Occupational delirium","Alcoholic hallucinosis"],c:1},
  {q:"50‑year‑old with earth‑gray skin, icteric sclera, methemoglobin 62% after paint‑factory exposure. Diagnosis?",
   o:["Chronic cyanide poisoning","Acute aniline poisoning","Acute toluene poisoning","Acquired hemolytic anemia","Acute viral hepatitis"],c:1},
  {q:"18‑year‑old ate salad with smoked fish, 6 h later: weakness, diplopia, dysphagia, mydriasis, afebrile. Diagnosis?",
   o:["Botulism","Mushroom poisoning","Salmonellosis","Fish histamine poisoning","Staph intoxication"],c:0},
  {q:"8‑year‑old 1 year after hepatitis B, now fatigue, poor appetite, ALT 220 U/L. Interpretation?",
   o:["Recurrence hepatitis B","Biliary dyskinesia","Cirrhosis","Development of chronic hepatitis","Residual effects"],c:3},
  {q:"45‑year‑old with duodenal ulcer, pale, Hb 88 g/L, microcytosis, positive occult blood. Treatment?",
   o:["Steroids + cytostatics","Vit C + CaCl2","IM B12","Iron therapy + diet","Packed RBC transfusion"],c:3},
  {q:"Polycystic kidney disease, diuresis 2–2.5 L/day, GFR 34 mL/min. Stage of CKD?",
   o:["Polyuric","Intermittent","Latent","Terminal","Compensated"],c:4},
  {q:"27‑year‑old woman, lumpy stools with mucus, pain relieved by defecation, colonoscopy normal. Diagnosis?",
   o:["Crohn disease","Irritable bowel syndrome","Whipple disease","Chronic non‑ulcer colitis","Chronic enteritis"],c:1},
  {q:"46‑year‑old woman woken at 3 a.m. by RUQ pain to shoulder, 39 °C, guarding. Diagnosis?",
   o:["Cholelithiasis","CBD stricture","Unstable angina","Peptic ulcer disease","Acute cholecystitis"],c:4},
  // you can append more questions from the PDF here later
];

let state = {
  today: new Date().toDateString(),
  selected: [],
  index: 0,
  correct: 0,
  total: 5,
  done: false,
  stats: {streak:0,scores:[]}
};

function loadStats(){
  const raw = localStorage.getItem('medQuizStats');
  if(raw){
    state.stats = JSON.parse(raw);
  }
}
function saveStats(){
  localStorage.setItem('medQuizStats', JSON.stringify(state.stats));
}
function shuffle(arr){
  for(let i=arr.length-1;i>0;i--){
    const j = Math.floor(Math.random()*(i+1));
    [arr[i],arr[j]]=[arr[j],arr[i]];
  }
}
function startQuiz(){
  const sel = document.getElementById('qCount');
  state.total = Math.min(parseInt(sel.value,10), allQuestions.length);
  state.index = 0;
  state.correct = 0;
  state.done = false;
  state.selected = allQuestions.slice();
  shuffle(state.selected);
  state.selected = state.selected.slice(0,state.total);
  document.getElementById('progressFill').style.width = '0%';
  showQuestion();
}
function showQuestion(){
  const c = document.getElementById('content');
  if(state.index>=state.total){
    finishQuiz();
    return;
  }
  const q = state.selected[state.index];
  const progressPct = Math.round(state.index/state.total*100);
  document.getElementById('progressFill').style.width = progressPct+'%';
  let html = `
    <div class="question-box">
      <div class="q-header">Question ${state.index+1} of ${state.total}</div>
      <div class="q-text">${q.q}</div>
      <div class="options">
  `;
  q.o.forEach((opt,i)=>{
    html += `
      <div class="option" onclick="selectOption(${i})" id="opt${i}">
        <span><strong>${String.fromCharCode(65+i)}.</strong></span>
        <span>${opt}</span>
      </div>`;
  });
  html += `
      </div>
      <div class="feedback" id="feedback"></div>
      <button class="btn-main" id="btnNext" onclick="submitAnswer()" disabled>Submit</button>
      <button class="btn-secondary" onclick="startQuiz()">Retake (new set)</button>
    </div>`;
  c.innerHTML = html;
}
let currentChoice = null;
function selectOption(i){
  currentChoice = i;
  document.querySelectorAll('.option').forEach((el,idx)=>{
    el.classList.toggle('selected', idx===i);
  });
  document.getElementById('btnNext').disabled = false;
}
function submitAnswer(){
  if(currentChoice==null) return;
  const q = state.selected[state.index];
  const fb = document.getElementById('feedback');
  const isCorrect = currentChoice===q.c;
  if(isCorrect) state.correct++;
  document.querySelectorAll('.option').forEach((el,idx)=>{
    el.classList.remove('selected');
    if(idx===q.c) el.classList.add('correct');
    else if(idx===currentChoice) el.classList.add('wrong');
  });
  fb.className = 'feedback show '+(isCorrect?'ok':'bad');
  fb.innerHTML = isCorrect ? '✅ Correct' :
    '❌ Incorrect. Correct answer: <strong>'+q.o[q.c]+'</strong>';
  document.getElementById('btnNext').textContent =
    state.index===state.total-1 ? 'See Results' : 'Next Question';
  document.getElementById('btnNext').onclick = ()=>{
    currentChoice = null;
    state.index++;
    showQuestion();
  };
}
function finishQuiz(){
  state.done = true;
  const pct = Math.round(state.correct/state.total*100);
  const c = document.getElementById('content');
  c.className = 'menu';
  c.innerHTML = `
    <h2 style="color:#667eea;margin-top:10px">Session Complete</h2>
    <p style="font-size:26px;font-weight:bold;color:#111;margin:10px 0">
      ${state.correct}/${state.total} (${pct}%)
    </p>
    <p>Retake as many times as you want or change question count.</p>
    <button class="btn-main" onclick="startQuiz()">Retake Quiz</button>
  `;
  updateStats(pct);
}
function updateStats(pct){
  loadStats();
  state.stats.scores.push({date:state.today,score:pct});
  // simple streak: full‑score days in a row
  if(pct===100) state.stats.streak = (state.stats.streak||0)+1;
  else state.stats.streak = 0;
  saveStats();
  renderStats();
}
function renderStats(){
  loadStats();
  const scores = state.stats.scores;
  const todayEntry = scores.length ? scores[scores.length-1] : null;
  document.getElementById('statToday').textContent =
    todayEntry ? todayEntry.score+'%' : '-';
  document.getElementById('statStreak').textContent =
    state.stats.streak || 0;
  if(scores.length){
    const avg = Math.round(scores.reduce((s,x)=>s+x.score,0)/scores.length);
    document.getElementById('statAvg').textContent = avg+'%';
  } else {
    document.getElementById('statAvg').textContent = '-';
  }
}
loadStats();
renderStats();
</script>
</body>
</html>
