<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medical Board Tutor</title>
    <style>
        :root { --primary: #2563eb; --background: #f8fafc; --text: #0f172a; --ai-color: #7c3aed; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background: var(--background); color: var(--text); padding: 20px; display: flex; justify-content: center; }
        .app-container { background: #fff; width: 100%; max-width: 800px; padding: 2rem; border-radius: 16px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .hidden { display: none; }
        button { cursor: pointer; border: none; font-weight: bold; transition: opacity 0.2s; }
        button:hover { opacity: 0.9; }
        
        /* Main Start Button */
        .primary-btn { background: var(--primary); color: white; width: 100%; font-size: 1.2rem; padding: 15px; border-radius: 12px; margin-top: 10px; }
        
        /* AI Button */
        .ai-btn { 
            background: linear-gradient(135deg, #7c3aed, #6d28d9); 
            color: white; padding: 12px 24px; border-radius: 8px; 
            margin: 20px auto; display: block; width: 100%; font-size: 1rem;
            box-shadow: 0 4px 6px rgba(124, 58, 237, 0.2);
        }
        
        /* Quiz Options */
        .option-btn { display: block; width: 100%; padding: 15px; margin: 10px 0; border: 2px solid #e2e8f0; background: white; text-align: left; border-radius: 10px; font-size: 1rem; }
        .option-btn:hover { border-color: var(--primary); background: #eff6ff; }
        .option-btn.correct { background: #dcfce7; border-color: #16a34a; color: #14532d; }
        .option-btn.wrong { background: #fee2e2; border-color: #dc2626; color: #7f1d1d; }
        
        /* AI Box */
        #ai-response-box { background: #f3e8ff; padding: 20px; border-radius: 12px; margin-top: 15px; display: none; border: 1px solid #d8b4fe; color: #4c1d95; line-height: 1.6; }
        
        /* Navigation */
        .nav-btn { background: white; border: 2px solid #e2e8f0; padding: 10px 20px; border-radius: 8px; color: #64748b; }
        .header { display: flex; justify-content: space-between; border-bottom: 1px solid #e2e8f0; padding-bottom: 15px; margin-bottom: 20px; }
    </style>
</head>
<body>

<div class="app-container">
    <div id="setup-view" style="text-align:center;">
        <h1 style="color: var(--primary);">Medical Board Tutor</h1>
        <p>AI Tutor Status: <strong style="color: #16a34a;">ONLINE</strong></p>
        <button class="primary-btn" onclick="startNewQuiz()">Start Session</button>
    </div>

    <div id="quiz-view" class="hidden">
        <div class="header">
            <span id="progress-text" style="font-weight:bold; color:#64748b;">Question 1</span>
            <span id="score-text" style="font-weight:bold; color:var(--primary);">Score: 0%</span>
        </div>
        
        <p id="q-text" style="font-size: 1.25rem; font-weight: 600; margin-bottom: 1.5rem;"></p>
        <div id="options-container"></div>
        
        <button class="ai-btn" onclick="askAI()">✨ Explain This Question</button>
        <div id="ai-response-box"></div>

        <div style="margin-top: 30px; display: flex; justify-content: space-between;">
            <button class="nav-btn" onclick="nav(-1)">Previous</button>
            <button class="nav-btn" onclick="nav(1)">Next</button>
        </div>
    </div>
</div>

<script>
    // --- YOUR API KEY ---
    const API_KEY = "AIzaSyAwvv2MjKEYA8VG8UEvVrv3zi7AiNARaxU"; 

    // --- QUESTION DATABASE ---
    const db = [
        { q: "A 10-month-old boy is poorly gaining weight. His mother complains about his constant persistent cough. Sputum is thick and viscous. The boy had pneumonia three times. His sweat chloride levels are over 80 mEq/L. Diagnosis?", o: ["Mucoviscidosis (cystic fibrosis)", "Bronchial asthma", "Congenital lung abnormality", "Foreign body", "Chronic bronchitis"], a: 0 },
        { q: "A 42-year-old man, duodenal ulcer 20 yrs, heaviness in stomach, eructation with smell of decay, vomiting food from day before, 'sloshing sound'. Complication?", o: ["Ulcer penetration", "Chronic pancreatitis", "Ulcerative stenosis of the pylorus", "Covered perforation", "Stomach cancer"], a: 2 },
        { q: "Pregnant woman, severe condition, edemas, headache, BP 160/130. Seizures, foam on lips. Diagnosis?", o: ["Hypertensive crisis", "Diabetic coma", "Chorea", "Eclampsia", "Epilepsy"], a: 3 },
        { q: "Rash after stress. Plaques with silvery-yellow scales. 'Stearin spot' positive. Diagnosis?", o: ["Dermatophytosis", "Eczema", "Lichen ruber planus", "Allergic dermatitis", "Psoriasis"], a: 4 },
        { q: "Swallowed brake fluid. Anuria 5 days, creatinine 0.569. Treatment?", o: ["Diuretics", "Antidotal therapy", "Hemodialysis", "Plasmapheresis", "Detoxication"], a: 2 },
        { q: "19yo woman, amenorrhea, lethargy, weight loss. Post-partum hemorrhage 1 year ago, no lactation. Hypoestrogenemia. Diagnosis?", o: ["Stein-Leventhal", "Hypothyroidism", "Astheno-vegetative", "Genital TB", "Sheehan syndrome"], a: 4 },
        { q: "64yo, sigmoid tumor, chronic thrombophlebitis. Prevention of DVT?", o: ["Phenylinum", "Low-molecular-weight heparin", "Regular heparin", "Aspirin", "Rheopolyglucin"], a: 1 },
        { q: "40yo smoker, cough, mucopurulent sputum, fever 37.6. Diffuse dry crackles. Purulent catarrhal endobronchitis. Diagnosis?", o: ["Lung cancer", "Asthma", "Pneumonia", "TB", "Chronic bronchitis"], a: 4 },
        { q: "5-day-old, icteric skin. Mother A(II)Rh+, Child 0(I)Rh+. Bilirubin 140 (indirect). Diagnosis?", o: ["Hemolytic anemia", "Congenital hepatitis", "Biliary atresia", "Hemolytic disease of newborn", "Physiological jaundice"], a: 4 },
        { q: "57yo miner, smoker. Chest pain, blood sputum. X-ray: triangular shadow, apex to root. Diagnosis?", o: ["Pneumoconiosis", "TB", "Cancer of right lung", "Bronchitis", "Pleuropneumonia"], a: 2 }
    ];

    let activeQ = [];
    let answers = {};
    let idx = 0;

    function startNewQuiz() {
        activeQ = db; 
        document.getElementById('setup-view').classList.add('hidden');
        document.getElementById('quiz-view').classList.remove('hidden');
        render();
    }

    function render() {
        const q = activeQ[idx];
        document.getElementById('progress-text').innerText = `Question ${idx + 1}/${activeQ.length}`;
        document.getElementById('q-text').innerText = q.q;
        
        let correctCount = 0;
        let answeredCount = 0;
        Object.keys(answers).forEach(k => {
            answeredCount++;
            if(answers[k] === activeQ[k].a) correctCount++;
        });
        const score = answeredCount === 0 ? 0 : Math.round((correctCount/answeredCount)*100);
        document.getElementById('score-text').innerText = `Score: ${score}%`;

        const div = document.getElementById('options-container');
        div.innerHTML = '';
        
        q.o.forEach((opt, i) => {
            const btn = document.createElement('button');
            btn.className = 'option-btn';
            btn.innerText = opt;
            if(answers[idx] !== undefined) {
                if(i === q.a) btn.classList.add('correct');
                if(i === answers[idx] && i !== q.a) btn.classList.add('wrong');
            }
            btn.onclick = () => { answers[idx] = i; render(); };
            div.appendChild(btn);
        });
        
        document.getElementById('ai-response-box').style.display = 'none';
    }

    function nav(d) {
        if(idx + d >= 0 && idx + d < activeQ.length) { idx += d; render(); }
    }

    // --- AI FUNCTION ---
    async function askAI() {
        const q = activeQ[idx];
        const box = document.getElementById('ai-response-box');
        box.style.display = 'block';
        box.innerHTML = "<strong>🤖 AI is analyzing...</strong>";

        const prompt = `
        You are a medical tutor. 
        Question: "${q.q}"
        Correct Answer: "${q.o[q.a]}"
        
        Task:
        1. Explain why this is the correct diagnosis.
        2. Briefly explain why the other options are wrong.
        Keep it clear and helpful.
        `;
        
        try {
            // FIXED MODEL NAME: gemini-1.5-flash
            const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${API_KEY}`;
            
            const response = await fetch(url, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
            });
            
            const data = await response.json();
            
            if(data.error) {
                box.innerHTML = `<span style="color:red">AI Error: ${data.error.message}</span>`;
            } else {
                const text = data.candidates[0].content.parts[0].text
                    .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
                    .replace(/\n/g, '<br>');
                box.innerHTML = text;
            }
                
        } catch (e) {
            box.innerHTML = `<span style="color:red">Connection Error: ${e.message}</span>`;
        }
    }
</script>
</body>
</html>
