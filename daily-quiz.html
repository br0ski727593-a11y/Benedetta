<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Medical Tutor</title>
    <style>
        :root {
            --primary: #2563eb;
            --primary-hover: #1d4ed8;
            --surface: #ffffff;
            --background: #f1f5f9;
            --text: #0f172a;
            --text-secondary: #475569;
            --success: #10b981;
            --error: #ef4444;
            --border: #e2e8f0;
            --ai-color: #7c3aed; /* Purple for AI */
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            background-color: var(--background);
            color: var(--text);
            line-height: 1.6;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            min-height: 100vh;
        }

        .app-container {
            background: var(--surface);
            width: 100%;
            max-width: 800px;
            border-radius: 16px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            padding: 2rem;
            display: flex;
            flex-direction: column;
            gap: 1.5rem;
        }

        /* Setup Screen */
        .setup-screen { text-align: center; padding: 1rem 0; }
        .input-group { margin-bottom: 1.5rem; text-align: left; }
        .input-group label { display: block; margin-bottom: 0.5rem; font-weight: 600; font-size: 0.9rem;}
        
        input[type="text"], input[type="password"], select {
            width: 100%;
            padding: 0.8rem;
            border: 2px solid var(--border);
            border-radius: 8px;
            font-size: 1rem;
            box-sizing: border-box;
        }

        /* Quiz Interface */
        .header {
            display: flex; justify-content: space-between; align-items: center;
            padding-bottom: 1rem; border-bottom: 1px solid var(--border);
        }
        
        .progress-bar-bg { width: 100%; height: 6px; background: var(--border); border-radius: 3px; overflow: hidden; margin-bottom: 1.5rem; }
        .progress-bar-fill { height: 100%; background: var(--primary); transition: width 0.3s ease; }

        .question-text { font-size: 1.2rem; font-weight: 500; margin-bottom: 1.5rem; }

        .options-grid { display: flex; flex-direction: column; gap: 0.8rem; }

        .option-btn {
            background: #fff; border: 2px solid var(--border); padding: 1rem;
            border-radius: 10px; text-align: left; cursor: pointer; font-size: 1rem;
            transition: all 0.2s;
        }
        .option-btn:hover:not(:disabled) { border-color: var(--primary); background: #eff6ff; }
        .option-btn.selected { border-color: var(--primary); background: #eff6ff; font-weight: 600; }
        .option-btn.correct { border-color: var(--success); background: #ecfdf5; color: #064e3b; }
        .option-btn.wrong { border-color: var(--error); background: #fef2f2; color: #7f1d1d; }

        /* AI Section */
        .ai-section {
            margin-top: 1.5rem;
            background: #f5f3ff; /* Light purple tint */
            border: 1px solid #ddd6fe;
            border-radius: 12px;
            padding: 1.5rem;
            display: none; /* Hidden by default */
        }
        .ai-header { font-weight: 700; color: var(--ai-color); margin-bottom: 0.5rem; display: flex; align-items: center; gap: 8px; }
        .ai-content { font-size: 0.95rem; white-space: pre-wrap; }

        /* Buttons */
        .primary-btn {
            background-color: var(--primary); color: white; border: none;
            padding: 1rem 2rem; border-radius: 8px; font-weight: 600; cursor: pointer; width: 100%;
        }
        .nav-btn {
            background: white; border: 2px solid var(--border); padding: 0.8rem 1.5rem;
            border-radius: 8px; font-weight: 600; cursor: pointer;
        }
        .ai-btn {
            background: linear-gradient(135deg, #7c3aed 0%, #6d28d9 100%);
            color: white; border: none; padding: 0.8rem 1.5rem;
            border-radius: 8px; font-weight: 600; cursor: pointer;
            display: flex; align-items: center; gap: 8px;
            box-shadow: 0 4px 6px -1px rgba(124, 58, 237, 0.3);
            transition: transform 0.1s;
        }
        .ai-btn:hover { transform: translateY(-1px); }
        .ai-btn:active { transform: translateY(1px); }

        .controls { display: flex; justify-content: space-between; align-items: center; margin-top: 2rem; }
        .hidden { display: none !important; }
        
        /* Markdown rendering helper */
        strong { font-weight: 700; }
    </style>
</head>
<body>

<div class="app-container">
    
    <div id="setup-view" class="setup-screen">
        <h1>Medical Board Tutor</h1>
        <p style="color: var(--text-secondary); margin-bottom: 2rem;">
            Database: <span id="total-count" style="font-weight:700">0</span> questions
        </p>

        <div class="input-group">
            <label>1. Study Mode</label>
            <select id="quiz-length">
                <option value="10">10 Questions</option>
                <option value="25">25 Questions</option>
                <option value="50">50 Questions</option>
                <option value="all">All Questions</option>
            </select>
        </div>

        <div class="input-group">
            <label>2. AI Tutor API Key (Optional)</label>
            <input type="password" id="api-key" placeholder="Paste Google Gemini API Key here...">
            <div style="font-size: 0.8rem; color: var(--text-secondary); margin-top: 5px;">
                Required for the "Explain" button. <a href="https://aistudio.google.com/app/apikey" target="_blank" style="color: var(--primary);">Get a free key here</a>.
                (Stored locally on your browser).
            </div>
        </div>

        <button class="primary-btn" onclick="startNewQuiz()">Start Session</button>
        <button id="resume-btn" class="nav-btn hidden" style="margin-top: 10px; width: 100%; border:none; background:none; text-decoration:underline;" onclick="resumeQuiz()">Resume previous</button>
    </div>

    <div id="quiz-view" class="hidden">
        <div class="header">
            <div>
                <div style="font-size: 0.85rem; color: var(--text-secondary);">Progress</div>
                <div id="progress-text" style="font-weight: 700;">1 / 10</div>
            </div>
            <div style="text-align: right;">
                <div style="font-size: 0.85rem; color: var(--text-secondary);">Score</div>
                <div id="score-text" style="font-weight: 700; color: var(--primary);">0%</div>
            </div>
        </div>

        <div style="margin-top: 1rem;">
            <div class="progress-bar-bg"><div id="progress-fill" class="progress-bar-fill" style="width: 0%"></div></div>
        </div>

        <div id="quiz-content">
            <div class="question-text" id="q-text"></div>
            <div class="options-grid" id="options-container"></div>
        </div>

        <div class="controls" style="justify-content: center; margin: 1.5rem 0;">
            <button class="ai-btn" onclick="askAI()" id="ai-trigger-btn">
                <span>✨ Explain with AI</span>
            </button>
        </div>

        <div id="ai-response-box" class="ai-section">
            <div class="ai-header">🤖 AI Tutor Analysis</div>
            <div id="ai-content" class="ai-content">Loading explanation...</div>
        </div>

        <div class="controls">
            <button class="nav-btn" id="prev-btn" onclick="nav(-1)">Previous</button>
            <button class="nav-btn" style="border:none; color:var(--text-secondary)" onclick="exitToMenu()">End</button>
            <button class="primary-btn" id="next-btn" style="width: auto;" onclick="nav(1)">Next</button>
        </div>
    </div>

</div>

<script>
    // --- DATABASE (First 30 Samples included) ---
    const db = [
        {
            q: "A 10-month-old boy is poorly gaining weight. His mother complains about his constant persistent cough. Sputum is thick and viscous. The boy had pneumonia three times. His sweat chloride levels are over 80 mEq/L. What is the most likely diagnosis?",
            o: ["Mucoviscidosis (cystic fibrosis)", "Bronchial asthma", "Congenital lung abnormality", "A foreign body in the bronchi", "Chronic bronchitis"],
            a: 0
        },
        {
            q: "A 42-year-old man, suffering from duodenal ulcer for 20 years, reports heaviness in stomach, eructation with smell of decay, vomiting food eaten the day before, and 'sloshing sound' in epigastrium. What complication is most likely?",
            o: ["Ulcer penetration", "Chronic pancreatitis", "Ulcerative stenosis of the pylorus", "Covered perforation of an ulcer", "Stomach cancer"],
            a: 2
        },
        {
            q: "A 22-year-old pregnant woman (severe condition) has edemas, headache, BP 160/130. She developed seizures, stopped breathing, then foam appeared on lips. What is the diagnosis?",
            o: ["Hypertensive crisis", "Diabetic coma", "Chorea", "Eclampsia", "Epilepsy"],
            a: 3
        },
        {
            q: "A 45-year-old patient has skin rash on trunk/limbs after stress. Plaques with silvery-yellow scales. 'Stearin spot' symptom is positive. What is the diagnosis?",
            o: ["Dermatophytosis", "Eczema", "Lichen ruber planus", "Allergic dermatitis", "Psoriasis"],
            a: 4
        },
        {
            q: "A 23-year-old man swallowed brake fluid. Anuria for 5 days, creatinine 0.569 mmol/L. What treatment tactics should be chosen?",
            o: ["Diuretics", "Antidotal therapy", "Hemodialysis", "Plasmapheresis", "Detoxication therapy"],
            a: 2
        },
        {
            q: "A 19-year-old woman with no menstruations, lethargy, weight loss. Pathological childbirth 1 year ago with blood loss and no lactation. Hypoestrogenemia. What is the diagnosis?",
            o: ["Stein-Leventhal syndrome", "Hypothyroidism", "Astheno-vegetative syndrome", "Genital tuberculosis", "Sheehan syndrome"],
            a: 4
        },
        {
            q: "A 64-year-old patient with sigmoid colon tumor and chronic thrombophlebitis is scheduled for surgery. What is the optimal medicine for DVT prevention?",
            o: ["Phenylinum", "Low-molecular-weight heparin", "Regular heparin", "Acetylsalicylic acid", "Rheopolyglucin"],
            a: 1
        },
        {
            q: "A 40-year-old patient with cough, mucopurulent sputum, fever 37.6°C. Smoker since 17. Auscultation: coarse respirations, diffuse dry crackles. Bronchoscopy: purulent catarrhal endobronchitis. Diagnosis?",
            o: ["Lung cancer", "Bronchial asthma", "Community-acquired pneumonia", "Pulmonary tuberculosis", "Chronic bronchitis"],
            a: 4
        },
        {
            q: "A 5-day-old girl, 3100g. Icteric skin on day 3. Condition satisfactory. Liver +1 cm, spleen '-'. Mother A(II)Rh+, Child 0(I)Rh+. Bilirubin 140 mcmol/L (indirect), direct 0. Diagnosis?",
            o: ["Hemolytic anemia", "Congenital hepatitis", "Biliary atresia", "Hemolytic disease of newborn", "Physiological jaundice"],
            a: 4
        },
        {
            q: "A 57-year-old miner, smoker (40 yrs). Chest pain, dyspnea, blood-streaked sputum. X-ray: triangular shadow in middle lobe of right lung, apex points to lung root. Diagnosis?",
            o: ["Pneumoconiosis", "Tuberculosis of right lung", "Cancer of right lung", "Chronic bronchitis", "Right-sided pleuropneumonia"],
            a: 2
        },
        {
            q: "Factory produces chromium and nickel compounds. Medical committee includes: therapist, dermatologist, traumatologist, ophthalmologist, neurologist, etc. What is the MINIMUM composition required?",
            o: ["Therapist, hematologist, ophthalmologist", "Otolaryngologist, therapist, dermatologist", "Therapist, immunologist, surgeon", "Therapist, dermatologist, traumatologist", "Therapist, ophthalmologist, neurologist"],
            a: 1
        },
        {
            q: "18-year-old patient with skin rash for 5 years. Started after car accident. Papular rash with silvery scales, 'thimble' sign on nails, affected joints. Diagnosis?",
            o: ["Lupus erythematosus", "Rheumatism", "Panaritium", "Onychomycosis", "Psoriasis"],
            a: 4
        },
        {
            q: "Children in Africa diagnosed with kwashiorkor (growth inhibition, edema, mental changes). What food products should be added to diet?",
            o: ["Milk, meat, vegetables", "Fish, vegetables, cereals", "Poultry, fruit, berries", "Vegetables, fruit", "Cereals, fruit, berries"],
            a: 1
        },
        {
            q: "25-year-old man, hematuria after cold exposure. BP 160/110. Urine: proteinuria 3.5 g/L, erythrocytes cover field, hyaline casts. Most informative diagnostic method?",
            o: ["Excretory urography", "Kidney ultrasound", "Cystoscopy", "Tomography", "Kidney biopsy"],
            a: 4
        },
        {
            q: "37-year-old woman received occupational trauma resulting in severe vision impairment. Needs training for new occupation. What type of rehabilitation?",
            o: ["Occupational rehabilitation", "Social rehabilitation", "Psychological rehabilitation", "Medical rehabilitation", "Target rehabilitation"],
            a: 0
        },
        {
            q: "44-year-old man, drinking vodka daily for 6 days. On 3rd night: anxiety, fear, visual hallucinations (horror movie on wall), threats. Diagnosis?",
            o: ["Somatogenic psychosis", "Delirium tremens", "Obsessive-phobic neurosis", "Occupational delirium", "Alcoholic hallucinosis"],
            a: 1
        },
        {
            q: "50-year-old man at paint factory. Weakness, dizziness, pain in back. Skin earth-gray, icteric sclera. Heinz-Ehrlich bodies in blood, methemoglobin 62%. Diagnosis?",
            o: ["Chronic cyanide poisoning", "Acute aniline poisoning", "Acute toluene poisoning", "Acquired hemolytic anemia", "Acute viral hepatitis"],
            a: 1
        },
        {
            q: "18-year-old ate smoked fish and mushrooms. 6 hours later: vision impairment, 'fog', swallowing problems. Dilated pupils, dry mouth. Diagnosis?",
            o: ["Botulism", "Mushroom poisoning", "Salmonellosis", "Fish poisoning", "Staphylococcal intoxication"],
            a: 0
        },
        {
            q: "8-year-old boy had Hepatitis B a year ago. Now complains of fatigue, disturbed sleep, nausea. Liver/spleen +1cm. ALT 220 units. Interpretation?",
            o: ["Recurrence of viral hepatitis B", "Biliary dyskinesia", "Development of hepatic cirrhosis", "Development of chronic hepatitis", "Residual effects of hepatitis B"],
            a: 3
        },
        {
            q: "45-year-old man, duodenal ulcer for 5 years. Weakness, dizziness. Pale skin. Systolic murmur. Hb 88 g/l, Serum Iron 9.5 mcmol/l. Treatment?",
            o: ["Corticosteroids", "Ascorbic acid", "Cyanocobalamin", "Iron preparations, balanced diet", "Red cells transfusion"],
            a: 3
        },
        {
            q: "Woman with polycystic kidney disease. Urine output 2-2.5 L. GFR 34 mL/min, creatinine 84 mcmol/L. What stage of chronic kidney failure?",
            o: ["Polyuric", "Intermittent", "Latent", "Terminal", "Compensated"],
            a: 4
        },
        {
            q: "27-year-old woman, frequent stools with mucus, abdominal pain abating after defecation. Colonoscopy normal. Diagnosis?",
            o: ["Crohn disease", "Irritable bowel syndrome", "Whipple disease", "Chronic non-ulcerative colitis", "Chronic enteritis"],
            a: 1
        },
        {
            q: "46-year-old woman, sharp pain in right subcostal region radiating to shoulder at 3 a.m. Vomiting, fever 39°C. Tense muscles right subcostal. Diagnosis?",
            o: ["Cholelithiasis", "Benign stricture bile duct", "Unstable angina", "Peptic ulcer disease", "Acute cholecystitis"],
            a: 4
        },
        {
            q: "56-year-old woman, disinfector for 19 years. Nausea, bitter taste, heavy right subcostal. Liver enlarged. Bilirubin 40 mcmol/L. Diagnosis?",
            o: ["Chronic cholecystitis", "Chronic pancreatitis", "Occupational toxic hepatitis", "Biliary dyskinesia", "Acute cholecystitis"],
            a: 2
        },
        {
            q: "8-year-old, growth retardation. Height 107cm. 'Doll's face', thin hair, dry skin. High subcutaneous fat on torso. Diagnosis?",
            o: ["Pituitary dwarfism", "Chondrodystrophy", "Craniopharyngioma", "Fanconi syndrome", "Down syndrome"],
            a: 0
        },
        {
            q: "5-year-old child, cough attacks with reprises. Face turns red, veins bulge. Vomiting after cough. Lymphocytes 72%. Diagnosis?",
            o: ["Foreign body", "Pertussis", "Adenovirus", "Pneumonia", "Obstructive bronchitis"],
            a: 1
        },
        {
            q: "Man with urolithiasis and hyperoxaluria. What products to reduce?",
            o: ["Dairy", "Meat, fish", "Fatty foods", "Canned soup", "Sorrel, spinach, tomatoes"],
            a: 4
        },
        {
            q: "Newborn 3980g. Obstetric assistance used. Right hand adducted, rotated, no movement in shoulder/elbow. 'Doll's arm'. Diagnosis?",
            o: ["Dejerine-Klumpke paresis", "Total brachial plexus paresis", "Duchenne-Erb paresis", "Dislocation right shoulder", "Humerus fracture"],
            a: 2
        },
        {
            q: "10-year-old boy, seasonal stuffy nose (spring/autumn). Pale, swollen face. Father has asthma. Rhinoscopy: swollen pale mucosa. Diagnosis?",
            o: ["Acute rhinitis", "Acute maxillary sinusitis", "Acute adenoiditis", "Allergic rhinitis", "Recurrent respiratory disease"],
            a: 3
        },
        {
            q: "40-year-old woman with epilepsy. After psychotrauma, seizures occur every 2-3 minutes without regaining consciousness. Pupils unresponsive. Diagnosis?",
            o: ["Increase in grand mal seizures", "Hysterical attack", "Status epilepticus", "Absence seizure", "Epileptic psychosis"],
            a: 2
        }
    ];

    // --- APP STATE ---
    let activeQuestions = [];
    let userAnswers = {};
    let currentIndex = 0;
    let apiKey = "";

    // --- DOM REFERENCES ---
    const setupView = document.getElementById('setup-view');
    const quizView = document.getElementById('quiz-view');
    const totalCountEl = document.getElementById('total-count');
    const resumeBtn = document.getElementById('resume-btn');
    const quizLengthSelect = document.getElementById('quiz-length');
    const apiKeyInput = document.getElementById('api-key');
    const aiBox = document.getElementById('ai-response-box');
    const aiContent = document.getElementById('ai-content');

    // --- INIT ---
    function init() {
        totalCountEl.textContent = db.length;
        Array.from(quizLengthSelect.options).forEach(opt => {
            if (opt.value !== 'all' && parseInt(opt.value) > db.length) opt.disabled = true;
        });

        // Load API Key
        const savedKey = localStorage.getItem('geminiApiKey');
        if(savedKey) apiKeyInput.value = savedKey;

        // Load Session
        if(localStorage.getItem('medQuizSession')) resumeBtn.classList.remove('hidden');
    }

    // --- CORE QUIZ FUNCTIONS ---
    function startNewQuiz() {
        // Save API Key
        apiKey = apiKeyInput.value.trim();
        if(apiKey) localStorage.setItem('geminiApiKey', apiKey);

        const val = quizLengthSelect.value;
        let count = (val === 'all') ? db.length : parseInt(val);
        
        activeQuestions = [...db].sort(() => 0.5 - Math.random()).slice(0, count);
        userAnswers = {};
        currentIndex = 0;
        
        saveSession();
        showQuiz();
    }

    function resumeQuiz() {
        // Load Key
        apiKey = localStorage.getItem('geminiApiKey') || "";
        
        const data = JSON.parse(localStorage.getItem('medQuizSession'));
        if(data) {
            activeQuestions = data.questions;
            userAnswers = data.answers;
            currentIndex = data.currentIndex;
            showQuiz();
        }
    }

    function showQuiz() {
        setupView.classList.add('hidden');
        quizView.classList.remove('hidden');
        renderQuestion();
    }

    function exitToMenu() {
        if(confirm("Exit?")) {
            quizView.classList.add('hidden');
            setupView.classList.remove('hidden');
            init();
        }
    }

    function saveSession() {
        localStorage.setItem('medQuizSession', JSON.stringify({
            questions: activeQuestions, answers: userAnswers, currentIndex: currentIndex
        }));
    }

    function nav(dir) {
        const next = currentIndex + dir;
        if(next >= 0 && next < activeQuestions.length) {
            currentIndex = next;
            // Hide AI box when changing questions
            aiBox.style.display = 'none';
            saveSession();
            renderQuestion();
        }
    }

    function handleOptionClick(idx) {
        userAnswers[currentIndex] = idx;
        saveSession();
        renderQuestion();
    }

    function renderQuestion() {
        const q = activeQuestions[currentIndex];
        const userAnswer = userAnswers[currentIndex];
        const isAnswered = userAnswer !== undefined;

        document.getElementById('progress-text').textContent = `${currentIndex + 1} / ${activeQuestions.length}`;
        document.getElementById('progress-fill').style.width = `${((currentIndex + 1) / activeQuestions.length) * 100}%`;

        // Score
        let correct = 0, answered = 0;
        activeQuestions.forEach((q, i) => {
            if(userAnswers[i] !== undefined) {
                answered++;
                if(userAnswers[i] === q.a) correct++;
            }
        });
        document.getElementById('score-text').textContent = answered === 0 ? "0%" : `${Math.round((correct/answered)*100)}%`;

        document.getElementById('q-text').textContent = q.q;

        const container = document.getElementById('options-container');
        container.innerHTML = '';

        q.o.forEach((optText, idx) => {
            const btn = document.createElement('button');
            btn.className = 'option-btn';
            btn.textContent = optText;

            if(isAnswered) {
                if(idx === q.a) btn.classList.add('correct');
                if(idx === userAnswer) {
                    if(idx !== q.a) btn.classList.add('wrong');
                    else btn.classList.add('selected');
                }
            }
            btn.onclick = () => handleOptionClick(idx);
            container.appendChild(btn);
        });

        document.getElementById('prev-btn').disabled = (currentIndex === 0);
        document.getElementById('next-btn').disabled = (currentIndex === activeQuestions.length - 1);
        
        // Hide AI box on render
        if(aiBox.style.display !== 'block') aiBox.style.display = 'none';
    }

    // --- AI LOGIC ---
    async function askAI() {
        if(!apiKey) {
            alert("Please enter a Google Gemini API Key in the setup screen to use this feature.");
            return;
        }

        const qData = activeQuestions[currentIndex];
        const correctText = qData.o[qData.a];
        
        // Show box with loading
        aiBox.style.display = 'block';
        aiContent.innerHTML = "Thinking... Analyzing medical context...";
        
        const prompt = `
            You are an expert medical board exam tutor.
            Question: "${qData.q}"
            Options: ${JSON.stringify(qData.o)}
            Correct Answer: "${correctText}"
            
            Task:
            1. Explain concisely why the correct answer is right.
            2. Briefly explain why the other options are wrong (differential diagnosis).
            3. Keep it simple and educational. Format with bullet points.
        `;

        try {
            const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${apiKey}`, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
            });

            const data = await response.json();
            
            if(data.error) {
                aiContent.innerHTML = `<span style="color:red">Error: ${data.error.message}</span>`;
            } else {
                // Basic Markdown formatting cleanup
                let text = data.candidates[0].content.parts[0].text;
                text = text.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>'); // Bold
                text = text.replace(/\n/g, '<br>'); // Newlines
                aiContent.innerHTML = text;
            }
        } catch (e) {
            aiContent.innerHTML = `<span style="color:red">Connection Error. Please check your internet or API key.</span>`;
        }
    }

    init();
</script>
</body>
</html>
