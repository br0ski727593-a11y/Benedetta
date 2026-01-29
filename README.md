<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medical Board Quiz App</title>
    <style>
        :root {
            --primary: #2563eb;
            --primary-hover: #1d4ed8;
            --surface: #ffffff;
            --background: #f3f4f6;
            --text: #1f2937;
            --success: #10b981;
            --error: #ef4444;
            --border: #e5e7eb;
        }

        body {
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            background-color: var(--background);
            color: var(--text);
            display: flex;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .app-container {
            background: var(--surface);
            width: 100%;
            max-width: 700px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            padding: 2rem;
            display: flex;
            flex-direction: column;
            gap: 1.5rem;
            margin-top: 2rem;
        }

        /* Setup Screen */
        .setup-screen {
            text-align: center;
            padding: 1rem 0;
        }

        .input-group {
            margin: 2rem 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 0.8rem;
        }

        select, input[type="number"] {
            padding: 0.8rem;
            font-size: 1.1rem;
            border: 2px solid var(--border);
            border-radius: 8px;
            width: 200px;
            text-align: center;
            background-color: #fff;
        }

        /* Quiz UI */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid var(--border);
            padding-bottom: 1rem;
        }

        .progress-container {
            width: 100%;
            background-color: var(--border);
            height: 8px;
            border-radius: 4px;
            overflow: hidden;
        }

        .progress-bar {
            height: 100%;
            background-color: var(--primary);
            transition: width 0.3s ease;
        }

        .question-text {
            font-size: 1.15rem;
            line-height: 1.6;
            font-weight: 500;
        }

        .options-grid {
            display: flex;
            flex-direction: column;
            gap: 0.8rem;
        }

        .option-btn {
            background: #f9fafb;
            border: 2px solid var(--border);
            padding: 1rem;
            border-radius: 8px;
            text-align: left;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 1rem;
            position: relative;
        }

        .option-btn:hover:not(:disabled) {
            border-color: var(--primary);
            background: #eff6ff;
        }

        /* Review Mode Colors */
        .option-btn.correct {
            border-color: var(--success);
            background: #d1fae5;
            color: #065f46;
        }

        .option-btn.wrong {
            border-color: var(--error);
            background: #fee2e2;
            color: #991b1b;
        }

        .option-btn.selected {
            border-color: var(--primary);
            background: #eff6ff;
            font-weight: 600;
        }

        /* Buttons */
        .btn {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 0.75rem 1.5rem;
            border-radius: 6px;
            cursor: pointer;
            font-weight: 600;
            font-size: 1rem;
            transition: background 0.2s;
        }

        .btn:hover {
            background-color: var(--primary-hover);
        }

        .btn:disabled {
            background-color: #9ca3af;
            cursor: not-allowed;
        }

        .btn-outline {
            background-color: transparent;
            color: #6b7280;
            border: none;
            cursor: pointer;
            text-decoration: underline;
            font-size: 0.9rem;
        }

        .controls {
            display: flex;
            justify-content: space-between;
            margin-top: 1rem;
        }

        .hidden { display: none !important; }
    </style>
</head>
<body>

