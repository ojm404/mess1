<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Marvelless Six Degrees</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,600;9..144,700&family=IBM+Plex+Sans:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --stage: #431059;
    --panel: #B71A9F;
    --panel-line: #000000;
    --gold: #E7B24C;
    --gold-dim: #8A6C2E;
    --red: #B23A2D;
    --moss: #6F9F6D;
    --paper: #ffffff;
    --muted: #F6C1EE;
    color-scheme: dark;
    box-sizing: border-box;
    padding-top: env(safe-area-inset-top, 0px);
    padding-bottom: env(safe-area-inset-bottom, 0px);
  }
  *, *::before, *::after { box-sizing: inherit; }
  html { scroll-padding-top: env(safe-area-inset-top, 0px); }
  html, body { height: 100%; }

  body {
    margin: 0;
    background: var(--stage);
    background-image:
      radial-gradient(circle at 15% 0%, rgba(231,178,76,0.05), transparent 45%),
      radial-gradient(circle at 85% 100%, rgba(231,178,76,0.04), transparent 45%);
    color: var(--paper);
    font-family: 'IBM Plex Sans', system-ui, sans-serif;
    min-height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 28px 16px 48px;
  }

  .wrap { width: 100%; max-width: 640px; }

  /* ---------- Marquee header ---------- */
  .marquee {
    position: relative;
    text-align: center;
    padding: 22px 12px 26px;
    margin-bottom: 22px;
  }
  .marquee h1 {
    font-family: 'Fraunces', serif;
    font-optical-sizing: auto;
    font-weight: 700;
    font-size: clamp(2rem, 7vw, 2.9rem);
    letter-spacing: 0.04em;
    margin: 0;
    color: var(--paper);
    text-shadow: 0 0 18px rgba(231,178,76,0.18);
  }
  .marquee .sub {
    margin-top: 6px;
    color: var(--muted);
    font-size: 0.92rem;
  }
  .bulbs {
    display: flex;
    justify-content: center;
    gap: 10px;
    margin-top: 14px;
  }
  .bulb {
    width: 7px; height: 7px;
    border-radius: 50%;
    background: var(--gold-dim);
    transition: background 0.25s, box-shadow 0.25s;
  }
  .marquee.won .bulb {
    background: var(--gold);
    box-shadow: 0 0 8px 2px rgba(231,178,76,0.7);
    animation: twinkle 1.1s ease-in-out infinite;
  }
  .marquee.won .bulb:nth-child(odd) { animation-delay: 0.4s; }
  @keyframes twinkle {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.35; }
  }
  .marquee.lost h1 { text-shadow: 0 0 18px rgba(178,58,45,0.28); }
  .marquee.lost .bulb {
    background: var(--red);
    box-shadow: none;
  }

  /* ---------- Stats chips (guesses / timer) ---------- */
  .stats {
    display: flex;
    gap: 10px;
    margin-bottom: 18px;
  }
  .chip {
    flex: 1;
    background: var(--panel);
    border: 1px solid var(--panel-line);
    border-radius: 3px;
    padding: 8px 12px;
    text-align: center;
  }
  .chip .chip-label {
    font-size: 0.62rem;
    letter-spacing: 0.08em;
    color: var(--muted);
    margin-bottom: 2px;
  }
  .chip .chip-value {
    font-family: 'Fraunces', serif;
    font-weight: 600;
    font-size: 1.08rem;
  }
  .chip.warn .chip-value { color: var(--red); }
  .chip.spent { opacity: 0.6; }

  /* ---------- Ticket stubs (start / end) ---------- */
  .tickets {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 22px;
  }
  .ticket {
    flex: 1;
    position: relative;
    background: var(--panel);
    border: 1px solid var(--panel-line);
    border-radius: 3px;
    padding: 14px 16px;
    text-align: center;
    overflow: hidden;
  }
  .ticket::before, .ticket::after {
    content: "";
    position: absolute;
    top: 50%;
    width: 14px; height: 14px;
    background: var(--stage);
    border-radius: 50%;
    transform: translateY(-50%);
  }
  .ticket::before { left: -7px; }
  .ticket::after { right: -7px; }
  .ticket .label {
    font-size: 0.68rem;
    letter-spacing: 0.08em;
    color: var(--gold);
    margin-bottom: 4px;
  }
  .ticket .name {
    font-family: 'Fraunces', serif;
    font-weight: 600;
    font-size: 1.05rem;
    line-height: 1.2;
  }
  .tickets .arrow {
    color: var(--muted);
    font-size: 1.3rem;
    flex-shrink: 0;
  }

  /* ---------- Filmstrip ---------- */
  .filmstrip {
    background: var(--panel);
    border: 1px solid var(--panel-line);
    border-radius: 4px;
    padding: 16px;
    margin-bottom: 18px;
    min-height: 96px;
  }
  .filmstrip .empty {
    color: var(--muted);
    font-size: 0.88rem;
    text-align: center;
    padding: 20px 8px;
  }
  .frames {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    align-items: center;
  }
  .frame {
    position: relative;
    background: var(--stage);
    border: 1px solid var(--panel-line);
    border-radius: 2px;
    padding: 10px 12px 8px;
    min-width: 108px;
    text-align: center;
    animation: Connect 0.35s ease-out;
  }
  .frame::before, .frame::after {
    content: "";
    position: absolute;
    left: 0; right: 0;
    height: 6px;
    background-image: radial-gradient(circle, var(--panel-line) 1.6px, transparent 1.7px);
    background-size: 12px 6px;
    background-repeat: repeat-x;
    background-position: 4px center;
  }
  .frame::before { top: 2px; }
  .frame::after { bottom: 2px; }
  .frame .kind {
    font-size: 0.6rem;
    letter-spacing: 0.06em;
    color: var(--muted);
    margin-bottom: 2px;
  }
  .frame.actor .kind { color: var(--gold); }
  .frame .nm {
    font-size: 0.82rem;
    font-weight: 500;
    line-height: 1.25;
  }
  .frame-link {
    color: var(--panel-line);
    font-size: 1rem;
  }
  @keyframes Connect {
    from { opacity: 0; transform: translateX(-8px); }
    to { opacity: 1; transform: translateX(0); }
  }

  /* ---------- Ticket-window input ---------- */
  .window {
    background: var(--panel);
    border: 1px solid var(--panel-line);
    border-radius: 4px;
    padding: 14px;
  }
  .window .hint {
    font-size: 0.78rem;
    color: var(--muted);
    margin-bottom: 8px;
  }
  .window .hint b { color: var(--paper); font-weight: 500; }
  form { display: flex; gap: 8px; }
  input[type="text"] {
    flex: 1;
    background: var(--stage);
    border: 1px solid var(--panel-line);
    border-radius: 3px;
    padding: 11px 12px;
    color: var(--paper);
    font-family: inherit;
    font-size: 0.95rem;
  }
  input[type="text"]:focus {
    outline: 2px solid var(--gold);
    outline-offset: 1px;
  }
  button {
    background: var(--gold);
    color: #241a08;
    border: none;
    border-radius: 3px;
    padding: 0 18px;
    font-family: inherit;
    font-weight: 600;
    font-size: 0.9rem;
    cursor: pointer;
    transition: transform 0.1s, background 0.15s;
  }
  button:hover { background: #f0c264; }
  button:active { transform: scale(0.97); }
  button:focus-visible { outline: 2px solid var(--paper); outline-offset: 2px; }

  .status {
    margin-top: 10px;
    font-size: 0.85rem;
    min-height: 1.2em;
  }
  .status.err { color: #e07a6d; }
  .status.win { color: var(--moss); font-weight: 500; }

  .meta {
    display: flex;
    justify-content: space-between;
    margin-top: 14px;
    font-size: 0.78rem;
    color: var(--muted);
  }
  .meta button.ghost {
    background: none;
    border: 1px solid var(--panel-line);
    color: var(--muted);
    padding: 5px 12px;
    font-size: 0.78rem;
    font-weight: 400;
  }
  .meta button.ghost:hover { color: var(--paper); border-color: var(--gold-dim); }

  .shake { animation: shake 0.32s; }
  @keyframes shake {
    0%, 100% { transform: translateX(0); }
    25% { transform: translateX(-5px); }
    75% { transform: translateX(5px); }
  }

  @media (prefers-reduced-motion: reduce) {
    .marquee.won .bulb, .frame, .shake { animation: none !important; }
  }

  @media (max-width: 420px) {
    .tickets { flex-direction: column; }
    .tickets .arrow { transform: rotate(90deg); }
  }
</style>
</head>
<body>

<div class="wrap">
  <div class="marquee" id="marquee">
    <h1>Marvelless Six Degrees</h1>
    <div class="sub">Connect the two actors through their shared films.</div>
    <div class="bulbs">
      <div class="bulb"></div><div class="bulb"></div><div class="bulb"></div>
      <div class="bulb"></div><div class="bulb"></div><div class="bulb"></div>
      <div class="bulb"></div>
    </div>
  </div>

  <div class="tickets">
    <div class="ticket">
      <div class="label">START</div>
      <div class="name" id="startName">—</div>
    </div>
    <div class="arrow">→</div>
    <div class="ticket">
      <div class="label">FINISH</div>
      <div class="name" id="endName">—</div>
    </div>
  </div>

  <div class="stats">
    <div class="chip" id="guessChip">
      <div class="chip-label">GUESSES</div>
      <div class="chip-value" id="guessValue">0 / 5</div>
    </div>
    <div class="chip" id="timeChip">
      <div class="chip-label">TIME</div>
      <div class="chip-value" id="timeValue">0:00</div>
    </div>
  </div>

  <div class="filmstrip">
    <div class="empty" id="filmEmpty">No links connected yet — start typing below.</div>
    <div class="frames" id="frames"></div>
  </div>

  <div class="window">
    <div class="hint" id="hint">Loading today's puzzle…</div>
    <form id="guessForm">
      <input type="text" id="guessInput" list="options" autocomplete="off" placeholder="Type a title or name…" disabled>
      <datalist id="options"></datalist>
      <button type="submit" disabled>Connect</button>
    </form>
    <div class="status" id="status"></div>
    <div class="meta">
      <span id="tally">0 links</span>
      <button type="button" class="ghost" id="newGame" disabled>New puzzle</button>
    </div>
  </div>
</div>

<script>
(function () {
  "use strict";

  /* ---------------------------------------------------------------
   * DATA
   * Loaded from actors.json / movies.json / pairs.json (see init()
   * below). These start undefined and get filled in once the fetch
   * resolves — nothing in this file talks to TMDB or Node directly.
   * That work happens in build-graph.js, a completely separate
   * script you run locally, never in the browser.
   * ------------------------------------------------------------- */
  let actors, movies, pairs;

  /* ---------------------------------------------------------------
   * STATE
   * ------------------------------------------------------------- */
  const MAX_GUESSES = 5;
  let startId, endId, chain; // chain: array of {type:'actor'|'movie', id}
  let guessesUsed, gameOver;
  let timerInterval, startTime;

  function norm(s) {
    return s.trim().toLowerCase();
  }

  function formatTime(ms) {
    const totalSecs = Math.floor(ms / 1000);
    const m = Math.floor(totalSecs / 60);
    const s = totalSecs % 60;
    return `${m}:${String(s).padStart(2, "0")}`;
  }

  function startTimer() {
    startTime = Date.now();
    updateTimerDisplay();
    timerInterval = setInterval(updateTimerDisplay, 1000);
  }

  function stopTimer() {
    clearInterval(timerInterval);
  }

  function updateTimerDisplay() {
    document.getElementById("timeValue").textContent = formatTime(Date.now() - startTime);
  }

  function updateStatsDisplay() {
    document.getElementById("guessValue").textContent = `${guessesUsed} / ${MAX_GUESSES}`;
    const chip = document.getElementById("guessChip");
    chip.classList.toggle("warn", !gameOver && guessesUsed === MAX_GUESSES - 1);
    chip.classList.toggle("spent", gameOver);
    document.getElementById("timeChip").classList.toggle("spent", gameOver);
  }

  function newPuzzle() {
    const pick = pairs[Math.floor(Math.random() * pairs.length)];
    startId = pick.a;
    endId = pick.b;
    chain = [{ type: "actor", id: startId }];
    guessesUsed = 0;
    gameOver = false;

    document.getElementById("startName").textContent = actors[startId].name;
    document.getElementById("endName").textContent = actors[endId].name;
    document.getElementById("status").textContent = "";
    document.getElementById("status").className = "status";
    document.getElementById("marquee").classList.remove("won", "lost");
    document.getElementById("guessInput").disabled = false;
    document.querySelector("button[type=submit]").disabled = false;

    stopTimer();
    startTimer();
    updateStatsDisplay();
    render();
  }

  function lastNode() {
    return chain[chain.length - 1];
  }

  function currentOptions() {
    const last = lastNode();
    if (last.type === "actor") {
      return actors[last.id].movies.map((id) => movies[id].title);
    }
    return movies[last.id].cast.map((id) => actors[id].name);
  }

  function render() {
    const framesEl = document.getElementById("frames");
    const emptyEl = document.getElementById("filmEmpty");
    framesEl.innerHTML = "";
    emptyEl.style.display = chain.length <= 1 ? "block" : "none";

    chain.forEach((node, i) => {
      if (i > 0) {
        const link = document.createElement("span");
        link.className = "frame-link";
        link.textContent = "⟶";
        framesEl.appendChild(link);
      }
      const f = document.createElement("div");
      f.className = "frame " + node.type;
      const kind = document.createElement("div");
      kind.className = "kind";
      kind.textContent = node.type === "actor" ? "ACTOR" : "FILM";
      const nm = document.createElement("div");
      nm.className = "nm";
      nm.textContent = node.type === "actor" ? actors[node.id].name : movies[node.id].title;
      f.appendChild(kind);
      f.appendChild(nm);
      framesEl.appendChild(f);
    });

    const last = lastNode();
    const hintEl = document.getElementById("hint");
    if (last.type === "actor") {
      hintEl.innerHTML = `Name a film <b>${actors[last.id].name}</b> was in.`;
    } else {
      hintEl.innerHTML = `Name someone in <b>${movies[last.id].title}</b>.`;
    }

    const dl = document.getElementById("options");
    dl.innerHTML = "";
    currentOptions().forEach((name) => {
      const opt = document.createElement("option");
      opt.value = name;
      dl.appendChild(opt);
    });

    document.getElementById("tally").textContent =
      (chain.length - 1) + (chain.length - 1 === 1 ? " link" : " links");
  }

  function flashError(msg) {
    const statusEl = document.getElementById("status");
    statusEl.textContent = msg;
    statusEl.className = "status err";
    const win = document.querySelector(".window");
    win.classList.remove("shake");
    void win.offsetWidth; // restart animation
    win.classList.add("shake");
  }

  function win() {
    gameOver = true;
    stopTimer();
    document.getElementById("status").textContent =
      `Connected in ${guessesUsed} guesses, ${formatTime(Date.now() - startTime)}. Nicely done.`;
    document.getElementById("status").className = "status win";
    document.getElementById("marquee").classList.add("won");
    document.getElementById("guessInput").disabled = true;
    document.querySelector("button[type=submit]").disabled = true;
    updateStatsDisplay();
    try {
      const key = `sixdegrees-best-${startId}-${endId}`;
      const prevBest = parseInt(localStorage.getItem(key) || "999", 10);
      if (guessesUsed < prevBest) {
        localStorage.setItem(key, String(guessesUsed));
      }
    } catch (e) {
      /* storage unavailable — fine, best score just won't persist */
    }
  }

  function lose() {
    gameOver = true;
    stopTimer();
    document.getElementById("status").textContent =
      `Out of guesses. ${actors[startId].name} and ${actors[endId].name} stay unconnected today.`;
    document.getElementById("status").className = "status err";
    document.getElementById("marquee").classList.add("lost");
    document.getElementById("guessInput").disabled = true;
    document.querySelector("button[type=submit]").disabled = true;
    updateStatsDisplay();
  }

  function submitGuess(raw) {
    if (gameOver) return;
    const guess = norm(raw);
    if (!guess) return;

    const last = lastNode();
    let matchId = null;

    if (last.type === "actor") {
      matchId = actors[last.id].movies.find(
        (id) => norm(movies[id].title) === guess
      );
      if (matchId !== undefined) {
        chain.push({ type: "movie", id: matchId });
      }
    } else {
      matchId = movies[last.id].cast.find(
        (id) => norm(actors[id].name) === guess
      );
      if (matchId !== undefined) {
        chain.push({ type: "actor", id: matchId });
      }
    }

    guessesUsed += 1;

    if (matchId === undefined) {
      updateStatsDisplay();
      if (guessesUsed >= MAX_GUESSES) {
        lose();
      } else {
        flashError("That doesn't connect. Check the spelling, or try someone else in the credits.");
      }
      return;
    }

    render();
    updateStatsDisplay();

    if (last.type === "movie" && matchId === endId) {
      win();
    } else if (guessesUsed >= MAX_GUESSES) {
      lose();
    } else {
      document.getElementById("status").textContent = "";
      document.getElementById("status").className = "status";
    }
  }

  document.getElementById("guessForm").addEventListener("submit", (e) => {
    e.preventDefault();
    const input = document.getElementById("guessInput");
    submitGuess(input.value);
    input.value = "";
    input.focus();
  });

  document.getElementById("newGame").addEventListener("click", newPuzzle);

  /* ---------------------------------------------------------------
   * INIT — loads the three JSON files, then starts the first puzzle.
   * Replace the three fetch() URLs below with wherever you end up
   * hosting actors.json / movies.json / pairs.json (see the GoDaddy
   * Website Builder note about jsDelivr + GitHub).
   * ------------------------------------------------------------- */
  async function init() {
    try {
      const [actorsRes, moviesRes, pairsRes] = await Promise.all([
        fetch('actors.json'),
        fetch('movies.json'),
        fetch('pairs.json'),
      ]);
      [actors, movies, pairs] = await Promise.all([
        actorsRes.json(),
        moviesRes.json(),
        pairsRes.json(),
      ]);
    } catch (err) {
      document.getElementById('status').textContent =
        'Could not load the game data. Try refreshing.';
      document.getElementById('status').className = 'status err';
      return;
    }
    newPuzzle();
  }

  init();
})();
</script>

</body>
</html>
