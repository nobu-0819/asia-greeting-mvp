<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>インドネシア語ひとことクイズ MVP</title>
  <style>
    * { box-sizing: border-box; }

    body {
      margin: 0;
      min-height: 100vh;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: linear-gradient(135deg, #fff7ed, #e0f2fe);
      color: #1f2937;
      padding: 18px;
    }

    .app { max-width: 680px; margin: 0 auto; }
    header { margin-bottom: 18px; }

    h1 {
      margin: 0 0 8px;
      font-size: 28px;
      line-height: 1.2;
    }

    .subtitle {
      margin: 0;
      color: #555;
      font-size: 15px;
      line-height: 1.6;
    }

    .card {
      background: rgba(255, 255, 255, 0.9);
      border: 1px solid rgba(0, 0, 0, 0.08);
      border-radius: 24px;
      padding: 20px;
      box-shadow: 0 14px 36px rgba(0, 0, 0, 0.1);
      margin-bottom: 14px;
    }

    .badge {
      display: inline-block;
      background: #111827;
      color: white;
      border-radius: 999px;
      padding: 6px 12px;
      font-size: 13px;
      font-weight: 800;
      margin-bottom: 14px;
    }

    .question-label {
      font-size: 14px;
      color: #666;
      font-weight: 700;
      margin-bottom: 8px;
    }

    .question {
      font-size: 40px;
      font-weight: 950;
      line-height: 1.2;
      margin-bottom: 8px;
    }

    .prompt {
      font-size: 18px;
      font-weight: 750;
      color: #444;
      margin-bottom: 18px;
    }

    .choices { display: grid; gap: 10px; }

    button {
      appearance: none;
      border: none;
      border-radius: 18px;
      padding: 16px;
      font-size: 17px;
      font-weight: 850;
      background: #ffffff;
      color: #111827;
      cursor: pointer;
      box-shadow: 0 6px 16px rgba(0, 0, 0, 0.08);
      transition: transform 0.12s ease, box-shadow 0.12s ease, background 0.12s ease, opacity 0.12s ease;
      text-align: left;
    }

    button:hover { box-shadow: 0 10px 22px rgba(0, 0, 0, 0.12); }
    button:active { transform: scale(0.98); }
    button:disabled { opacity: 0.55; cursor: not-allowed; }

    .choice.selected {
      background: #dbeafe;
      border: 2px solid #2563eb;
    }

    .choice.correct {
      background: #dcfce7;
      border: 2px solid #16a34a;
    }

    .choice.wrong {
      background: #fee2e2;
      border: 2px solid #dc2626;
    }

    .answer-button-wrap {
      display: none;
      margin-top: 14px;
    }

    .answer-button-wrap.show { display: block; }

    .answer-button {
      width: 100%;
      text-align: center;
      background: #2563eb;
      color: white;
      font-size: 18px;
    }

    .feedback {
      display: none;
      margin-top: 16px;
      padding: 14px;
      border-radius: 16px;
      font-weight: 800;
      line-height: 1.6;
    }

    .feedback.show { display: block; }
    .feedback.correct { background: #dcfce7; color: #166534; }
    .feedback.wrong { background: #fee2e2; color: #991b1b; }

    .answer-box {
      display: none;
      margin-top: 14px;
      background: #f8fafc;
      border-radius: 18px;
      padding: 14px;
      line-height: 1.7;
    }

    .answer-box.show { display: block; }

    .native {
      font-size: 28px;
      font-weight: 950;
      margin-bottom: 2px;
    }

    .pronunciation {
      font-size: 18px;
      font-weight: 800;
      color: #444;
    }

    .meaning { color: #555; }

    .actions {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
      margin-top: 14px;
    }

    .action-button {
      text-align: center;
      background: #111827;
      color: white;
    }

    .score {
      font-size: 15px;
      color: #555;
      line-height: 1.7;
    }

    @media (max-width: 520px) {
      h1 { font-size: 24px; }
      .question { font-size: 34px; }
      .actions { grid-template-columns: 1fr; }
    }
  </style>
</head>
<body>
  <main class="app">
    <header>
      <h1>インドネシア語ひとことクイズ</h1>
      <p class="subtitle">日本語を見て、インドネシア語を選ぶMVP版。まずは3単語だけ。</p>
    </header>

    <section class="card">
      <div class="badge">🇮🇩 インドネシア語モード</div>
      <div class="question-label">この日本語をインドネシア語で言うと？</div>
      <div id="question" class="question"></div>
      <div class="prompt">答えを選んでから「回答」を押してください</div>
      <div id="choices" class="choices"></div>

      <div id="answerButtonWrap" class="answer-button-wrap">
        <button id="submitButton" class="answer-button">回答</button>
      </div>

      <div id="feedback" class="feedback"></div>

      <div id="answerBox" class="answer-box">
        <div id="native" class="native"></div>
        <div id="pronunciation" class="pronunciation"></div>
        <div id="meaning" class="meaning"></div>
      </div>

      <div class="actions">
        <button id="speakButton" class="action-button">▶ 正解を聞く</button>
        <button id="nextButton" class="action-button">次の問題へ</button>
      </div>
    </section>

    <div id="streakPopup" style="display:none; position:fixed; inset:0; z-index:999; pointer-events:none; align-items:center; justify-content:center;">
      <div id="streakPopupInner" style="background:rgba(17,24,39,0.92); color:white; padding:28px 34px; border-radius:28px; text-align:center; transform:scale(0.8); opacity:0; transition:all 0.35s ease; box-shadow:0 20px 50px rgba(0,0,0,0.35);">
        <div id="streakEmoji" style="font-size:54px; margin-bottom:10px;">🔥</div>
        <div id="streakTitle" style="font-size:34px; font-weight:950; margin-bottom:6px;">3連続正解！</div>
        <div id="streakMessage" style="font-size:17px; color:#e5e7eb;">Indonesia Lover!</div>
      </div>
    </div>

    <section class="card score">
      <strong>スコア：</strong><span id="scoreText">0問中0問正解</span><br />
      次の拡張候補：音声入力、英語・中国語モード、空港・ホテル・飲食店カテゴリ。
    </section>
  </main>

  <script>
    const quizData = [
      { japanese: "こんにちは", native: "Halo", pronunciation: "ハロ", meaning: "こんにちは" },
      { japanese: "ありがとう", native: "Terima kasih", pronunciation: "トゥリマ カシ", meaning: "ありがとうございます" },
      { japanese: "ごめんね", native: "Maaf, ya", pronunciation: "マアフ ヤ", meaning: "ごめんね / すみません" }
    ];

    let currentQuestion = null;
    let selectedItem = null;
    let selectedButton = null;
    let answered = false;
    let totalCount = 0;
    let streakCount = 0;
    let correctCount = 0;
    let audioContext = null;

    const questionEl = document.getElementById("question");
    const choicesEl = document.getElementById("choices");
    const answerButtonWrap = document.getElementById("answerButtonWrap");
    const submitButton = document.getElementById("submitButton");
    const feedbackEl = document.getElementById("feedback");
    const answerBoxEl = document.getElementById("answerBox");
    const nativeEl = document.getElementById("native");
    const pronunciationEl = document.getElementById("pronunciation");
    const meaningEl = document.getElementById("meaning");
    const speakButton = document.getElementById("speakButton");
    const nextButton = document.getElementById("nextButton");
    const scoreText = document.getElementById("scoreText");
    const streakPopup = document.getElementById("streakPopup");
    const streakPopupInner = document.getElementById("streakPopupInner");
    const streakEmoji = document.getElementById("streakEmoji");
    const streakTitle = document.getElementById("streakTitle");
    const streakMessage = document.getElementById("streakMessage");

    function shuffle(array) {
      return [...array].sort(() => Math.random() - 0.5);
    }

    function createQuestion() {
      answered = false;
      nextButton.disabled = true;
      selectedItem = null;
      selectedButton = null;
      currentQuestion = quizData[Math.floor(Math.random() * quizData.length)];
      const choices = shuffle(quizData);

      questionEl.textContent = currentQuestion.japanese;
      choicesEl.innerHTML = "";
      answerButtonWrap.className = "answer-button-wrap";
      feedbackEl.className = "feedback";
      feedbackEl.textContent = "";
      answerBoxEl.className = "answer-box";

      choices.forEach((item) => {
        const button = document.createElement("button");
        button.className = "choice";
        button.textContent = item.native;
        button.addEventListener("click", () => selectChoice(button, item));
        choicesEl.appendChild(button);
      });
    }

    function selectChoice(button, item) {
      if (answered) return;

      selectedItem = item;
      selectedButton = button;

      document.querySelectorAll(".choice").forEach((choiceButton) => {
        choiceButton.classList.remove("selected");
      });

      button.classList.add("selected");
      answerButtonWrap.className = "answer-button-wrap show";
    }

    function submitAnswer() {
      if (!selectedItem || answered) return;

      answered = true;
      totalCount++;

      const isCorrect = selectedItem.native === currentQuestion.native;
      playEffectSound(isCorrect);

      document.querySelectorAll(".choice").forEach((choiceButton) => {
        choiceButton.disabled = true;
        if (choiceButton.textContent === currentQuestion.native) {
          choiceButton.classList.add("correct");
        }
      });

      if (isCorrect) {
        correctCount++;
        streakCount++;

        if (streakCount === 3) {
          showStreakPopup("🔥", "3連続正解！", "Indonesia Beginner!");
        }

        if (streakCount === 5) {
          showStreakPopup("🌏", "5連続正解！", "Indonesia Explorer!");
        }
        selectedButton.classList.add("correct");
        feedbackEl.className = "feedback correct show";
        feedbackEl.textContent = "正解！いい感じです。";
      } else {
        selectedButton.classList.add("wrong");
        streakCount = 0;
        feedbackEl.className = "feedback wrong show";
        feedbackEl.textContent = "惜しい。正解はこちらです。";
      }

      answerButtonWrap.className = "answer-button-wrap";
      nextButton.disabled = false;
      showAnswer();
      updateScore();
    }

    function showAnswer() {
      nativeEl.textContent = currentQuestion.native;
      pronunciationEl.textContent = currentQuestion.pronunciation;
      meaningEl.textContent = currentQuestion.meaning;
      answerBoxEl.className = "answer-box show";
    }

    function getAudioContext() {
      if (!audioContext) {
        audioContext = new (window.AudioContext || window.webkitAudioContext)();
      }
      return audioContext;
    }

    function playTone(frequency, startTime, duration, volume) {
      const ctx = getAudioContext();
      const oscillator = ctx.createOscillator();
      const gain = ctx.createGain();

      oscillator.type = "sine";
      oscillator.frequency.setValueAtTime(frequency, startTime);
      gain.gain.setValueAtTime(0.0001, startTime);
      gain.gain.exponentialRampToValueAtTime(volume, startTime + 0.015);
      gain.gain.exponentialRampToValueAtTime(0.0001, startTime + duration);

      oscillator.connect(gain);
      gain.connect(ctx.destination);
      oscillator.start(startTime);
      oscillator.stop(startTime + duration + 0.02);
    }

    function playEffectSound(isCorrect) {
      const ctx = getAudioContext();
      const now = ctx.currentTime;

      if (isCorrect) {
        playTone(523.25, now, 0.12, 0.13);
        playTone(659.25, now + 0.10, 0.14, 0.13);
        playTone(783.99, now + 0.20, 0.18, 0.14);
        playTone(1046.5, now + 0.32, 0.28, 0.16);
      } else {
        playTone(220, now, 0.12, 0.055);
        playTone(180, now + 0.11, 0.16, 0.045);
      }
    }

    function speakAnswer() {
      if (!currentQuestion) return;
      window.speechSynthesis.cancel();
      const utterance = new SpeechSynthesisUtterance(currentQuestion.native);
      utterance.lang = "id-ID";
      utterance.rate = 0.82;
      window.speechSynthesis.speak(utterance);
    }

    function showStreakPopup(emoji, title, message) {
      streakEmoji.textContent = emoji;
      streakTitle.textContent = title;
      streakMessage.textContent = message;

      streakPopup.style.display = "flex";

      requestAnimationFrame(() => {
        streakPopupInner.style.transform = "scale(1)";
        streakPopupInner.style.opacity = "1";
      });

      setTimeout(() => {
        streakPopupInner.style.transform = "scale(0.82)";
        streakPopupInner.style.opacity = "0";

        setTimeout(() => {
          streakPopup.style.display = "none";
        }, 320);
      }, 1800);
    }

    function updateScore() {
      scoreText.textContent = `${totalCount}問中${correctCount}問正解 ｜ 現在 ${streakCount} 連続正解中 🔥`;
    }

    submitButton.addEventListener("click", submitAnswer);
    speakButton.addEventListener("click", speakAnswer);
    nextButton.addEventListener("click", () => {
      if (!answered) return;
      createQuestion();
    });

    createQuestion();
  </script>
</body>
</html>
