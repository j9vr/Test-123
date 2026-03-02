<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SVS System</title>
<link rel="stylesheet" href="style.css">
<script src="https://cdn.jsdelivr.net/npm/tone@14.8.39/build/Tone.min.js"></script>
</head>
<body>
<header>
  <h1>🎶 SVS Music & Video System</h1>
</header>

<nav>
  <button onclick="showSection('music')">Music</button>
  <button onclick="showSection('prompts')">Lyrics / Prompts</button>
  <button onclick="showSection('videos')">Video Ideas</button>
</nav>

<main>
  <!-- Music Section -->
  <section id="music" class="section">
    <h2>Music Creator</h2>
    <div id="trackGrid"></div>
    <button id="playBtn">Play / Stop</button>
  </section>

  <!-- ChatGPT Prompts Section -->
  <section id="prompts" class="section" style="display:none;">
    <h2>Lyrics / Song Ideas</h2>
    <textarea id="lyricPrompt" placeholder="Enter prompt for lyrics or melodies..."></textarea>
    <button onclick="generateLyrics()">Generate Lyrics</button>
    <pre id="lyricOutput"></pre>
  </section>

  <!-- Video Ideas Section -->
  <section id="videos" class="section" style="display:none;">
    <h2>Video Ideas</h2>
    <textarea id="videoPrompt" placeholder="Enter prompt for video ideas..."></textarea>
    <button onclick="generateVideoIdeas()">Generate Video Ideas</button>
    <pre id="videoOutput"></pre>
  </section>
</main>

<script>
// =================== Navigation ===================
function showSection(sectionId) {
  document.querySelectorAll('.section').forEach(sec => sec.style.display = 'none');
  document.getElementById(sectionId).style.display = 'block';
}

// =================== Music Grid ===================
const synth = new Tone.Synth().toDestination();
let playing = false;

// Simple grid: 4 notes
const notes = ["C4", "E4", "G4", "B4"];
const trackGrid = document.getElementById("trackGrid");

notes.forEach((note, i) => {
  const btn = document.createElement("button");
  btn.innerText = note;
  btn.onclick = () => synth.triggerAttackRelease(note, "8n");
  trackGrid.appendChild(btn);
});

document.getElementById("playBtn").onclick = async () => {
  if (!playing) {
    await Tone.start();
    playing = true;
    Tone.Transport.bpm.value = 90;
    Tone.Transport.start();
  } else {
    Tone.Transport.stop();
    playing = false;
  }
};

// =================== ChatGPT Prompts ===================
// Replace with your OpenAI API Key
const OPENAI_API_KEY = "YOUR_OPENAI_API_KEY";

async function generateLyrics() {
  const prompt = document.getElementById("lyricPrompt").value;
  const output = document.getElementById("lyricOutput");
  output.textContent = "Generating...";
  try {
    const res = await fetch("https://api.openai.com/v1/chat/completions", {
      method:"POST",
      headers:{
        "Content-Type":"application/json",
        "Authorization":`Bearer ${OPENAI_API_KEY}`
      },
      body: JSON.stringify({
        model: "gpt-4",
        messages: [{role:"user", content: prompt}],
        max_tokens: 300
      })
    });
    const data = await res.json();
    output.textContent = data.choices[0].message.content;
  } catch (err) {
    output.textContent = "Error: " + err;
  }
}

async function generateVideoIdeas() {
  const prompt = document.getElementById("videoPrompt").value;
  const output = document.getElementById("videoOutput");
  output.textContent = "Generating...";
  try {
    const res = await fetch("https://api.openai.com/v1/chat/completions", {
      method:"POST",
      headers:{
        "Content-Type":"application/json",
        "Authorization":`Bearer ${OPENAI_API_KEY}`
      },
      body: JSON.stringify({
        model: "gpt-4",
        messages: [{role:"user", content: prompt}],
        max_tokens: 300
      })
    });
    const data = await res.json();
    output.textContent = data.choices[0].message.content;
  } catch (err) {
    output.textContent = "Error: " + err;
  }
}
</script>

<style>
body { margin:0; font-family:Arial,sans-serif; background:#111; color:#fff; }
header { text-align:center; padding:1rem; background:#222; }
nav { text-align:center; margin:1rem 0; }
nav button { margin:0 5px; padding:0.5rem 1rem; cursor:pointer; }
main { padding:1rem; }
button { background:#444; color:#fff; border:none; padding:0.5rem 1rem; margin:0.2rem; cursor:pointer; border-radius:5px; }
button:hover { background:#666; }
textarea { width:100%; height:80px; margin:0.5rem 0; background:#222; color:#fff; border:none; padding:0.5rem; border-radius:5px; }
pre { background:#222; padding:0.5rem; border-radius:5px; white-space:pre-wrap; word-wrap:break-word; }
#trackGrid button { width:60px; height:60px; font-size:16px; }
</style>
</body>
</html>
