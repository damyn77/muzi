<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Music Player</title>
  <style>
    :root {
      --bg1: #0f172a;
      --bg2: #111827;
      --panel: rgba(17, 24, 39, 0.8);
      --card: rgba(30, 41, 59, 0.9);
      --accent: #7c3aed;
      --accent-2: #22c55e;
      --text: #e5e7eb;
      --muted: #9ca3af;
      --danger: #ef4444;
      --shadow: 0 25px 60px rgba(0, 0, 0, 0.45);
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      min-height: 100vh;
      display: grid;
      place-items: center;
      font-family: Arial, sans-serif;
      background: radial-gradient(circle at top, #1e293b 0%, var(--bg1) 40%, var(--bg2) 100%);
      color: var(--text);
    }

    .player {
      width: min(420px, 92vw);
      background: var(--panel);
      border: 1px solid rgba(255,255,255,0.08);
      border-radius: 26px;
      box-shadow: var(--shadow);
      padding: 24px 20px 18px;
      backdrop-filter: blur(16px);
    }

    .art {
      position: relative;
      width: 100%;
      height: 220px;
      border-radius: 22px;
      overflow: hidden;
      background:
        linear-gradient(135deg, rgba(124,58,237,0.7), rgba(34,197,94,0.35)),
        url("https://images.unsplash.com/photo-1516280440614-37939bbacd81?auto=format&fit=crop&w=900&q=80") center/cover no-repeat;
      box-shadow: inset 0 0 30px rgba(0,0,0,0.25);
      display: flex;
      align-items: end;
      padding: 18px;
    }

    .art::after {
      content: "";
      position: absolute;
      inset: 0;
      background: linear-gradient(to top, rgba(15,23,42,0.7), rgba(15,23,42,0.08));
    }

    .track-meta {
      position: relative;
      z-index: 1;
    }

    .tiny {
      color: #dbeafe;
      letter-spacing: 0.15em;
      font-size: 10px;
      text-transform: uppercase;
      opacity: 0.8;
    }

    h2 {
      margin: 8px 0 0;
      font-size: clamp(1.4rem, 4vw, 2rem);
      line-height: 1.1;
    }

    .artist {
      margin-top: 6px;
      color: var(--muted);
      font-size: 0.9rem;
    }

    .timeline {
      margin-top: 16px;
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .time-row {
      display: flex;
      justify-content: space-between;
      font-size: 0.75rem;
      color: var(--muted);
    }

    .progress {
      width: 100%;
      height: 8px;
      border-radius: 999px;
      background: rgba(255,255,255,0.12);
      position: relative;
      overflow: hidden;
    }

    .progress-bar {
      position: absolute;
      inset: 0 auto 0 0;
      width: 36%;
      background: linear-gradient(90deg, var(--accent), #a78bfa);
      border-radius: inherit;
    }

    .controls {
      display: grid;
      grid-template-columns: repeat(5, minmax(0, 1fr));
      gap: 12px;
      margin-top: 18px;
    }

    .control {
      border: none;
      border-radius: 16px;
      height: 52px;
      cursor: pointer;
      background: rgba(255,255,255,0.05);
      color: var(--text);
      font-size: 1.1rem;
      font-weight: 700;
      transition: transform 0.2s ease, background 0.2s ease, box-shadow 0.2s ease;
      box-shadow: inset 0 0 0 1px rgba(255,255,255,0.04);
    }

    .control:hover {
      transform: translateY(-2px);
      background: rgba(255,255,255,0.09);
    }

    .control.primary {
      background: linear-gradient(135deg, var(--accent), #8b5cf6);
      color: white;
      box-shadow: 0 10px 18px rgba(124,58,237,0.35);
    }

    .control.success {
      background: linear-gradient(135deg, var(--accent-2), #34d399);
      color: white;
    }

    .control.danger {
      background: rgba(239,68,68,0.18);
      color: #fca5a5;
    }

    .control.active {
      background: rgba(96, 165, 250, 0.18);
      color: #bfdbfe;
      box-shadow: inset 0 0 0 1px rgba(147, 197, 253, 0.4);
    }

    .control:active {
      transform: translateY(1px) scale(0.98);
    }

    .footer-row {
      margin-top: 18px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 0.78rem;
      color: var(--muted);
      padding-top: 8px;
      border-top: 1px solid rgba(255,255,255,0.08);
    }

    .pill {
      background: rgba(34,197,94,0.14);
      color: #bbf7d0;
      border: 1px solid rgba(34,197,94,0.25);
      padding: 6px 10px;
      border-radius: 999px;
      font-weight: 600;
    }

    .hidden {
      display: none;
    }

    @media (max-width: 420px) {
      .controls {
        grid-template-columns: repeat(3, minmax(0, 1fr));
      }
    }
  </style>
</head>
<body>
  <div class="player">
    <div class="art">
      <div class="track-meta">
        <div class="tiny">Now playing</div>
        <h2 id="title">Midnight Echo</h2>
        <div class="artist" id="artist">Luna Waves</div>
      </div>
    </div>

    <div class="timeline">
      <div class="time-row">
        <span id="currentTime">0:00</span>
        <span id="duration">3:42</span>
      </div>
      <div class="progress">
        <div class="progress-bar" id="progressBar"></div>
      </div>
    </div>

    <div class="controls">
      <button class="control" data-action="prev" title="Previous">⏮</button>
      <button class="control" data-action="shuffle" title="Shuffle">🔀</button>
      <button class="control" data-action="back" title="Back 10s">⏪</button>
      <button class="control primary" data-action="play" title="Play / Pause">▶</button>
      <button class="control" data-action="forward" title="Forward 10s">⏩</button>

      <button class="control danger" data-action="stop" title="Stop">■</button>
      <button class="control" data-action="repeat" title="Repeat">🔁</button>
      <button class="control" data-action="volDown" title="Volume down">🔉</button>
      <button class="control" data-action="mute" title="Mute">🔇</button>
      <button class="control" data-action="volUp" title="Volume up">🔊</button>

      <button class="control" data-action="favorite" title="Favorite">♡</button>
      <button class="control" data-action="lyrics" title="Lyrics">♫</button>
      <button class="control" data-action="queue" title="Queue">☰</button>
    </div>

    <div class="footer-row">
      <span id="statusText">Ready</span>
      <span class="pill" id="modePill">Stereo</span>
    </div>
  </div>

  <audio id="audio" preload="metadata"></audio>

  <script>
    const tracks = [
      { title: "Midnight Echo", artist: "Luna Waves", src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" },
      { title: "Neon Skyline", artist: "Aster Bloom", src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3" },
      { title: "Velvet Drift", artist: "Harbor Lights", src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3" }
    ];

    const audio = document.getElementById("audio");
    const titleEl = document.getElementById("title");
    const artistEl = document.getElementById("artist");
    const currentTimeEl = document.getElementById("currentTime");
    const durationEl = document.getElementById("duration");
    const progressBar = document.getElementById("progressBar");
    const statusText = document.getElementById("statusText");
    const modePill = document.getElementById("modePill");
    const playButton = document.querySelector('[data-action="play"]');

    let currentTrackIndex = 0;
    let isPlaying = false;
    let isShuffle = false;
    let isRepeat = false;
    let isMuted = false;
    let isFavorite = false;

    function formatTime(seconds) {
      if (!Number.isFinite(seconds) || seconds < 0) return "0:00";
      const mins = Math.floor(seconds / 60);
      const secs = Math.floor(seconds % 60);
      return `${mins}:${String(secs).padStart(2, "0")}`;
    }

    function loadTrack(index) {
      currentTrackIndex = (index + tracks.length) % tracks.length;
      const track = tracks[currentTrackIndex];
      titleEl.textContent = track.title;
      artistEl.textContent = track.artist;
      statusText.textContent = "Loading...";
      audio.src = track.src;
      audio.load();
      if (isPlaying) {
        audio.play().catch(() => {});
      }
    }

    function updatePlayButton() {
      playButton.textContent = isPlaying ? "❚❚" : "▶";
      playButton.title = isPlaying ? "Pause" : "Play";
    }

    function togglePlay() {
      if (audio.src) {
        if (isPlaying) {
          audio.pause();
          isPlaying = false;
          statusText.textContent = "Paused";
        } else {
          audio.play().catch(() => {
            statusText.textContent = "Tap play again";
          });
          isPlaying = true;
          statusText.textContent = "Playing";
        }
        updatePlayButton();
      } else {
        loadTrack(currentTrackIndex);
        isPlaying = true;
        updatePlayButton();
      }
    }

    function playNext() {
      loadTrack(isShuffle ? Math.floor(Math.random() * tracks.length) : currentTrackIndex + 1);
      isPlaying = true;
      updatePlayButton();
      statusText.textContent = "Playing";
    }

    function playPrev() {
      loadTrack(currentTrackIndex - 1);
      isPlaying = true;
      updatePlayButton();
      statusText.textContent = "Playing";
    }

    function toggleRepeat() {
      isRepeat = !isRepeat;
      audio.loop = isRepeat;
      document.querySelector('[data-action="repeat"]').classList.toggle("active", isRepeat);
      statusText.textContent = isRepeat ? "Repeat on" : "Repeat off";
      modePill.textContent = isRepeat ? "Repeat" : "Stereo";
    }

    function toggleShuffle() {
      isShuffle = !isShuffle;
      document.querySelector('[data-action="shuffle"]').classList.toggle("active", isShuffle);
      statusText.textContent = isShuffle ? "Shuffle on" : "Shuffle off";
    }

    function toggleMute() {
      isMuted = !isMuted;
      audio.muted = isMuted;
      document.querySelector('[data-action="mute"]').classList.toggle("active", isMuted);
      statusText.textContent = isMuted ? "Muted" : "Sound on";
    }

    function setVolume(delta) {
      const newValue = Math.min(1, Math.max(0, audio.volume + delta));
      audio.volume = newValue;
      statusText.textContent = `Volume ${newValue.toFixed(2)}`;
    }

    function stopPlayback() {
      audio.pause();
      audio.currentTime = 0;
      isPlaying = false;
      updatePlayButton();
      statusText.textContent = "Stopped";
    }

    function skip(seconds) {
      if (audio.duration) {
        audio.currentTime = Math.min(audio.duration, Math.max(0, audio.currentTime + seconds));
      }
    }

    function toggleFavorite() {
      isFavorite = !isFavorite;
      const favoriteBtn = document.querySelector('[data-action="favorite"]');
      favoriteBtn.classList.toggle("active", isFavorite);
      favoriteBtn.textContent = isFavorite ? "♥" : "♡";
      statusText.textContent = isFavorite ? "Added to favorites" : "Removed from favorites";
    }

    function toggleLyrics() {
      document.querySelector('[data-action="lyrics"]').classList.toggle("active");
      statusText.textContent = "Lyrics panel";
    }

    function toggleQueue() {
      document.querySelector('[data-action="queue"]').classList.toggle("active");
      statusText.textContent = "Queue opened";
    }

    document.querySelectorAll(".control").forEach(button => {
      button.addEventListener("click", () => {
        const action = button.dataset.action;

        switch (action) {
          case "play":
            togglePlay();
            break;
          case "prev":
            playPrev();
            break;
          case "next":
            playNext();
            break;
          case "shuffle":
            toggleShuffle();
            break;
          case "back":
            skip(-10);
            break;
          case "forward":
            skip(10);
            break;
          case "stop":
            stopPlayback();
            break;
          case "repeat":
            toggleRepeat();
            break;
          case "volDown":
            setVolume(-0.1);
            break;
          case "mute":
            toggleMute();
            break;
          case "volUp":
            setVolume(0.1);
            break;
          case "favorite":
            toggleFavorite();
            break;
          case "lyrics":
            toggleLyrics();
            break;
          case "queue":
            toggleQueue();
            break;
        }
      });
    });

    audio.addEventListener("loadedmetadata", () => {
      durationEl.textContent = formatTime(audio.duration);
      statusText.textContent = "Ready";
    });

    audio.addEventListener("timeupdate", () => {
      const pct = (audio.currentTime / audio.duration) * 100 || 0;
      progressBar.style.width = `${pct}%`;
      currentTimeEl.textContent = formatTime(audio.currentTime);
    });

    audio.addEventListener("ended", () => {
      if (isRepeat) {
        audio.currentTime = 0;
        audio.play();
      } else {
        playNext();
      }
    });

    audio.addEventListener("play", () => {
      isPlaying = true;
      updatePlayButton();
      statusText.textContent = "Playing";
    });

    audio.addEventListener("pause", () => {
      isPlaying = false;
      updatePlayButton();
      if (audio.currentTime > 0 && !audio.ended) {
        statusText.textContent = "Paused";
      }
    });

    loadTrack(0);
    audio.volume = 0.6;
    updatePlayButton();
  </script>
</body>
</html>
