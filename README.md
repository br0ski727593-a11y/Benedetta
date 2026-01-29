# Medical Board Quiz App

A lightweight, single-file web application for practicing medical board exam questions.

## Features
- **Customizable Quiz Length:** Choose between 10, 25, 50, 100, or all available questions.
- **Randomized Order:** Questions are shuffled every time you start a new quiz.
- **Auto-Save:** Your progress is saved automatically to your browser. You can close the tab and resume later.
- **Instant Feedback:** See correct/incorrect answers immediately upon selection.
- **Responsive Design:** Works on mobile, tablet, and desktop.

## How to Run
1. Download the `index.html` file.
2. Open the file in any modern web browser (Google Chrome, Firefox, Safari, Edge).
3. Select your quiz length and click **Start New Quiz**.

## How to Add More Questions
The quiz content is stored directly inside the `index.html` file in a JavaScript array. To add more questions:

1. Open `index.html` in a text editor (like VS Code, Notepad++, or Sublime Text).
2. Find the line that says: `const fullQuestionPool = [`
3. Add new questions inside the square brackets following this format:

```javascript
{
    q: "Type your question text here?",
    o: [
        "Option A text", 
        "Option B text", 
        "Option C text", 
        "Option D text", 
        "Option E text"
    ],
    a: 0 // The index of the correct answer (0 = Option A, 1 = Option B, etc.)
},
