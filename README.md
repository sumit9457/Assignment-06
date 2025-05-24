<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Quiz App with Login</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: Arial, sans-serif; background-color: #f0f0f0; padding: 20px; }
    .card {
      background: white; padding: 20px; border-radius: 8px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1); max-width: 600px; margin: 0 auto 20px;
    }
    h2 { margin-bottom: 15px; }
    .input-group { margin-bottom: 15px; }
    input[type="text"], input[type="password"] {
      width: 100%; padding: 8px; margin-top: 4px;
    }
    button {
      padding: 10px 15px; border: none; border-radius: 4px;
      background-color: #007BFF; color: white; cursor: pointer; width: 100%;
    }
    button:hover { background-color: #0056b3; }
    .navigation-buttons {
      display: flex; justify-content: space-between; margin-top: 10px;
    }
    .navigation-buttons button,
    .submit-button {
      width: 48%; background-color: #007BFF;
    }
    .submit-button {
      width: 100%; margin-top: 10px; background-color: #28a745;
    }
    .submit-button:hover { background-color: #218838; }
    .result { margin-top: 20px; }
    .correct { color: green; }
    .incorrect { color: red; }
    label { display: block; margin-bottom: 10px; }
  </style>
</head>
<body>

<!-- Sign Up -->
<div class="card" id="signupCard">
  <h2>Sign Up</h2>
  <div class="input-group">
    <label>Username: <input type="text" id="signupUsername" /></label>
    <label>Password: <input type="password" id="signupPassword" /></label>
  </div>
  <button onclick="handleSignup()">Sign Up</button>
  <p style="text-align:center; margin-top:10px;">
    Already have an account? <a href="#" onclick="showLogin()">Login</a>
  </p>
</div>

<!-- Login -->
<div class="card" id="loginCard" style="display:none;">
  <h2>Login</h2>
  <div class="input-group">
    <label>Username: <input type="text" id="loginUsername" /></label>
    <label>Password: <input type="password" id="loginPassword" /></label>
  </div>
  <button onclick="handleLogin()">Login</button>
  <p style="text-align:center; margin-top:10px;">
    Don't have an account? <a href="#" onclick="showSignup()">Sign Up</a>
  </p>
</div>

<!-- Quiz -->
<div class="card" id="quizCard" style="display:none;">
  <h2 id="question">Question</h2>
  <div id="options"></div>
  <div class="navigation-buttons">
    <button id="prevBtn">Previous</button>
    <button id="nextBtn">Next</button>
  </div>
  <button class="submit-button" id="submitBtn">Submit Quiz</button>
  <div class="result" id="result"></div>
</div>

<script>
  function showSignup() {
    signupCard.style.display = "block";
    loginCard.style.display = "none";
  }

  function showLogin() {
    signupCard.style.display = "none";
    loginCard.style.display = "block";
  }

  function handleSignup() {
    const user = signupUsername.value.trim();
    const pass = signupPassword.value.trim();
    if (!user || !pass) return alert("Please fill in both fields.");
    if (localStorage.getItem(user)) return alert("Username already exists.");
    localStorage.setItem(user, pass);
    alert("Signup successful! Please login.");
    showLogin();
  }

  function handleLogin() {
    const user = loginUsername.value.trim();
    const pass = loginPassword.value.trim();
    if (localStorage.getItem(user) === pass) {
      loginCard.style.display = "none";
      quizCard.style.display = "block";
      loadQuestion(currentQuestion);
    } else {
      alert("Invalid username or password.");
    }
  }

  const quizData = [
    { question: "What is the capital of France?", options: ["Berlin", "Madrid", "Paris", "Lisbon"], answer: "Paris" },
    { question: "Which language is used for web development?", options: ["Python", "HTML", "Java", "C++"], answer: "HTML" },
    { question: "What is the largest planet?", options: ["Earth", "Mars", "Jupiter", "Saturn"], answer: "Jupiter" },
    { question: "Who wrote 'Hamlet'?", options: ["Shakespeare", "Twain", "Austen", "Dickens"], answer: "Shakespeare" },
    { question: "Chemical symbol for water?", options: ["O2", "H2O", "CO2", "NaCl"], answer: "H2O" },
    { question: "Land of the Rising Sun?", options: ["China", "Japan", "Korea", "India"], answer: "Japan" },
    { question: "Mona Lisa painter?", options: ["Da Vinci", "Picasso", "Van Gogh", "Monet"], answer: "Da Vinci" },
    { question: "Smallest prime number?", options: ["0", "1", "2", "3"], answer: "2" },
    { question: "Largest ocean?", options: ["Atlantic", "Indian", "Arctic", "Pacific"], answer: "Pacific" },
    { question: "Boiling point of water?", options: ["90°C", "100°C", "110°C", "120°C"], answer: "100°C" }
  ];

  let currentQuestion = 0;
  let userAnswers = new Array(quizData.length).fill(null);

  const questionEl = document.getElementById('question');
  const optionsEl = document.getElementById('options');
  const prevBtn = document.getElementById('prevBtn');
  const nextBtn = document.getElementById('nextBtn');
  const submitBtn = document.getElementById('submitBtn');
  const resultEl = document.getElementById('result');

  function loadQuestion(index) {
    const q = quizData[index];
    questionEl.textContent = `${index + 1}. ${q.question}`;
    optionsEl.innerHTML = '';
    q.options.forEach(opt => {
      const label = document.createElement('label');
      const input = document.createElement('input');
      input.type = 'radio'; input.name = 'answer'; input.value = opt;
      if (userAnswers[index] === opt) input.checked = true;
      label.appendChild(input);
      label.appendChild(document.createTextNode(opt));
      optionsEl.appendChild(label);
    });
    prevBtn.disabled = index === 0;
    nextBtn.disabled = index === quizData.length - 1;
  }

  function getSelectedOption() {
    const opts = document.getElementsByName('answer');
    for (const o of opts) if (o.checked) return o.value;
    return null;
  }

  prevBtn.onclick = () => {
    const selected = getSelectedOption();
    if (selected) userAnswers[currentQuestion] = selected;
    if (currentQuestion > 0) {
      currentQuestion--;
      loadQuestion(currentQuestion);
    }
  };

  nextBtn.onclick = () => {
    const selected = getSelectedOption();
    if (selected) userAnswers[currentQuestion] = selected;
    if (currentQuestion < quizData.length - 1) {
      currentQuestion++;
      loadQuestion(currentQuestion);
    }
  };

  submitBtn.onclick = () => {
    const selected = getSelectedOption();
    if (selected) userAnswers[currentQuestion] = selected;
    if (userAnswers.includes(null)) return alert("Answer all questions.");
    let score = 0;
    resultEl.innerHTML = "<h3>Results:</h3>";
    quizData.forEach((q, i) => {
      const userAns = userAnswers[i];
      const correct = userAns === q.answer;
      if (correct) score++;
      resultEl.innerHTML += `
        <p><strong>${i + 1}. ${q.question}</strong><br/>
        Your answer: <span class="${correct ? 'correct' : 'incorrect'}">${userAns}</span><br/>
        ${!correct ? `Correct: <span class="correct">${q.answer}</span>` : ""}</p>`;
    });
    resultEl.innerHTML += `<p><strong>Your score: ${score} / ${quizData.length}</strong></p>`;
    questionEl.style.display = optionsEl.style.display = 
    prevBtn.style.display = nextBtn.style.display = 
    submitBtn.style.display = "none";
  };
</script>
</body>
</html>
