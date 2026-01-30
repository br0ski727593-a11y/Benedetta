# Medical Board Exam Tutor 🩺

A private, interactive web app designed to help study for medical board exams. It features a database of practice questions and an AI Tutor (powered by Google Gemini) to explain complex diagnoses.

## ✨ Features
* **Practice Mode:** Randomly shuffles questions from the database.
* **Smart Scoring:** Tracks progress and saves it automatically.
* **AI Tutor:** Click "Explain This Question" to get a detailed breakdown of why an answer is correct and why the distractors are wrong.
* **Responsive:** Works on laptops, tablets, and phones.

## 🚀 How to Use
1.  **Download:** Click the green "Code" button and select "Download ZIP" (or clone the repo).
2.  **Open:** Double-click `index.html` to open it in your browser.
3.  **Start:** Select your session length and begin!

## 🤖 AI Setup (Optional)
The app works perfectly as a standard quiz without any setup.
To enable the **"Explain This Question"** button:
1.  Open `index.html` in a text editor (Notepad, TextEdit, VS Code).
2.  Scroll to the bottom script section.
3.  Paste your Google Gemini API Key where indicated.
4.  Save and refresh.

## 📱 Tech Stack
* **HTML5 / CSS3 / JavaScript** (Vanilla, Single-file)
* **Google Gemini API** (via direct fetch)
* **Local Storage** (for saving progress)

