<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>ひとこと接客フレーズ MVP</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: linear-gradient(135deg, #f7f4ef, #e8f0ff);
      color: #222;
      min-height: 100vh;
      padding: 20px;
    }

    .app {
      max-width: 720px;
      margin: 0 auto;
    }

    header {
      margin-bottom: 20px;
    }

    h1 {
      margin: 0 0 8px;
      font-size: 28px;
      line-height: 1.2;
    }

    .subtitle {
      margin: 0;
      color: #555;
      font-size: 15px;
    }

    .panel {
      background: rgba(255, 255, 255, 0.86);
      border: 1px solid rgba(0, 0, 0, 0.08);
      border-radius: 22px;
      padding: 18px;
      box-shadow: 0 12px 34px rgba(0, 0, 0, 0.08);
      backdrop-filter: blur(10px);
      margin-bottom: 16px;
    }

    .label {
      font-size: 13px;
      font-weight: 700;
      color: #555;
      margin-bottom: 10px;
    }

    .language-grid,
    .phrase-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
    }

    button {
      appearance: none;
      border: none;
      border-radius: 16px;
      background: #fff;
      padding: 14px 12px;
      font-size: 15px;
      font-weight: 700;
      cursor: pointer;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
      transition: transform 0.12s ease, box-shadow 0.12s ease, background 0.12s ease;
    }

    button:active {
      transform: scale(0.97);
    }

    button:hover {
      box-shadow: 0 8px 18px rgba(0, 0, 0, 0.12);
    }

    .language-button.active {
      background: #1f2937;
      color: #fff;
    }

    .phrase-button {
      min-height: 58px;
    }

    .result-card {
      display: none;
    }

    .result-card.show {
      display: block;
    }

    .category {
      display: inline-block;
      background: #eef2ff;
      color: #3730a3;
      font-size: 12px;
      font-weight: 800;
      padding: 6px 10px;
      border-radius: 999px;
      margin-bottom: 12px;
    }

    .native-text {
      font-size: 34px;
      font-weight: 900;
      line-height: 1.25;
      margin-bottom: 8px;
    }

    .pronunciation {
      font-size: 20px;
      font-weight: 700;
      color: #444;
      margin-bottom: 8px;
    }

    .meaning {
      font-size: 16px;
      color: #555;
      margin-bottom: 16px;
    }

    .note {
      background: #fff7ed;
      border-left: 5px solid #fb923c;
      padding: 12px;
      border-radius: 12px;
      color: #5f370e;
      font-size: 14px;
      margin-bottom: 16px;
    }

    .actions {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
    }

    .action-button {
      background: #111827;
      color: #fff;
    }

    .copy-message {
      margin-top: 10px;
      font-size: 13px;
      color: #16803c;
      font-weight: 700;
      display: none;
    }

    .copy-message.show {
      display: block;
    }

    .small {
      font-size: 12px;
      color: #666;
      line-height: 1.6;
    }

    @media (max-width: 560px) {
      body {
        padding: 14px;
      }

      h1 {
        font-size: 24px;
      }

      .language-grid,
      .phrase-grid {
        grid-template-columns: 1fr 1fr;
      }

      .native-text {
        font-size: 28px;
      }
    }
  </style>