<div class="app-container">
    
    <div id="setup-view" class="setup-screen">
        <h1>Medical Board Quiz</h1>
        <p>Database size: <span id="total-available" style="font-weight:bold;">0</span> questions.</p>
        
        <div class="input-group">
            <label for="quiz-length">Select number of questions:</label>
            <select id="quiz-length">
                <option value="10">10 Questions</option>
                <option value="25">25 Questions</option>
                <option value="50">50 Questions</option>
                <option value="100">100 Questions</option>
                <option value="all">All Available</option>
            </select>
        </div>

        <button class="btn" onclick="startNewQuiz()">Start New Quiz</button>
        
        <div style="margin-top: 20px;">
            <button class="btn-outline hidden" id="resume-btn" onclick="resumeQuiz()">Resume Previous Session</button>
        </div>
    </div>

    <div id="quiz-view" class="hidden">
        <div class="header">
            <div>
                <h3 style="margin:0;">Quiz In Progress</h3>
                <div id="score-display" style="font-size: 0.9rem; color: #6b7280;">Score: 0/0</div>
            </div>
            <button class="btn-outline" onclick="exitToSetup()">Exit to Menu</button>
        </div>

        <div class="progress-container">
            <div class="progress-bar" id="progress-bar" style="width: 0%"></div>
        </div>

        <div id="quiz-content">
            <div class="question-text" id="question-text">Loading...</div>
            <div class="options-grid" id="options-container"></div>
        </div>

        <div class="controls">
            <button class="btn" id="prev-btn" onclick="changeQuestion(-1)">Previous</button>
            <span id="question-number" style="align-self: center; font-weight: bold; color: #6b7280;"></span>
            <button class="btn" id="next-btn" onclick="changeQuestion(1)">Next</button>
        </div>
    </div>

</div>