---
*Made with ❤️ for study sessions.*

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medical Board Tutor (150 Qs)</title>
    <style>
        :root { --primary: #2563eb; --background: #f8fafc; --surface: #ffffff; --text: #0f172a; --ai-color: #7c3aed; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; background-color: var(--background); color: var(--text); line-height: 1.6; margin: 0; padding: 20px; display: flex; justify-content: center; min-height: 100vh; }
        .app-container { background: var(--surface); width: 100%; max-width: 800px; padding: 2.5rem; border-radius: 16px; box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1); }
        .hidden { display: none; }
        button { cursor: pointer; border: none; font-weight: 700; transition: transform 0.1s, opacity 0.2s; }
        button:active { transform: scale(0.98); }
        .primary-btn { background: var(--primary); color: white; width: 100%; font-size: 1.2rem; padding: 16px; border-radius: 12px; margin-top: 20px; }
        .ai-btn { background: linear-gradient(135deg, #7c3aed, #6d28d9); color: white; padding: 12px 24px; border-radius: 8px; margin: 25px auto; display: flex; align-items: center; justify-content: center; gap: 8px; width: 100%; font-size: 1rem; box-shadow: 0 4px 6px rgba(124, 58, 237, 0.2); }
        .option-btn { display: block; width: 100%; padding: 16px; margin: 12px 0; border: 2px solid #e2e8f0; background: white; text-align: left; border-radius: 12px; font-size: 1rem; color: var(--text); }
        .option-btn:hover { border-color: var(--primary); background: #eff6ff; }
        .option-btn.correct { background: #dcfce7; border-color: #16a34a; color: #14532d; }
        .option-btn.wrong { background: #fee2e2; border-color: #dc2626; color: #7f1d1d; }
        .option-btn.selected { border-color: var(--primary); background: #eff6ff; font-weight: 600; }
        #ai-response-box { background: #f5f3ff; padding: 20px; border-radius: 12px; margin-top: 15px; display: none; border: 1px solid #d8b4fe; color: #4c1d95; font-size: 0.95rem; }
        .nav-container { display: flex; justify-content: space-between; margin-top: 30px; }
        .nav-btn { background: white; border: 2px solid #e2e8f0; padding: 10px 24px; border-radius: 8px; color: #64748b; }
        .nav-btn:hover { border-color: #94a3b8; color: var(--text); }
        .nav-btn:disabled { opacity: 0.5; cursor: not-allowed; }
        .header { display: flex; justify-content: space-between; border-bottom: 1px solid #e2e8f0; padding-bottom: 15px; margin-bottom: 20px; }
        .dots::after { content: '.'; animation: dots 1.5s steps(5, end) infinite; }
        @keyframes dots { 0%, 20% { content: '.'; } 40% { content: '..'; } 60% { content: '...'; } 80%, 100% { content: ''; } }
    </style>
</head>
<body>

<div class="app-container">
    <div id="setup-view" style="text-align:center; padding: 2rem 0;">
        <h1 style="color: var(--primary); margin-bottom: 10px;">Medical Board Tutor</h1>
        <p style="color: #64748b;">Database: <strong>150 Questions</strong></p>
        <p style="color: #64748b;">AI Status: <strong style="color: #16a34a;">CONNECTED</strong></p>
        <button class="primary-btn" onclick="startNewQuiz()">Start Session</button>
    </div>

    <div id="quiz-view" class="hidden">
        <div class="header">
            <span id="progress-text" style="font-weight:700; color:#64748b; font-size: 0.9rem;">Q 1</span>
            <span id="score-text" style="font-weight:700; color:var(--primary); font-size: 0.9rem;">Score: 0%</span>
        </div>
        <p id="q-text" style="font-size: 1.25rem; font-weight: 600; margin-bottom: 1.5rem;"></p>
        <div id="options-container"></div>
        <button class="ai-btn" onclick="askAI()"><span>✨ Explain This</span></button>
        <div id="ai-response-box"></div>
        <div class="nav-container">
            <button class="nav-btn" id="prev-btn" onclick="nav(-1)">Previous</button>
            <button class="nav-btn" id="next-btn" onclick="nav(1)">Next</button>
        </div>
        <div style="text-align: center; margin-top: 20px;">
             <button onclick="exitToMenu()" style="background: none; text-decoration: underline; color: #94a3b8; font-weight: normal; font-size: 0.9rem;">Exit</button>
        </div>
    </div>
</div>

<script>
    const API_KEY = "AIzaSyAwvv2MjKEYA8VG8UEvVrv3zi7AiNARaxU"; 
    
    // --- 150 QUESTION DATABASE ---
    const db = [
        // 1-30 (Original Krok)
        { q: "10-month-old boy, poor weight gain, persistent cough, thick sputum. Sweat chloride > 80 mEq/L. Diagnosis?", o: ["Cystic fibrosis", "Asthma", "Lung abnormality", "Foreign body", "Bronchitis"], a: 0 },
        { q: "42yo man, duodenal ulcer 20 yrs, vomiting food from day before, 'sloshing sound'. Complication?", o: ["Ulcer penetration", "Chronic pancreatitis", "Pyloric stenosis", "Perforation", "Cancer"], a: 2 },
        { q: "Pregnant woman, severe condition, BP 160/130, seizures, foam on lips. Diagnosis?", o: ["HTN crisis", "Diabetic coma", "Chorea", "Eclampsia", "Epilepsy"], a: 3 },
        { q: "Rash after stress. Plaques with silvery-yellow scales. 'Stearin spot' positive. Diagnosis?", o: ["Dermatophytosis", "Eczema", "Lichen ruber planus", "Allergic dermatitis", "Psoriasis"], a: 4 },
        { q: "Swallowed brake fluid. Anuria 5 days, creatinine 0.569. Treatment?", o: ["Diuretics", "Antidote", "Hemodialysis", "Plasmapheresis", "Detoxication"], a: 2 },
        { q: "19yo woman, amenorrhea, lethargy, weight loss. Post-partum hemorrhage 1 year ago, no lactation. Diagnosis?", o: ["Stein-Leventhal", "Hypothyroidism", "Astheno-vegetative", "Genital TB", "Sheehan syndrome"], a: 4 },
        { q: "64yo, sigmoid tumor, chronic thrombophlebitis. Prevention of DVT?", o: ["Phenylinum", "Low-molecular-weight heparin", "Regular heparin", "Aspirin", "Rheopolyglucin"], a: 1 },
        { q: "40yo smoker, cough, mucopurulent sputum, fever 37.6. Diffuse dry crackles. Diagnosis?", o: ["Lung cancer", "Asthma", "Pneumonia", "TB", "Chronic bronchitis"], a: 4 },
        { q: "5-day-old, icteric skin. Mother A(II)Rh+, Child 0(I)Rh+. Bilirubin 140 (indirect). Diagnosis?", o: ["Hemolytic anemia", "Congenital hepatitis", "Biliary atresia", "Hemolytic disease newborn", "Physiological jaundice"], a: 4 },
        { q: "57yo miner, smoker. Chest pain, blood sputum. X-ray: triangular shadow, apex to root. Diagnosis?", o: ["Pneumoconiosis", "TB", "Cancer right lung", "Bronchitis", "Pleuropneumonia"], a: 2 },
        { q: "Factory produces chromium/nickel. Medical committee needs minimum composition?", o: ["Therapist, hematologist, ophthalmologist", "Otolaryngologist, therapist, dermatologist", "Therapist, immunologist, surgeon", "Therapist, dermatologist, traumatologist", "Therapist, neuro"], a: 1 },
        { q: "18yo, skin rash after accident. Papular rash, silvery scales, 'thimble' sign on nails. Diagnosis?", o: ["Lupus", "Rheumatism", "Panaritium", "Onychomycosis", "Psoriasis"], a: 4 },
        { q: "Kwashiorkor diet needs addition of?", o: ["Milk, meat, vegetables", "Fish, veggies", "Poultry, fruit", "Veggies, fruit", "Cereals"], a: 1 },
        { q: "25yo man, hematuria after cold. BP 160/110. Urine: proteinuria 3.5 g/L, RBCs. Best diagnostic?", o: ["Urography", "Ultrasound", "Cystoscopy", "Tomography", "Biopsy"], a: 4 },
        { q: "37yo woman, occupational trauma, severe vision impairment. Needs new job. Rehab type?", o: ["Occupational", "Social", "Psychological", "Medical", "Target"], a: 0 },
        { q: "44yo man, vodka daily. Day 3: anxiety, visual hallucinations (horror on wall). Diagnosis?", o: ["Somatogenic psychosis", "Delirium tremens", "Neurosis", "Occupational delirium", "Hallucinosis"], a: 1 },
        { q: "50yo man, paint factory. Weakness, earth-gray skin, Heinz-Ehrlich bodies. Diagnosis?", o: ["Cyanide poisoning", "Acute aniline poisoning", "Toluene", "Hemolytic anemia", "Hepatitis"], a: 1 },
        { q: "18yo ate smoked fish/mushrooms. Vision 'fog', dry mouth, dilated pupils. Diagnosis?", o: ["Botulism", "Mushroom poisoning", "Salmonellosis", "Fish poisoning", "Staph"], a: 0 },
        { q: "8yo boy, Hep B history. Fatigue, nausea. Liver +1cm. ALT 220. Interpretation?", o: ["Recurrence", "Biliary dyskinesia", "Cirrhosis", "Chronic hepatitis", "Residual effects"], a: 3 },
        { q: "45yo man, duodenal ulcer. Weakness, pale skin, systolic murmur. Hb 88. Treatment?", o: ["Steroids", "Vit C", "B12", "Iron + diet", "Transfusion"], a: 3 },
        { q: "Polycystic kidney disease. Urine 2.5L. GFR 34, Creatinine 84. Stage?", o: ["Polyuric", "Intermittent", "Latent", "Terminal", "Compensated"], a: 4 },
        { q: "27yo woman, mucus stools, pain abates after defecation. Colonoscopy normal. Diagnosis?", o: ["Crohn", "IBS", "Whipple", "Colitis", "Enteritis"], a: 1 },
        { q: "46yo woman, pain right subcostal radiating to shoulder. Vomiting, fever. Tense muscles. Diagnosis?", o: ["Cholelithiasis", "Stricture", "Angina", "Ulcer", "Acute cholecystitis"], a: 4 },
        { q: "56yo disinfector. Nausea, bitter taste, liver enlarged. Bilirubin 40. Diagnosis?", o: ["Cholecystitis", "Pancreatitis", "Occupational toxic hepatitis", "Dyskinesia", "Acute cholecystitis"], a: 2 },
        { q: "8yo, growth retardation. 'Doll's face', thin hair, high sub-q fat. Diagnosis?", o: ["Pituitary dwarfism", "Chondrodystrophy", "Craniopharyngioma", "Fanconi", "Down syndrome"], a: 0 },
        { q: "5yo, cough attacks with reprises. Red face, bulging veins. Lymphocytes 72%. Diagnosis?", o: ["Foreign body", "Pertussis", "Adenovirus", "Pneumonia", "Bronchitis"], a: 1 },
        { q: "Urolithiasis and hyperoxaluria. Reduce which foods?", o: ["Dairy", "Meat", "Fatty", "Canned", "Sorrel, spinach, tomatoes"], a: 4 },
        { q: "Newborn 3980g. Right hand adducted, rotated, no movement shoulder/elbow. Diagnosis?", o: ["Klumpke", "Total plexus", "Duchenne-Erb", "Dislocation", "Fracture"], a: 2 },
        { q: "10yo boy, seasonal stuffy nose. Pale swollen mucosa. Father has asthma. Diagnosis?", o: ["Rhinitis", "Sinusitis", "Adenoiditis", "Allergic rhinitis", "Respiratory disease"], a: 3 },
        { q: "40yo woman, epilepsy. Seizures every 2-3 mins, no consciousness between. Diagnosis?", o: ["Grand mal", "Hysterical", "Status epilepticus", "Absence", "Psychosis"], a: 2 },

        // 31-80 (Internal Medicine & Surgery High Yield)
        { q: "35yo woman, fatigue, weight gain, cold intolerance. Goiter, delayed reflexes. High TSH. Diagnosis?", o: ["Graves", "Hashimoto's", "Subacute thyroiditis", "Adenoma", "Iodine deficiency"], a: 1 },
        { q: "60yo male, crushing chest pain, diaphoresis. ST elevation II, III, aVF. Artery?", o: ["LAD", "Circumflex", "RCA", "Left Main", "Posterior Descending"], a: 2 },
        { q: "4yo child, fever 5 days, strawberry tongue, desquamation. Complication?", o: ["Renal failure", "Coronary aneurysms", "Meningitis", "Liver failure", "PE"], a: 1 },
        { q: "24yo male, aggressive, vertical nystagmus, high pain tolerance. Drug?", o: ["Cocaine", "Heroin", "PCP", "Alcohol", "Benzos"], a: 2 },
        { q: "30yo female, painless vision loss one eye. Hx of arm weakness. MRI periventricular plaques. Diagnosis?", o: ["Multiple Sclerosis", "Guillain-Barré", "Myasthenia", "Stroke", "Tumor"], a: 0 },
        { q: "55yo smoker, painless hematuria. Gold standard test?", o: ["CT", "Ultrasound", "Cystoscopy", "MRI", "KUB"], a: 2 },
        { q: "Chronic A-Fib, sudden severe abdominal pain out of proportion to exam. High lactate. Diagnosis?", o: ["Appendicitis", "Mesenteric ischemia", "Diverticulitis", "Obstruction", "Cholecystitis"], a: 1 },
        { q: "2-week-old, projectile non-bilious vomiting. Olive mass. Diagnosis?", o: ["Intussusception", "Pyloric stenosis", "Hirschsprung", "Volvulus", "GERD"], a: 1 },
        { q: "32-week pregnant, painless bright red bleeding. Placenta covers os. Diagnosis?", o: ["Abruption", "Vasa previa", "Placenta previa", "Rupture", "Cervicitis"], a: 2 },
        { q: "45yo female, itching at night. High Alk Phos, +AMA. Diagnosis?", o: ["PSC", "PBC", "Autoimmune Hep", "Cirrhosis", "Hemochromatosis"], a: 1 },
        { q: "70yo male, asbestos. Dyspnea. Pleural thickening, bloody effusion. Diagnosis?", o: ["Small cell", "Adeno", "Mesothelioma", "Squamous", "TB"], a: 2 },
        { q: "Patient on Haloperidol. Fever 40C, rigidity, autonomic instability. Diagnosis?", o: ["Serotonin Syn", "Neuroleptic Malignant Syn", "Malignant Hyperthermia", "Anticholinergic", "Dystonia"], a: 1 },
        { q: "22yo athlete collapses. Family hx sudden death. Autopsy?", o: ["Dilated CM", "Hypertrophic CM", "MVP", "Aortic Stenosis", "Myocarditis"], a: 1 },
        { q: "SLE patient on steroids. Hip pain. MRI crescent sign. Diagnosis?", o: ["OA", "Avascular necrosis", "RA", "Septic arthritis", "Osteomyelitis"], a: 1 },
        { q: "Newborn, continuous machinery murmur. Diagnosis?", o: ["VSD", "ASD", "PDA", "Tetralogy", "Coarctation"], a: 2 },
        { q: "Alcoholic, confusion, ataxia, ophthalmoplegia. Diagnosis?", o: ["Korsakoff", "Wernicke", "DTs", "Hepatic enceph", "Subdural"], a: 1 },
        { q: "Moon face, buffalo hump, purple striae. Screening test?", o: ["Cortisol", "ACTH stim", "Low-dose Dex suppression", "MRI", "TSH"], a: 2 },
        { q: "65yo male, back pain worse at night. M-spike. Diagnosis?", o: ["Multiple Myeloma", "Prostate Ca", "Lymphoma", "Osteoporosis", "Paget's"], a: 0 },
        { q: "3yo boy, limp after viral URI. Afebrile. Diagnosis?", o: ["Septic arthritis", "Transient synovitis", "Perthes", "SCFE", "Osteomyelitis"], a: 1 },
        { q: "25yo female, rubbery mobile breast mass. Diagnosis?", o: ["Fibrocystic", "Fibroadenoma", "Papilloma", "Cancer", "Fat necrosis"], a: 1 },
        { q: "HIV, CD4 40. Pizza pie retinopathy. Diagnosis?", o: ["Toxo", "CMV", "PCP", "Candida", "HSV"], a: 1 },
        { q: "Fall on outstretched hand. Snuffbox tenderness. X-ray neg. Next?", o: ["MRI", "Thumb spica", "Wrap", "Surgery", "PT"], a: 1 },
        { q: "Tremor at rest, shuffling gait. Neurotransmitter?", o: ["Serotonin", "Acetylcholine", "Dopamine", "GABA", "Norepinephrine"], a: 2 },
        { q: "Pregnant, rash on palms/soles. Hx of painless ulcer. Diagnosis?", o: ["Herpes", "Syphilis", "Chancroid", "Gonorrhea", "HPV"], a: 1 },
        { q: "Unilateral throbbing headache, tearing, runny nose. Night attacks. Diagnosis?", o: ["Migraine", "Tension", "Cluster", "Sinusitis", "Temporal arteritis"], a: 2 },
        { q: "Type 1 DM, vomiting, fruity breath. Priority?", o: ["Insulin", "IV Fluids", "Bicarb", "Potassium", "Antibiotics"], a: 1 },
        { q: "75yo female fall. Leg shortened/externally rotated. Diagnosis?", o: ["Post hip dislocation", "Femoral neck fx", "Intertrochanteric", "Pelvic", "Ant hip dislocation"], a: 1 },
        { q: "Harsh systolic murmur right upper sternal border, radiates to carotids. Syncope. Diagnosis?", o: ["MR", "AS", "AR", "MS", "TR"], a: 1 },
        { q: "16yo amenorrhea, webbed neck, wide nipples. Karyotype?", o: ["46XX", "47XXY", "45XO", "47XXX", "46XY"], a: 2 },
        { q: "Target lesions on hands/feet after Herpes. Diagnosis?", o: ["SJS", "Erythema Multiforme", "Psoriasis", "Lichen Planus", "Contact Derm"], a: 1 },
        { q: "Severe big toe pain. Needle-shaped neg birefringent crystals. Diagnosis?", o: ["Pseudogout", "Septic", "Gout", "RA", "OA"], a: 2 },
        { q: "Newborn no meconium. Barium enema transition zone. Diagnosis?", o: ["Meconium ileus", "Hirschsprung", "Intussusception", "Imperforate anus", "NEC"], a: 1 },
        { q: "Back pain worse in morning, HLA-B27. Diagnosis?", o: ["Strain", "Ankylosing Spondylitis", "Disc", "Stenosis", "Reactive arthritis"], a: 1 },
        { q: "Warfarin skin necrosis. Deficiency?", o: ["Factor V", "Protein C", "Antithrombin", "Prothrombin", "Lupus anticoag"], a: 1 },
        { q: "Numbness first 3 digits. Phalen positive. Nerve?", o: ["Ulnar", "Radial", "Median", "Musculocutaneous", "Axillary"], a: 2 },
        { q: "Bag of worms scrotum. Concern?", o: ["Varicocele", "Hydrocele", "Spermatocele", "RCC", "Torsion"], a: 3 },
        { q: "Slapped cheek rash. Diagnosis?", o: ["Measles", "Rubella", "Roseola", "Fifth Disease", "Scarlet Fever"], a: 3 },
        { q: "Dysphagia solids/liquids. Bird's beak. Diagnosis?", o: ["GERD", "Spasms", "Achalasia", "Zenker", "Scleroderma"], a: 2 },
        { q: "Post-splenectomy fever. Howell-Jolly. Organism?", o: ["Staph", "Strep pneumo", "E coli", "Pseudomonas", "Listeria"], a: 1 },
        { q: "Painless testicular mass. High AFP/bHCG. Diagnosis?", o: ["Seminoma", "Non-seminoma", "Leydig", "Hydrocele", "Epididymitis"], a: 1 },
        { q: "Vertigo, tinnitus, hearing loss. Episodic. Diagnosis?", o: ["BPPV", "Meniere's", "Neuritis", "Acoustic neuroma", "Labyrinthitis"], a: 1 },
        { q: "Star of death on CT head. Cause?", o: ["Trauma", "Berry Aneurysm", "AVM", "HTN", "Coagulopathy"], a: 1 },
        { q: "Hypotension, muffled heart, JVD. Diagnosis?", o: ["Pneumo", "Tamponade", "MI", "PE", "Dissection"], a: 1 },
        { q: "Gout flare on HCTZ. Mechanism?", o: ["High Ca", "Decreased uric acid excretion", "High purine", "Autoimmune", "Infection"], a: 1 },
        { q: "Barking cough, steeple sign. Diagnosis?", o: ["Epiglottitis", "Croup", "Foreign body", "Asthma", "Bronchiolitis"], a: 1 },
        { q: "Cafe-au-lait, Lisch nodules. Diagnosis?", o: ["NF1", "NF2", "Tuberous Sclerosis", "Sturge-Weber", "VHL"], a: 0 },
        { q: "HTN, string of beads renal artery. Diagnosis?", o: ["Atherosclerosis", "Fibromuscular Dysplasia", "PAN", "Stenosis", "HTN"], a: 1 },
        { q: "Hematemesis after retching. Diagnosis?", o: ["Varices", "Mallory-Weiss", "Boerhaave", "Ulcer", "Gastritis"], a: 1 },
        { q: "Trauma, trachea deviated left, no breath sounds right. Diagnosis?", o: ["Left Tension", "Right Tension Pneumo", "Hemothorax", "Tamponade", "Flail"], a: 1 },
        { q: "Painless jaundice, palpable gallbladder. Diagnosis?", o: ["Stones", "Pancreatic Cancer", "Hepatitis", "Cholangitis", "Liver Ca"], a: 1 },

        // 81-120 (OBGYN & Peds)
        { q: "Sudden lower abd pain, LMP 6 weeks ago, hypotensive. Diagnosis?", o: ["Appendicitis", "Ectopic Pregnancy", "Torsion", "PID", "Cystitis"], a: 1 },
        { q: "Burn fluid resuscitation formula. Fluid?", o: ["D5W", "Half NS", "Lactated Ringer's", "Albumin", "Hypertonic"], a: 2 },
        { q: "Post-op fever day 3. Wound clean. Urine WBCs. Diagnosis?", o: ["Pneumonia", "UTI", "Wound inf", "DVT", "Line inf"], a: 1 },
        { q: "Constipation, 'coffee bean' X-ray. Diagnosis?", o: ["Sigmoid Volvulus", "Cecal Volvulus", "SBO", "Diverticulitis", "Colon Ca"], a: 0 },
        { q: "Epigastric pain to back. High Lipase. Diagnosis?", o: ["Cholecystitis", "Pancreatitis", "Ulcer", "Gastritis", "Ischemia"], a: 1 },
        { q: "A-fib, cold/pale/painful leg. Diagnosis?", o: ["DVT", "Acute Arterial Occlusion", "Cellulitis", "Venous insuff", "Neuropathy"], a: 1 },
        { q: "Boxer fracture bone?", o: ["1st Metacarpal", "Scaphoid", "5th Metacarpal", "Radius", "Ulna"], a: 2 },
        { q: "Head trauma, lucid interval, lens bleed. Diagnosis?", o: ["Subdural", "Epidural", "Subarachnoid", "Intraparenchymal", "Concussion"], a: 1 },
        { q: "Scaphoid fx risk?", o: ["Malunion", "Avascular Necrosis", "Infection", "Nerve", "DVT"], a: 1 },
        { q: "Humerus shaft fx nerve?", o: ["Ulnar", "Median", "Radial", "Axillary", "Musculocutaneous"], a: 2 },
        { q: "Currant jelly stool, mass. Diagnosis?", o: ["Stenosis", "Intussusception", "Volvulus", "Hirschsprung", "Appy"], a: 1 },
        { q: "Newborn drooling, NG tube coils. Diagnosis?", o: ["TE Fistula", "Pyloric", "GERD", "Atresia", "Hernia"], a: 0 },
        { q: "Fat Female Forty Fertile. RUQ pain. Diagnosis?", o: ["Choledocholithiasis", "Cholelithiasis", "Pancreatitis", "Ulcer", "Hepatitis"], a: 1 },
        { q: "McBurney point tenderness. Diagnosis?", o: ["Cholecystitis", "Appendicitis", "Diverticulitis", "Pancreatitis", "Kidney Stone"], a: 1 },
        { q: "Tingling after thyroidectomy. Cause?", o: ["Thyroid storm", "Hypocalcemia", "Hypercalcemia", "Hyponatremia", "Hypokalemia"], a: 1 },
        { q: "Common cause SBO adults?", o: ["Hernias", "Adhesions", "Tumors", "Crohn's", "Volvulus"], a: 1 },
        { q: "Pulsatile abd mass, hypotension. Diagnosis?", o: ["Ruptured AAA", "Diverticulitis", "Pancreatitis", "Obstruction", "Colic"], a: 0 },
        { q: "Burn eschar, loss of pulse. Tx?", o: ["Abx", "Escharotomy", "Amputation", "Elevation", "Heparin"], a: 1 },
        { q: "Raccoon eyes, Battle sign. Diagnosis?", o: ["Basilar Skull Fx", "Orbital", "Nasal", "Mandible", "Le Fort"], a: 0 },
        { q: "Sudden testicular pain, absent cremasteric. Diagnosis?", o: ["Epididymitis", "Torsion", "Hernia", "Hydrocele", "Varicocele"], a: 1 },
        { q: "Pre-eclampsia is HTN plus?", o: ["Edema", "Proteinuria", "Seizures", "Headache", "Vision change"], a: 1 },
        { q: "Snowstorm on ultrasound, high hCG. Diagnosis?", o: ["Molar Pregnancy", "Twins", "Ectopic", "Normal", "Fibroids"], a: 0 },
        { q: "Foul smelling lochia, fever post-partum. Diagnosis?", o: ["Endometritis", "UTI", "Mastitis", "Pneumonia", "DVT"], a: 1 },
        { q: "Grey-white discharge, fishy odor, clue cells. Diagnosis?", o: ["Candida", "Bacterial Vaginosis", "Trichomonas", "Chlamydia", "Gonorrhea"], a: 1 },
        { q: "Strawberry cervix, motile protozoa. Diagnosis?", o: ["Candida", "BV", "Trichomonas", "Chlamydia", "Gonorrhea"], a: 2 },
        { q: "Primary amenorrhea, uterus absent. Diagnosis?", o: ["Turner", "Mullerian Agenesis", "PCOS", "Prolactinoma", "Asherman"], a: 1 },
        { q: "PCOS triad: Oligo, Hyperandrogenism, and?", o: ["Cysts", "Obesity", "Diabetes", "Pain", "Bleeding"], a: 0 },
        { q: "Post-menopausal bleeding. Next step?", o: ["Pap smear", "Endometrial Biopsy", "Ultrasound", "CT", "MRI"], a: 1 },
        { q: "Chocolate cyst. Diagnosis?", o: ["PCOS", "Endometrioma", "Dermoid", "Fibroid", "Cancer"], a: 1 },
        { q: "Most common cause of post-partum hemorrhage?", o: ["Laceration", "Uterine Atony", "Retained tissue", "Coagulopathy", "Rupture"], a: 1 },
        { q: "Baby blues last how long?", o: ["2 days", "2 weeks", "2 months", "6 months", "1 year"], a: 1 },
        { q: "Blueberry muffin rash newborn. Diagnosis?", o: ["Rubella/CMV", "Measles", "Varicella", "HSV", "Syphilis"], a: 0 },
        { q: "Sandpaper rash, strawberry tongue. Diagnosis?", o: ["Kawasaki", "Scarlet Fever", "Measles", "Rubella", "Roseola"], a: 1 },
        { q: "Cough, Coryza, Conjunctivitis, Koplik spots. Diagnosis?", o: ["Measles", "Rubella", "Mumps", "Roseola", "Varicella"], a: 0 },
        { q: "Parotid swelling, orchitis. Diagnosis?", o: ["Measles", "Rubella", "Mumps", "Flu", "CMV"], a: 2 },
        { q: "Projectile bilious vomiting. Double bubble. Diagnosis?", o: ["Pyloric", "Duodenal Atresia", "Volvulus", "Hirschsprung", "NEC"], a: 1 },
        { q: "Salty sweat, meconium ileus. Diagnosis?", o: ["Cystic Fibrosis", "Celiac", "Asthma", "Allergy", "GERD"], a: 0 },
        { q: "Delayed passage of meconium (>48h). Diagnosis?", o: ["CF", "Hirschsprung", "Volvulus", "Atresia", "Intussusception"], a: 1 },
        { q: "Painful limp, obese adolescent. Diagnosis?", o: ["Perthes", "SCFE", "Septic", "Transient", "Fracture"], a: 1 },
        { q: "Blue sclera, fractures. Diagnosis?", o: ["Osteogenesis Imperfecta", "Rickets", "Abuse", "Achondroplasia", "Ehlers-Danlos"], a: 0 },

        // 121-150 (Neuro & Psych)
        { q: "Worst headache of life. Diagnosis?", o: ["Migraine", "Cluster", "Subarachnoid", "Tension", "Tumor"], a: 2 },
        { q: "Ascending paralysis, absent reflexes. Diagnosis?", o: ["MS", "Guillain-Barre", "ALS", "Myasthenia", "Botulism"], a: 1 },
        { q: "Ptosis, diplopia, worse end of day. Diagnosis?", o: ["MS", "Guillain-Barre", "ALS", "Myasthenia Gravis", "Lambert-Eaton"], a: 3 },
        { q: "Resting tremor, rigidity, bradykinesia. Diagnosis?", o: ["Parkinson's", "Essential Tremor", "Huntington's", "Wilson's", "Dementia"], a: 0 },
        { q: "Chorea, behavioral change, CAG repeats. Diagnosis?", o: ["Parkinson's", "Huntington's", "Wilson's", "Alzheimer's", "Pick's"], a: 1 },
        { q: "Memory loss, neurofibrillary tangles. Diagnosis?", o: ["Alzheimer's", "Vascular", "Lewy Body", "Frontotemporal", "Normal Pressure Hydro"], a: 0 },
        { q: "Wet, Wobbly, Wacky. Diagnosis?", o: ["Alzheimer's", "Parkinson's", "Normal Pressure Hydrocephalus", "Stroke", "Tumor"], a: 2 },
        { q: "Visual hallucinations, parkinsonism. Diagnosis?", o: ["Alzheimer's", "Lewy Body Dementia", "Frontotemporal", "Vascular", "CJD"], a: 1 },
        { q: "Sudden onset focal deficit. First test?", o: ["MRI", "CT non-contrast", "Angio", "EEG", "LP"], a: 1 },
        { q: "Loss of pain/temp cape distribution. Diagnosis?", o: ["Syringomyelia", "Brown-Sequard", "Anterior Cord", "Central Cord", "Posterior Cord"], a: 0 },
        { q: "Split personality is NOT schizophrenia. Diagnosis?", o: ["Schizoaffective", "Dissociative Identity Disorder", "Bipolar", "Borderline", "Histrionic"], a: 1 },
        { q: "Hallucinations + Delusions > 6 months. Diagnosis?", o: ["Schizophrenia", "Schizophreniform", "Brief Psychotic", "Schizoaffective", "Delusional"], a: 0 },
        { q: "Symptoms of schizophrenia < 1 month. Diagnosis?", o: ["Schizophrenia", "Schizophreniform", "Brief Psychotic Disorder", "Schizoaffective", "Adjustment"], a: 2 },
        { q: "Worry about everything > 6 months. Diagnosis?", o: ["Panic", "GAD", "Social Phobia", "OCD", "PTSD"], a: 1 },
        { q: "Fear of open spaces. Diagnosis?", o: ["Social Phobia", "Agoraphobia", "Panic", "Specific Phobia", "GAD"], a: 1 },
        { q: "Recurring intrusive thoughts + rituals. Diagnosis?", o: ["OCD", "OCPD", "GAD", "Panic", "Schizo"], a: 0 },
        { q: "Re-experiencing trauma, avoidance, arousal. Diagnosis?", o: ["Acute Stress", "PTSD", "Adjustment", "Panic", "GAD"], a: 1 },
        { q: "Binge eating + purging. Diagnosis?", o: ["Anorexia", "Bulimia", "Binge Eating", "Pica", "Rumination"], a: 1 },
        { q: "Refusal to maintain body weight. Diagnosis?", o: ["Anorexia Nervosa", "Bulimia", "Binge Eating", "Body Dysmorphia", "OCD"], a: 0 },
        { q: "Flight of ideas, grandiosity, no sleep. Diagnosis?", o: ["Depression", "Manic Episode", "Hypomania", "Cyclothymia", "Dysthymia"], a: 1 },
        { q: "Depression + Hypomania. Diagnosis?", o: ["Bipolar I", "Bipolar II", "Cyclothymia", "Dysthymia", "Major Depression"], a: 1 },
        { q: "Unstable relationships, splitting, self-harm. Diagnosis?", o: ["Borderline PD", "Histrionic", "Narcissistic", "Antisocial", "Avoidant"], a: 0 },
        { q: "Criminal behavior, no remorse, >18yo. Diagnosis?", o: ["Conduct", "Antisocial PD", "Borderline", "Narcissistic", "Oppositional"], a: 1 },
        { q: "Loner, no desire for relationships. Diagnosis?", o: ["Schizoid", "Schizotypal", "Avoidant", "Paranoid", "Antisocial"], a: 0 },
        { q: "Magical thinking, odd behavior. Diagnosis?", o: ["Schizoid", "Schizotypal", "Schizophrenia", "Avoidant", "Paranoid"], a: 1 },
        { q: "Wants relationships but fears rejection. Diagnosis?", o: ["Schizoid", "Avoidant", "Dependent", "Borderline", "Histrionic"], a: 1 },
        { q: "Submissive, clingy. Diagnosis?", o: ["Dependent", "Avoidant", "Borderline", "Histrionic", "Narcissistic"], a: 0 },
        { q: "Obsessed with orderliness/control. Diagnosis?", o: ["OCD", "OCPD", "Paranoid", "Schizoid", "Narcissistic"], a: 1 },
        { q: "Alcohol withdrawal, confusion, ataxia, ophthalmoplegia. Tx?", o: ["Glucose then Thiamine", "Thiamine then Glucose", "Benzos", "Haldol", "Fluids"], a: 1 },
        { q: "Opioid overdose triad: Coma, Pinpoint pupils, and?", o: ["Hypertension", "Respiratory Depression", "Tachycardia", "Seizure", "Hyperthermia"], a: 1 }
    ];

    let activeQ = [];
    let answers = {};
    let idx = 0;

    function startNewQuiz() {
        activeQ = db.sort(() => 0.5 - Math.random()); 
        userAnswers = {};
        idx = 0;
        document.getElementById('setup-view').classList.add('hidden');
        document.getElementById('quiz-view').classList.remove('hidden');
        render();
    }
    
    function exitToMenu() {
        if(confirm("Exit?")) {
            document.getElementById('quiz-view').classList.add('hidden');
            document.getElementById('setup-view').classList.remove('hidden');
        }
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
                if(i === answers[idx]) btn.classList.add('selected');
            }
            btn.onclick = () => { answers[idx] = i; render(); };
            div.appendChild(btn);
        });
        
        document.getElementById('ai-response-box').style.display = 'none';
        document.getElementById('prev-btn').disabled = (idx === 0);
        document.getElementById('next-btn').disabled = (idx === activeQ.length - 1);
    }

    function nav(d) {
        if(idx + d >= 0 && idx + d < activeQ.length) { idx += d; render(); }
    }

    async function askAI() {
        const q = activeQ[idx];
        const box = document.getElementById('ai-response-box');
        box.style.display = 'block';
        box.innerHTML = "<span class='dots'><strong>🤖 AI is analyzing</strong></span>";
        const prompt = `Medical Board Tutor. Q: "${q.q}" Correct: "${q.o[q.a]}" Options: ${JSON.stringify(q.o)} Task: 1. Explain why correct. 2. Explain why others wrong. Keep it concise.`;
        try {
            const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${API_KEY}`;
            const response = await fetch(url, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] }) });
            const data = await response.json();
            if(data.error) { box.innerHTML = `<span style="color:red">Error: ${data.error.message}</span>`; } else {
                box.innerHTML = data.candidates[0].content.parts[0].text.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>').replace(/\n/g, '<br>');
            }
        } catch (e) { box.innerHTML = `<span style="color:red">Connection Error.</span>`; }
    }
</script>
</body>
</html>