</head>
<body>
  <main class="app">
    <header>
      <h1>ひとこと接客フレーズ</h1>
      <p class="subtitle">外国人のお客さんに、相手の言葉で一言ギブするMVP版。</p>
    </header>

    <section class="panel">
      <div class="label">① 言語を選ぶ</div>
      <div id="languageGrid" class="language-grid"></div>
    </section>

    <section class="panel">
      <div class="label">② 場面を選ぶ</div>
      <div id="phraseGrid" class="phrase-grid"></div>
    </section>

    <section id="resultCard" class="panel result-card">
      <div id="category" class="category"></div>
      <div id="nativeText" class="native-text"></div>
      <div id="pronunciation" class="pronunciation"></div>
      <div id="meaning" class="meaning"></div>
      <div id="note" class="note"></div>
      <div class="actions">
        <button id="speakButton" class="action-button">▶ 音声</button>
        <button id="copyButton" class="action-button">📋 コピー</button>
      </div>
      <div id="copyMessage" class="copy-message">コピーしました</div>
    </section>

    <section class="panel">
      <div class="label">MVPメモ</div>
      <p class="small">
        まずは3言語・飲食店向け6フレーズだけ。後から「トイレ案内」「辛さ確認」「アレルギー確認」「旅行モード」「標識・マナー図鑑」を追加できます。
      </p>
    </section>
  </main>

  <script>
    const languages = [
      { id: "id", name: "インドネシア語", flag: "🇮🇩", voiceLang: "id-ID" },
      { id: "en", name: "英語", flag: "🇺🇸", voiceLang: "en-US" },
      { id: "zh", name: "中国語", flag: "🇨🇳", voiceLang: "zh-CN" }
    ];

    const phrases = [
      {
        id: "hello",
        label: "こんにちは",
        category: "あいさつ",
        data: {
          id: { native: "Halo", pronunciation: "ハロ", meaning: "こんにちは", note: "カジュアルで使いやすい挨拶。飲食店でも自然です。" },
          en: { native: "Hello", pronunciation: "ハロー", meaning: "こんにちは", note: "どの場面でも使える基本の挨拶です。" },
          zh: { native: "你好", pronunciation: "ニーハオ", meaning: "こんにちは", note: "中国語の基本挨拶。店頭でも使えます。" }
        }
      },
      {
        id: "thanks",
        label: "ありがとう",
        category: "感謝",
        data: {
          id: { native: "Terima kasih", pronunciation: "トゥリマ カシ", meaning: "ありがとうございます", note: "丁寧で万能。まず覚えるならこれです。" },
          en: { native: "Thank you", pronunciation: "サンキュー", meaning: "ありがとうございます", note: "接客でも日常でも使える基本表現です。" },
          zh: { native: "谢谢", pronunciation: "シエシエ", meaning: "ありがとうございます", note: "短くて伝わりやすい感謝表現です。" }
        }
      },
      {
        id: "wait",
        label: "少々お待ちください",
        category: "接客",
        data: {
          id: { native: "Tunggu sebentar, ya", pronunciation: "トゥング スブンタール ヤ", meaning: "少々お待ちください", note: "ya を付けるとやわらかい印象になります。" },
          en: { native: "Please wait a moment", pronunciation: "プリーズ ウェイト ア モーメント", meaning: "少々お待ちください", note: "丁寧で接客向きです。" },
          zh: { native: "请稍等", pronunciation: "チン シャオ ドン", meaning: "少々お待ちください", note: "店内案内や会計前に使いやすい表現です。" }
        }
      },
      {
        id: "water",
        label: "お水どうぞ",
        category: "提供",
        data: {
          id: { native: "Ini airnya", pronunciation: "イニ アイルニャ", meaning: "こちらがお水です", note: "直訳は『これが水です』。自然な提供表現です。" },
          en: { native: "Here is your water", pronunciation: "ヒア イズ ユア ウォーター", meaning: "お水どうぞ", note: "料理や飲み物を出す時にも応用できます。" },
          zh: { native: "这是您的水", pronunciation: "ヂァー シー ニン ダ シュイ", meaning: "こちらがお水です", note: "您的 は丁寧な『あなたの』です。" }
        }
      },
      {
        id: "payment",
        label: "お会計はこちらです",
        category: "会計",
        data: {
          id: { native: "Pembayarannya di sini", pronunciation: "プンバヤランニャ ディ シニ", meaning: "お支払いはこちらです", note: "di sini は『ここで』という意味です。" },
          en: { native: "Please pay here", pronunciation: "プリーズ ペイ ヒア", meaning: "こちらでお支払いください", note: "シンプルで伝わりやすい表現です。" },
          zh: { native: "请在这里付款", pronunciation: "チン ザイ ヂァーリー フークァン", meaning: "こちらでお支払いください", note: "会計場所を案内する時に便利です。" }
        }
      },
      {
        id: "comeAgain",
        label: "また来てください",
        category: "見送り",
        data: {
          id: { native: "Silakan datang lagi", pronunciation: "シラカン ダタン ラギ", meaning: "また来てください", note: "silakan は丁寧な『どうぞ』のニュアンスがあります。" },
          en: { native: "Please come again", pronunciation: "プリーズ カム アゲイン", meaning: "また来てください", note: "お見送りに使いやすい表現です。" },
          zh: { native: "欢迎再来", pronunciation: "ファンイン ザイライ", meaning: "またお越しください", note: "店を出るお客さんへの自然な一言です。" }
        }
      }
    ];

    let selectedLanguageId = "id";
    let selectedPhrase = null;

    const languageGrid = document.getElementById("languageGrid");
    const phraseGrid = document.getElementById("phraseGrid");
    const resultCard = document.getElementById("resultCard");
    const categoryEl = document.getElementById("category");
    const nativeTextEl = document.getElementById("nativeText");
    const pronunciationEl = document.getElementById("pronunciation");
    const meaningEl = document.getElementById("meaning");
    const noteEl = document.getElementById("note");
    const speakButton = document.getElementById("speakButton");
    const copyButton = document.getElementById("copyButton");
    const copyMessage = document.getElementById("copyMessage");

    function renderLanguages() {
      languageGrid.innerHTML = "";

      languages.forEach((language) => {
        const button = document.createElement("button");
        button.className = "language-button" + (language.id === selectedLanguageId ? " active" : "");
        button.textContent = `${language.flag} ${language.name}`;
        button.addEventListener("click", () => {
          selectedLanguageId = language.id;
          renderLanguages();
          if (selectedPhrase) showPhrase(selectedPhrase);
        });
        languageGrid.appendChild(button);
      });
    }

    function renderPhrases() {
      phraseGrid.innerHTML = "";

      phrases.forEach((phrase) => {
        const button = document.createElement("button");
        button.className = "phrase-button";
        button.textContent = phrase.label;
        button.addEventListener("click", () => showPhrase(phrase));
        phraseGrid.appendChild(button);
      });
    }

    function showPhrase(phrase) {
      selectedPhrase = phrase;
      const item = phrase.data[selectedLanguageId];

      categoryEl.textContent = phrase.category;
      nativeTextEl.textContent = item.native;
      pronunciationEl.textContent = item.pronunciation;
      meaningEl.textContent = item.meaning;
      noteEl.textContent = item.note;
      copyMessage.classList.remove("show");
      resultCard.classList.add("show");
    }

    function getSelectedLanguage() {
      return languages.find((language) => language.id === selectedLanguageId);
    }

    function speakCurrentPhrase() {
      if (!selectedPhrase) return;

      const item = selectedPhrase.data[selectedLanguageId];
      const language = getSelectedLanguage();

      window.speechSynthesis.cancel();
      const utterance = new SpeechSynthesisUtterance(item.native);
      utterance.lang = language.voiceLang;
      utterance.rate = 0.82;
      window.speechSynthesis.speak(utterance);
    }

    async function copyCurrentPhrase() {
      if (!selectedPhrase) return;

      const item = selectedPhrase.data[selectedLanguageId];
      const text = `${item.native}\n${item.pronunciation}\n${item.meaning}`;

      try {
        await navigator.clipboard.writeText(text);
        copyMessage.classList.add("show");
      } catch (error) {
        alert(text);
      }
    }

    speakButton.addEventListener("click", speakCurrentPhrase);
    copyButton.addEventListener("click", copyCurrentPhrase);

    renderLanguages();
    renderPhrases();
    showPhrase(phrases[0]);
  </script>
</body>
</html>