<script>
    // --- QUESTION DATABASE (First 100 Questions) ---
    const fullQuestionPool = [
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
        },
        {
            q: "51-year-old man hit by car. Pain in pelvis. Larrey's and Gabai's signs positive. Cannot lift extended leg ('heel glued down'). Diagnosis?",
            o: ["Fracture of superior pubic ramus", "Fractures of cotyloid cavity", "Pubic symphysis fracture", "Superior iliac spine fracture", "Ischium fracture"],
            a: 0
        },
        {
            q: "Postparturient woman, day 5. Pain in left mammary gland, fever 38.1°C. Nipple edematous/fissured. Prevention measures?",
            o: ["Constant expression", "Feeding on demand, expression, nipple care", "Feeding through overlay", "Stop breastfeeding", "Feeding on schedule"],
            a: 1
        },
        {
            q: "18-year-old stung by bee. Dyspnea, edema of face/neck. BP 75/50. Diagnosis?",
            o: ["Urticaria", "Quincke's edema", "Status asthmaticus", "Hypotonic crisis", "Anaphylactic shock"],
            a: 4
        },
        {
            q: "20-year-old, skin rash. Light brown spots merging. Balzer test positive. Diagnosis?",
            o: ["Microbial eczema", "Pityriasis rosea", "Secondary syphilis", "Tinea versicolor", "Parapsoriasis"],
            a: 3
        },
        {
            q: "20-year-old woman, seasonal rhinitis/conjunctivitis (Aug-Sept). Hypersensitivity to Ambrosia. Which antibody class?",
            o: ["IgD", "IgA", "IgG", "IgM", "IgE"],
            a: 4
        },
        {
            q: "Man with sharp retrosternal pain radiating to left arm. Lost consciousness, no pulse, dilated pupils. Diagnosis?",
            o: ["Cerebral circulation disorder", "Coma", "Agonal state", "Clinical death", "Heart attack"],
            a: 3
        },
        {
            q: "32-year-old man, cold exposure. Fever, cough. Dull percussion under right shoulder blade, moist crackles. Leukocytes 13.2. Diagnosis?",
            o: ["Tuberculosis", "Right-sided focal pneumonia", "Acute bronchitis", "Chronic bronchitis exacerbation", "Lung cancer"],
            a: 1
        },
        {
            q: "32-year-old woman, dyspnea, fever 39°C. Sputum: acid-fast bacteria. X-ray: miliary focal shadows throughout lungs. Diagnosis?",
            o: ["Focal tuberculosis", "Caseous pneumonia", "Sarcoidosis", "Chronic disseminated tuberculosis", "Miliary pulmonary tuberculosis"],
            a: 4
        },
        {
            q: "45-year-old man, vomiting blood, weight loss. Esophagoscopy: cauliflower-shaped growth in abdominal esophagus. Diagnosis?",
            o: ["Esophageal tumor", "Esophageal achalasia", "Abdominal esophagitis", "Esophageal diverticulum", "Barrett esophagus"],
            a: 0
        },
        {
            q: "16-year-old bitten by stray dog on face. Tactics for rabies prevention?",
            o: ["Immunoglobulin + ribavirin", "Anti-rabies immunoglobulin + vaccine", "Vaccine + immunoglobulin", "Immunoglobulin + ribavirin", "Vaccine + ribavirin"],
            a: 1
        },
        {
            q: "35-year-old woman, left arm pain/weakness, pulse absent on left arm. No pulsation left carotid. Murmur right carotid. Diagnosis?",
            o: ["Arterial thrombosis", "Neurological amyotrophy", "Dermatomyositis", "Systemic lupus erythematosus", "Obliterating aortoarteritis"],
            a: 4
        },
        {
            q: "Boy with foreign body under nail. 3 days later: sharp throbbing pain, pus, fever. Diagnosis?",
            o: ["Erysipelas", "Paronychia", "Subungual panaritium", "Erysipeloid", "Abscess"],
            a: 2
        },
        {
            q: "3-year-old, cough/runny nose. Dyspnea. Bandbox resonance. Diffuse wet and dry wheezes. Diagnosis?",
            o: ["Bilateral bronchopneumonia", "Acute bronchiolitis", "Bronchial asthma", "Acute obstructive bronchitis", "Stenosing laryngotracheitis"],
            a: 3
        },
        {
            q: "44-year-old woman, weakness, palpitations. Thyroid enlarged, dense, painful. T4 low, TSH high, Antibodies high. Diagnosis?",
            o: ["Autoimmune thyroiditis", "Diffuse toxic goiter", "Subacute thyroiditis", "Diffuse euthyroid goiter", "Thyroid cancer"],
            a: 0
        },
        {
            q: "14-year-old girl, short stature, webbed neck, no puberty. Karyotype 45,X0. Diagnosis?",
            o: ["Klinefelter syndrome", "Turner syndrome", "Edwards syndrome", "Patau syndrome", "Down syndrome"],
            a: 1
        },
        {
            q: "78-year-old woman, lumbar pain. Mobility limited. L2 vertebra tender. Ca 2.35. ESR 5. Diagnosis?",
            o: ["Sciatica", "Multiple myeloma", "Secondary hyperparathyroidism", "Osteoporosis with fracture", "Amyloidosis"],
            a: 3
        },
        {
            q: "58-year-old man, ischemic stroke 10 days ago. Atrial fibrillation. Prevention of secondary stroke?",
            o: ["Aspirin", "Lipid-lowering agents", "Oral anticoagulants", "Cerebral blood flow drugs", "Clopidogrel"],
            a: 2
        },
        {
            q: "Premature baby (34 wks). 4 hours old: tachypnea, seesaw respiration, expiratory murmurs. X-ray: air bronchogram, nodose-reticular pattern. Diagnosis?",
            o: ["Meconium aspiration", "Birth injury", "Neonatal pneumonia", "Pulmonary atelectasis", "Hyaline membrane disease"],
            a: 4
        },
        {
            q: "Lighting fixtures in classrooms. Which type is most hygienically acceptable?",
            o: ["Direct lighting", "Reflected lighting", "Combined lighting", "Semi-reflected lighting", "Scattered lighting"],
            a: 1
        },
        {
            q: "45-year-old farmer. Acute fever, pain in calf muscles, jaundice, dark urine, oliguria. Diagnosis?",
            o: ["Leptospirosis", "Trichinosis", "Brucellosis", "Viral hepatitis", "Pseudotuberculosis"],
            a: 0
        },
        {
            q: "In newborn: cough attacks after eating, pneumonia. 1.5 months later: more pneumonia, cough when on left side. Hypotrophy. Diagnosis?",
            o: ["Posthypoxic encephalopathy", "Tracheobronchomalacia", "Mucoviscidosis", "Hernia of esophageal opening", "Tracheoesophageal fistula"],
            a: 4
        },
        {
            q: "Study: excessive weight in ischemic heart disease among working age males (40+). Overweight men had more disease. Type of study?",
            o: ["Case report", "Experimental study", "Case series report", "Cohort study", "Case-control study"],
            a: 3
        },
        {
            q: "29-year-old woman, infertility, irregular cycle. 160cm, 91kg, hair on face/thighs. Ovaries enlarged/dense. Diagnosis?",
            o: ["Sclerocystic ovary syndrome", "Premenstrual syndrome", "Adrenogenital syndrome", "Chronic bilateral adnexitis", "Ovarian androblastoma"],
            a: 0
        },
        {
            q: "29-year-old: painless ulcer on lip for 3 weeks. Now edema. Ulcer 2.5cm, 'old lard' bottom, cartilage infiltrate. First test?",
            o: ["Skin scraping for mycosis", "Smear for acantholytic cells", "Treponema pallidum test", "Eosinophil count", "Bacterial culture"],
            a: 2
        },
        {
            q: "47-year-old man, acute phlegmonous laryngitis -> decompensated stenosis. Dyspnea, cold sweat, BP low. Urgent treatment?",
            o: ["Hyposensitization/broncholytics", "Oxygen therapy", "Dehydrating agents", "Tracheostomy", "Glucocorticoids"],
            a: 3
        },
        {
            q: "25-year-old man, pain in lower abdomen/right lumbar 1 hr ago. Temp 38.2C. Aure-Rozanov and Gabay positive. Diagnosis?",
            o: ["Intestinal obstruction", "Acute cholecystitis", "Acute appendicitis", "Right-sided renal colic", "Cecal tumor"],
            a: 2
        },
        {
            q: "30-year-old man, 2 months post-op for humerus fracture. Fistula with pus, red area, fluctuation. X-ray: destruction/sequestra. Diagnosis?",
            o: ["Posttraumatic osteomyelitis", "Posttraumatic phlegmon", "Hematogenous osteomyelitis", "Wound suppuration", "Suture sinus"],
            a: 0
        },
        {
            q: "Industrial microclimate: Air 35C, Radiant 30C, Humidity 50%, Air flow 0.01 m/s. Main heat transfer type?",
            o: ["Radiation", "Convection", "Radiation", "Evaporation", "Conduction"],
            a: 3
        },
        {
            q: "Who can examine a dead body at accident scene if forensic expert is unavailable?",
            o: ["Only pathologist", "Only surgeon/anesthesiologist", "Any doctor", "Only therapist/pathologist", "Only family doctor/pathologist"],
            a: 2
        },
        {
            q: "70-year-old woman, memory loss (current events), forgetting way home. Past memory ok. Semantic/amnestic aphasia. History: diabetes. Diagnosis?",
            o: ["Vascular dementia", "Involutional depression", "Encephalitis", "Brain tumor", "Alzheimer's disease"],
            a: 4
        },
        {
            q: "65-year-old man, cough when eating liquid food. 3 months ago: esophageal carcinoma (upper third), radiation therapy. Complication?",
            o: ["Perforation gastric cardia", "Lung abscess rupture", "Spontaneous pneumothorax", "Tracheal stenosis", "Tracheoesophageal fistula"],
            a: 4
        },
        {
            q: "Planner designs heating for preschool. Which room needs highest air temperature?",
            o: ["Common room", "Game room (nursery)", "Bedroom (nursery)", "Bedroom (preschool)", "Gymnasium"],
            a: 1
        },
        {
            q: "45-year-old woman, weakness, gray hair, peeling nails. History: uterine fibromyoma. Hb 76 g/L, Color Index 0.7, Anisocytosis. Anemia type?",
            o: ["Hypoplastic", "B12-deficiency", "Iron-deficiency", "Minkowski-Chauffard", "Autoimmune hemolytic"],
            a: 2
        },
        {
            q: "Woman with focal encephalitis history. 'Buildings are small/distorted, trees upside down, people tall'. Syndrome?",
            o: ["Derealization", "Oneiric", "Hallucinatory", "Depersonalization", "Cenestopathic"],
            a: 0
        },
        {
            q: "32-year-old woman, sudden headache, unconscious. Sopor, meningeal syndrome. Bloody CSF, pressure 260. Diagnosis?",
            o: ["Meningioma", "Meningoencephalitis", "Traumatic brain injury", "Ischemic stroke", "Aneurysm rupture/subarachnoid hemorrhage"],
            a: 4
        },
        {
            q: "12-year-old girl, fever 38.5C, rash. Hepatosplenomegaly. Punctate/maculopapular rash on flexor surfaces/trunk. 'Gloves/Socks' coloring. Diagnosis?",
            o: ["Scarlet fever", "Pseudotuberculosis", "Measles", "Chickenpox", "Infectious mononucleosis"],
            a: 1
        },
        {
            q: "4-month-old boy, pneumocystic pneumonia. HIV antibodies in umbilical blood. PCR positive at 1 & 3 months. Diagnosis?",
            o: ["HIV/AIDS", "Adenovirus", "Pneumonia", "Infectious mononucleosis", "Tuberculosis"],
            a: 0
        },
        {
            q: "27-year-old man, fatigue, heavy left subcostal. Spleen/Liver enlarged. Leukocytes 100x10^9/L, Basophils 7%, Eosinophils 5%, Juvenile 16%. Diagnosis?",
            o: ["Hepatic cirrhosis", "Acute leukemia", "Chronic myelogenous leukemia", "Erythremia", "Chronic lymphocytic leukemia"],
            a: 2
        },
        {
            q: "35-year-old polisher. Cold blue fingertips, 'mask' face, pursed mouth, thick skin. Lung crackles. ESR 45. Diagnosis?",
            o: ["Myxedema", "Obliterating endarteritis", "Vibration disease", "Raynaud's disease", "Systemic scleroderma"],
            a: 4
        },
        {
            q: "Woman at pig farm. Chills, fever 39.9C, leg muscle pain, nosebleeds. Face hyperemic, scleritis. Oliguria. Diagnosis?",
            o: ["Hemorrhagic fever w/ renal syndrome", "Viral hepatitis", "Influenza", "Leptospirosis", "Yersiniosis"],
            a: 3
        },
        {
            q: "Secundipara, bloody discharge at delivery onset. Cervix 6cm, internal os obstructed by spongy tissue by 1/3. Tactics?",
            o: ["Cesarean section", "Stimulation of labor", "Hemostatic therapy", "Termination of pregnancy", "Amniotomy"],
            a: 4
        },
        {
            q: "7-day-old girl, vomiting, hypotension, earthy skin. Enlarged clitoris, incomplete vulvar union. Hyperkalemia, hyponatremia. Diagnosis?",
            o: ["Turner syndrome", "Hermaphroditism", "Adrenogenital (salt-wasting)", "Adrenogenital (simple-virilizing)", "Adrenogenital (hypertensive)"],
            a: 2
        },
        {
            q: "Forensic exam of baby: 3500g, 50cm. Umbilical cord moist/shiny. Lung float tests positive. Conclusion?",
            o: ["Primary atelectasis", "Born dead", "Secondary atelectasis", "Hyaline membrane disease", "Born alive"],
            a: 4
        },
        {
            q: "17-year-old girl, 172cm/40kg. Thinks she's overweight. Strict diet, exercise, binds waist. Vomiting. Diagnosis?",
            o: ["Anorexia", "Stress response", "Bulimia", "Depression", "Personality disorder"],
            a: 0
        },
        {
            q: "38-year-old woman, glomerulonephritis 20 yrs. Hypertension refractory to Losartan+Lercanidipine. Add what?",
            o: ["Urapidil", "Lisinopril", "Torasemide", "Bisoprolol", "Doxazosin"],
            a: 2
        },
        {
            q: "30-year-old ate mushrooms. Nausea, anuria. Creatinine 700, Urea 32. Treatment?",
            o: ["Hemodialysis", "Antidote therapy", "Detoxification therapy", "Diuretics", "Peritoneal dialysis"],
            a: 0
        },
        {
            q: "Man with acute fever 39.9C, headache, eye pain, body aches. 2 nosebleeds. Diagnosis?",
            o: ["Parainfluenza", "Adenovirus", "Respiratory syncytial", "Influenza", "Enterovirus"],
            a: 3
        },
        {
            q: "Casting worker (12 yrs). Heavy metals/CO exposure. Abdominal pain, constipation. Urine: Aminolevulinic acid. Intoxication by?",
            o: ["Carbon monoxide", "Zinc", "Nitric oxide", "Lead", "Tin"],
            a: 3
        },
        {
            q: "Newborn 32 wks, 1700g. Resp distress. Previous child died of RDS. X-ray: reticulonodular pattern. Diagnosis?",
            o: ["Pulmonary atelectasis", "Diaphragmatic hernia", "Edematous hemorrhagic syndrome", "Intrauterine pneumonia", "Hyaline membrane syndrome"],
            a: 4
        },
        {
            q: "2-year-old, frequent resp diseases, malabsorption. Mucoviscidosis suspected. Confirm with?",
            o: ["Immunogram", "Sweat chloride test", "Chest X-ray", "Bronchoscopy", "Karyotyping"],
            a: 1
        },
        {
            q: "Gynecology unit: 6500 women, 102000 bed-days total. What indicator?",
            o: ["Average length of stay", "Planned bed occupancy", "Bed turnover", "Average bed occupancy", "Number of beds"],
            a: 0
        },
        {
            q: "Cadmium in river water 8-10x limit. Population disease?",
            o: ["Minamata", "Itai-Itai", "Yusho", "Kashin-Beck", "Prasad"],
            a: 1
        },
        {
            q: "Normative document assisting practitioner/patient decisions for specific diseases?",
            o: ["Medical care certificate", "Clinical practice guidelines", "Standard of medical care", "Accreditation", "Conformity certificate"],
            a: 1
        },
        {
            q: "Baby fed milk formula (well water). Cyanosis, nausea, vomiting. Cardiopulmonary failure. Diagnosis?",
            o: ["Staphylococcal", "Nitrate-nitrite intoxication", "Foodborne toxicoinfection", "Pesticides", "Heavy metals"],
            a: 1
        },
        {
            q: "Newborn: round red formation suprapubic. Urine pulses from two orifices. Anomaly?",
            o: ["Urachal cyst", "Bladder exstrophy", "Vesico-umbilical fistula", "Bladder agenesis", "Bladder diverticulum"],
            a: 1
        },
        {
            q: "32-year-old, obesity (abdomen/shoulders). Pale pink skin, male hair pattern. Sugar 4.9. Diagnosis?",
            o: ["Secondary hypo-ovarian obesity", "Secondary neuroendocrine obesity", "Primary alimentary (gynoid)", "Secondary hypothalamic obesity", "Primary alimentary (android)"],
            a: 4
        },
        {
            q: "Pregnant 28yo. History: cervical rupture, 2 miscarriages (12/14 wks). Cervix scarred, canal gaping. Diagnosis?",
            o: ["Threatened abortion", "Isthmico-cervical insufficiency", "Incipient abortion", "Cervical pregnancy", "Cervical hysteromyoma"],
            a: 1
        },
        {
            q: "Water quality: Turbidity 1.5, Odor 3 pts, Taste 2 pts (metallic), Color light yellow. Violation?",
            o: ["Temperature", "Chromaticity", "Taste", "Turbidity", "Odor"],
            a: 4
        },
        {
            q: "Dead body exam: Gray-yellow triangular spots in eye corners. Phenomenon?",
            o: ["Idiomuscular tumor", "Kayser-Fleischer ring", "Larcher spots", "Louis sign", "Beloglazov sign"],
            a: 2
        },
        {
            q: "Lumbar puncture in newborn (birth injury). Bloody CSF. Hemorrhage type?",
            o: ["Supratentorial", "Subtentorial", "Epidural", "Subarachnoid", "Cephalohematoma"],
            a: 3
        },
        {
            q: "13-year-old girl, profuse bleeding 10 days. Irregular cycle. No pathology on exam. Diagnosis?",
            o: ["Werlhof's disease", "Endometrial polyp", "Juvenile uterine bleeding", "External genital injury", "Adenomyosis"],
            a: 2
        },
        {
            q: "24-year-old woman, post-op hypothyroidism. Palpitations, irritability. On Levothyroxine 150mcg. Tactics?",
            o: ["Beta-blockers", "Reduce dose", "Sulfonylurea", "Increase dose", "Add Mercazolil"],
            a: 1
        },
        {
            q: "18-year-old woman, lower abdominal pain, purulent discharge. Acute adnexitis. Bacteria: diplococci intra/extracellular. Agent?",
            o: ["Colibacillus", "Trichomonad", "Gonococcus", "Chlamydia", "Staphylococcus"],
            a: 2
        },
        {
            q: "Grounds for forensic medical examination?",
            o: ["Family doctor referral", "Investigative/Court resolution", "Head doctor referral", "Investigative referral (duplicate)", "Relative wishes"],
            a: 1
        },
        {
            q: "Patient with disturbed gait, forced laughter, mask-like face. Chemical exposure history. Factor?",
            o: ["Manganese", "Cadmium", "Lead", "Benzene", "Mercury"],
            a: 0
        },
        {
            q: "Newborn day 3. Edema/hematoma left supraclavicular. Arm pressed to torso. Diagnosis?",
            o: ["Displaced clavicle fracture", "Osteomyelitis", "Erb's palsy", "Phlegmon", "Non-displaced clavicle fracture"],
            a: 0
        },
        {
            q: "Man, sharp right chest pain, dyspnea. Acrocyanosis. Subcutaneous emphysema. Bandbox resonance right. Diagnosis?",
            o: ["Pneumonia", "Myocardial infarction", "Lung infarction", "Spontaneous pneumothorax", "Exudative pleurisy"],
            a: 3
        },
        {
            q: "38-year-old woman, dry peeling skin. Papular rash extensor knees/elbows. Wax-colored nodules hair follicles. Deficiency?",
            o: ["Thiamine", "Retinol", "Riboflavin", "Pyridoxine", "Ascorbic acid"],
            a: 1
        },
        {
            q: "56-year-old woman, biliary cirrhosis. Itching, nausea. Bilirubin 142. Treatment?",
            o: ["Ursodeoxycholic acid", "Livolin forte", "Sirepar", "Essentiale forte", "Allochol"],
            a: 0
        },
        {
            q: "27-year-old woman, foul discharge, fever. Abortion 1 week ago. Uterus painful/enlarged. Diagnosis?",
            o: ["Acute respiratory disease", "Enterocolitis", "Appendicitis", "Postabortal endometritis", "Salpingoophoritis"],
            a: 3
        }
    ];

    // --- APP LOGIC ---
    let activeQuestions = [];
    let userAnswers = {};
    let currentQuestionIndex = 0;

    const setupView = document.getElementById('setup-view');
    const quizView = document.getElementById('quiz-view');
    const totalAvailableSpan = document.getElementById('total-available');
    const quizLengthSelect = document.getElementById('quiz-length');
    const resumeBtn = document.getElementById('resume-btn');

    function init() {
        // Display total questions available in DB
        totalAvailableSpan.textContent = fullQuestionPool.length;
        
        // Update max options in select based on actual DB size
        const count = fullQuestionPool.length;
        // Dynamically hide options that are larger than DB
        Array.from(quizLengthSelect.options).forEach(opt => {
            if (opt.value !== 'all' && parseInt(opt.value) > count) {
                opt.disabled = true;
            }
        });

        checkResume();
    }

    function checkResume() {
        const saved = localStorage.getItem('medQuizSession');
        if (saved) {
            resumeBtn.classList.remove('hidden');
        } else {
            resumeBtn.classList.add('hidden');
        }
    }

    function startNewQuiz() {
        const val = quizLengthSelect.value;
        let count = 0;
        if (val === 'all') count = fullQuestionPool.length;
        else count = parseInt(val);

        if (confirm("Start new quiz? Unsaved progress will be lost.")) {
            // Shuffle
            const shuffled = [...fullQuestionPool].sort(() => 0.5 - Math.random());
            activeQuestions = shuffled.slice(0, count);
            
            userAnswers = {};
            currentQuestionIndex = 0;
            saveSession();
            showQuiz();
        }
    }

    function resumeQuiz() {
        const saved = localStorage.getItem('medQuizSession');
        if (saved) {
            const data = JSON.parse(saved);
            activeQuestions = data.q;
            userAnswers = data.a;
            currentQuestionIndex = data.i;
            showQuiz();
        }
    }

    function exitToSetup() {
        if (confirm("Return to main menu?")) {
            quizView.classList.add('hidden');
            setupView.classList.remove('hidden');
            checkResume();
        }
    }

    function showQuiz() {
        setupView.classList.add('hidden');
        quizView.classList.remove('hidden');
        renderQuestion();
    }

    function saveSession() {
        localStorage.setItem('medQuizSession', JSON.stringify({
            q: activeQuestions,
            a: userAnswers,
            i: currentQuestionIndex
        }));
    }

    function renderQuestion() {
        const qData = activeQuestions[currentQuestionIndex];
        const userAnswer = userAnswers[currentQuestionIndex];
        const isAnswered = userAnswer !== undefined;

        document.getElementById('question-text').textContent = `${currentQuestionIndex + 1}. ${qData.q}`;
        document.getElementById('question-number').textContent = `${currentQuestionIndex + 1} / ${activeQuestions.length}`;

        const container = document.getElementById('options-container');
        container.innerHTML = '';

        qData.o.forEach((opt, idx) => {
            const btn = document.createElement('button');
            btn.className = 'option-btn';
            btn.textContent = opt;

            if (isAnswered) {
                if (idx === qData.a) btn.classList.add('correct');
                if (idx === userAnswer) {
                    if (idx !== qData.a) btn.classList.add('wrong');
                    else btn.classList.add('selected');
                }
            }

            btn.onclick = () => {
                userAnswers[currentQuestionIndex] = idx;
                saveSession();
                renderQuestion();
            };

            container.appendChild(btn);
        });

        // Navigation state
        document.getElementById('prev-btn').disabled = (currentQuestionIndex === 0);
        document.getElementById('next-btn').disabled = (currentQuestionIndex === activeQuestions.length - 1);

        // Progress Bar
        const pct = ((currentQuestionIndex + 1) / activeQuestions.length) * 100;
        document.getElementById('progress-bar').style.width = `${pct}%`;

        // Score
        let correct = 0;
        Object.keys(userAnswers).forEach(k => {
            if (userAnswers[k] === activeQuestions[k].a) correct++;
        });
        document.getElementById('score-display').textContent = `Score: ${correct}`;
    }

    function changeQuestion(delta) {
        const next = currentQuestionIndex + delta;
        if (next >= 0 && next < activeQuestions.length) {
            currentQuestionIndex = next;
            saveSession();
            renderQuestion();
        }
    }

    init();
</script>
</body>
</html>
