<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1"/>
  <title>시험용 시계 (아날로그/디지털 전환 + 설정 + 알림)</title>
  <style>
    :root { color-scheme: dark; }
    body{
      margin:0;
      min-height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      background:#0b0b0b;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, "Apple SD Gothic Neo", "Noto Sans KR", Arial, sans-serif;
      color:#eaeaea;
    }
    .wrap{
      width:min(860px, 96vw);
      display:flex;
      flex-direction:column;
      gap:14px;
      align-items:center;
      padding:12px 0;
      box-sizing:border-box;
    }
    .panel{
      width:100%;
      display:flex;
      justify-content:space-between;
      align-items:flex-start;
      gap:12px;
      padding:14px 14px;
      background:#121212;
      border:1px solid #222;
      border-radius:14px;
      box-shadow: 0 10px 30px rgba(0,0,0,.35);
      box-sizing:border-box;
      flex-wrap:wrap;
    }
    .left{
      display:flex;
      flex-direction:column;
      gap:10px;
      min-width:min(540px, 100%);
      flex: 1 1 520px;
    }
    .meta{
      display:grid;
      grid-template-columns: 1fr 1fr;
      gap:10px 18px;
      align-items:start;
    }
    .block{
      display:flex;
      flex-direction:column;
      gap:6px;
      padding:10px 12px;
      background:#101010;
      border:1px solid #1f1f1f;
      border-radius:12px;
    }
    .label{ opacity:.75; font-size:12px; }
    .value{
      font-variant-numeric: tabular-nums;
      font-size:18px;
      letter-spacing:.2px;
    }
    .value.big{
      font-size:22px;
      letter-spacing:.6px;
      font-weight:650;
    }

    .controls{
      display:flex;
      flex-wrap:wrap;
      gap:10px 12px;
      align-items:end;
    }
    .field{
      display:flex;
      flex-direction:column;
      gap:6px;
      background:#101010;
      border:1px solid #1f1f1f;
      padding:10px 12px;
      border-radius:12px;
    }
    .field label{
      font-size:12px;
      opacity:.8;
    }
    .field .row{
      display:flex;
      gap:8px;
      align-items:center;
      flex-wrap:wrap;
    }
    input[type="number"]{
      width:84px;
      padding:10px 10px;
      border-radius:10px;
      border:1px solid #2a2a2a;
      background:#0e0e0e;
      color:#eaeaea;
      font-size:14px;
      font-variant-numeric: tabular-nums;
      outline:none;
    }
    input[type="number"]:focus{ border-color:#3a3a3a; }

    .btns{
      display:flex;
      gap:10px;
      align-items:center;
      flex-wrap:wrap;
      justify-content:flex-end;
      flex: 0 0 auto;
    }
    button{
      appearance:none;
      border:1px solid #2a2a2a;
      background:#1a1a1a;
      color:#eaeaea;
      padding:10px 14px;
      border-radius:12px;
      cursor:pointer;
      font-size:14px;
      line-height:1;
      transition: transform .05s ease, background .2s ease, border-color .2s ease;
      user-select:none;
      white-space:nowrap;
    }
    button:hover{ background:#202020; border-color:#3a3a3a; }
    button:active{ transform: translateY(1px); }
    button.primary{ background:#232323; font-weight:650; }
    button.danger{ border-color:#463030; background:#2a1a1a; }
    button:disabled{ opacity:.45; cursor:not-allowed; }

    .stage{
      width:100%;
      display:flex;
      align-items:center;
      justify-content:center;
      padding:6px 0 0;
      box-sizing:border-box;
    }

    canvas{
      width:min(520px, 88vw);
      height:min(520px, 88vw);
      background:#0d0d0d;
      border-radius:999px;
      border:1px solid #222;
      box-shadow: 0 18px 50px rgba(0,0,0,.5);
      display:none;
    }
    .digital{
      width:min(640px, 92vw);
      padding:22px 18px;
      border-radius:18px;
      background:#0d0d0d;
      border:1px solid #222;
      box-shadow: 0 18px 50px rgba(0,0,0,.5);
      display:none;
      text-align:center;
    }
    .digital .time{
      font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
      font-variant-numeric: tabular-nums;
      font-size:88px;
      letter-spacing: 2px;
    }
    .digital .sub{
      margin-top:10px;
      opacity:.75;
      font-size:14px;
      font-variant-numeric: tabular-nums;
    }

    .hint{
      opacity:.7;
      font-size:12px;
      text-align:center;
      max-width: 820px;
      padding: 0 10px;
      box-sizing:border-box;
    }

    /* ===== TLSM Footer (bottom-right) ===== */
    .tlsm-footer{
      position: fixed;
      right: 18px;
      bottom: 14px;
      display: flex;
      flex-direction: column;
      gap: 4px;
      text-align: right;
      pointer-events: none;
      user-select: none;
      opacity: .72;
      z-index: 9999;
    }
    .tlsm-footer .tlsm-tag{
      font-size: 12px;
      letter-spacing: .6px;
    }
    .tlsm-footer .tlsm-copy{
      font-size: 11px;
      opacity: .85;
      letter-spacing: .3px;
    }

    @media (max-width: 520px){
      .digital .time{ font-size:64px; }
      input[type="number"]{ width:78px; }
      .tlsm-footer{ right: 12px; bottom: 10px; }
    }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="panel">
      <div class="left">
        <div class="meta">
          <div class="block">
            <div class="label">시험 시작</div>
            <div class="value" id="startText">13:00:00</div>
          </div>
          <div class="block">
            <div class="label">시험 종료</div>
            <div class="value" id="endText">16:00:00</div>
          </div>
          <div class="block">
            <div class="label">지난 시간</div>
            <div class="value big" id="elapsedText">00:00:00</div>
          </div>
          <div class="block">
            <div class="label">남은 시간</div>
            <div class="value big" id="remainText">03:00:00</div>
          </div>
        </div>

        <div class="controls">
          <div class="field">
            <label>시작 시각 설정</label>
            <div class="row">
              <input type="number" id="startHour" min="0" max="23" value="13" />
              <span class="label">시</span>
              <input type="number" id="startMin" min="0" max="59" value="0" />
              <span class="label">분</span>
            </div>
          </div>

          <div class="field">
            <label>시험 시간 설정</label>
            <div class="row">
              <input type="number" id="durHour" min="0" max="24" value="3" />
              <span class="label">시간</span>
              <input type="number" id="durMin" min="0" max="59" value="0" />
              <span class="label">분</span>
            </div>
          </div>

          <div class="field">
            <label>설정 적용</label>
            <div class="row">
              <button class="primary" id="apply">적용(리셋)</button>
            </div>
          </div>
        </div>
      </div>

      <div class="btns">
        <button id="toggleMode" class="primary">디지털로 보기</button>
        <button id="togglePause" class="primary">일시정지</button>
        <button id="reset" class="danger">리셋</button>
        <button id="fullscreen">전체화면</button>
      </div>
    </div>

    <div class="stage">
      <canvas id="analog" width="600" height="600" aria-label="시험용 아날로그 시계"></canvas>

      <div class="digital" id="digitalBox" aria-label="시험용 디지털 시계">
        <div class="time" id="digitalTime">13:00:00</div>
        <div class="sub" id="digitalSub">상태: 실행 중 · 시작 13:00 · 종료 16:00</div>
      </div>
    </div>

    <div class="hint">
      알림음: 종료 5분 전 1회, 종료 시 1회. 브라우저 정책상 버튼을 한 번이라도 누른 뒤부터 소리가 정상 재생됩니다.
    </div>
  </div>

  <!-- TLSM Footer (bottom-right) -->
  <div class="tlsm-footer" aria-label="TLSM">
    <div class="tlsm-tag">think less, sleep more</div>
    <div class="tlsm-copy">© 틀숨; tlsm</div>
  </div>

<script>
(() => {
  // ===== DOM =====
  const startTextEl = document.getElementById('startText');
  const endTextEl = document.getElementById('endText');
  const elapsedTextEl = document.getElementById('elapsedText');
  const remainTextEl = document.getElementById('remainText');

  const startHourEl = document.getElementById('startHour');
  const startMinEl  = document.getElementById('startMin');
  const durHourEl   = document.getElementById('durHour');
  const durMinEl    = document.getElementById('durMin');

  const applyBtn = document.getElementById('apply');
  const toggleModeBtn = document.getElementById('toggleMode');
  const togglePauseBtn = document.getElementById('togglePause');
  const resetBtn = document.getElementById('reset');
  const fsBtn = document.getElementById('fullscreen');

  const canvas = document.getElementById('analog');
  const ctx = canvas.getContext('2d');

  const digitalBox = document.getElementById('digitalBox');
  const digitalTimeEl = document.getElementById('digitalTime');
  const digitalSubEl = document.getElementById('digitalSub');

  // ===== 상태 =====
  let mode = "analog"; // "analog" | "digital"
  let paused = false;

  // 설정값(가변)
  let START_H = 13, START_M = 0, START_S = 0;
  let DURATION_MS = 3 * 60 * 60 * 1000;

  // 타이머 진행
  let elapsedMs = 0;
  let lastTick = performance.now();

  // 알림 트리거(1회)
  let warned5min = false;
  let warnedEnd = false;

  // 오디오(사용자 클릭 이후 활성)
  let audioCtx = null;
  let audioUnlocked = false;

  // ===== 유틸 =====
  function clampInt(v, min, max){
    const n = Number(v);
    if (!Number.isFinite(n)) return min;
    return Math.min(max, Math.max(min, Math.trunc(n)));
  }
  function pad2(n){ return String(n).padStart(2,'0'); }

  function formatHMS(ms){
    ms = Math.max(0, ms);
    const total = Math.floor(ms / 1000);
    const hh = Math.floor(total / 3600);
    const mm = Math.floor((total % 3600) / 60);
    const ss = total % 60;
    return `${pad2(hh)}:${pad2(mm)}:${pad2(ss)}`;
  }

  function toTimeText(h, m, s=0){
    return `${pad2(h)}:${pad2(m)}:${pad2(s)}`;
  }

  // 종료 시각 계산(시/분/초) - 24시간 넘어가면 다음날로 순환 표기
  function getEndTimeParts(){
    const durSec = Math.floor(DURATION_MS / 1000);
    const startSec = START_H*3600 + START_M*60 + START_S;
    const endSec = (startSec + durSec) % (24*3600);
    return {
      h: Math.floor(endSec/3600),
      m: Math.floor((endSec%3600)/60),
      s: endSec%60
    };
  }

  // "시험 시각" = 시작 + 경과
  function getExamTimeParts(){
    const totalSec = Math.floor(elapsedMs / 1000);
    const startSec = START_H*3600 + START_M*60 + START_S;
    const curSec = (startSec + totalSec) % (24*3600);
    return {
      h: Math.floor(curSec/3600),
      m: Math.floor((curSec%3600)/60),
      s: curSec%60
    };
  }

  // ===== 알림음 =====
  function ensureAudio(){
    try{
      if (!audioCtx){
        const Ctx = window.AudioContext || window.webkitAudioContext;
        audioCtx = new Ctx();
      }
      if (audioCtx.state === "suspended") audioCtx.resume();
      audioUnlocked = true;
    } catch(e){
      // 오디오 미지원 환경이면 무시
    }
  }

  function beep(pattern){
    // pattern: 배열 [{freq, ms}, ...]
    if (!audioUnlocked || !audioCtx) return;

    const now = audioCtx.currentTime;
    let t = now;

    for (const p of pattern){
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();

      osc.type = "sine";
      osc.frequency.value = p.freq;

      // 클릭/팝 방지 envelope
      gain.gain.setValueAtTime(0.0001, t);
      gain.gain.exponentialRampToValueAtTime(0.25, t + 0.01);
      gain.gain.exponentialRampToValueAtTime(0.0001, t + (p.ms/1000));

      osc.connect(gain);
      gain.connect(audioCtx.destination);

      osc.start(t);
      osc.stop(t + (p.ms/1000) + 0.02);

      t += (p.ms/1000) + 0.06;
    }
  }

  function beep5min(){
    // 짧게 2번
    beep([{freq: 880, ms: 180}, {freq: 880, ms: 180}]);
  }
  function beepEnd(){
    // 길게 3번
    beep([{freq: 660, ms: 350}, {freq: 660, ms: 350}, {freq: 660, ms: 350}]);
  }

  // ===== 모드 전환 =====
  function setMode(newMode){
    mode = newMode;
    if (mode === "analog"){
      canvas.style.display = "block";
      digitalBox.style.display = "none";
      toggleModeBtn.textContent = "디지털로 보기";
    } else {
      canvas.style.display = "none";
      digitalBox.style.display = "block";
      toggleModeBtn.textContent = "아날로그로 보기";
    }
    draw();
  }

  // ===== 렌더 =====
  function drawAnalog(){
    const now = getExamTimeParts();
    const h = now.h, m = now.m, s = now.s;

    const w = canvas.width, hp = canvas.height;
    const cx = w/2, cy = hp/2;
    const radius = Math.min(w, hp) * 0.42;

    ctx.clearRect(0,0,w,hp);

    // 배경/테두리
    ctx.beginPath();
    ctx.arc(cx, cy, radius*1.12, 0, Math.PI*2);
    ctx.fillStyle = "#0b0b0b";
    ctx.fill();
    ctx.strokeStyle = "#2a2a2a";
    ctx.lineWidth = 6;
    ctx.stroke();

    // 눈금
    for (let i=0; i<60; i++){
      const ang = (Math.PI*2) * (i/60) - Math.PI/2;
      const isHour = i % 5 === 0;
      const r1 = radius * (isHour ? 0.92 : 0.96);
      const r2 = radius * 1.02;

      ctx.beginPath();
      ctx.moveTo(cx + Math.cos(ang)*r1, cy + Math.sin(ang)*r1);
      ctx.lineTo(cx + Math.cos(ang)*r2, cy + Math.sin(ang)*r2);
      ctx.strokeStyle = isHour ? "#e0e0e0" : "#6a6a6a";
      ctx.lineWidth = isHour ? 4 : 2;
      ctx.stroke();
    }

    // 12/3/6/9
    const marks = [
      {t:"12", a:-Math.PI/2},
      {t:"3",  a:0},
      {t:"6",  a:Math.PI/2},
      {t:"9",  a:Math.PI},
    ];
    ctx.fillStyle="#d8d8d8";
    ctx.font="600 34px system-ui, -apple-system, Segoe UI, Roboto, Arial";
    ctx.textAlign="center";
    ctx.textBaseline="middle";
    for (const mk of marks){
      ctx.fillText(
        mk.t,
        cx + Math.cos(mk.a) * radius * 0.78,
        cy + Math.sin(mk.a) * radius * 0.78
      );
    }

    // 바늘 각도
    const hour12 = (h % 12) + (m/60) + (s/3600);
    const hourAng = (Math.PI*2) * (hour12/12) - Math.PI/2;
    const minAng  = (Math.PI*2) * ((m + s/60)/60) - Math.PI/2;
    const secAng  = (Math.PI*2) * (s/60) - Math.PI/2;

    // 시침
    ctx.beginPath();
    ctx.moveTo(cx, cy);
    ctx.lineTo(cx + Math.cos(hourAng)*radius*0.55, cy + Math.sin(hourAng)*radius*0.55);
    ctx.strokeStyle="#f0f0f0";
    ctx.lineWidth=10;
    ctx.lineCap="round";
    ctx.stroke();

    // 분침
    ctx.beginPath();
    ctx.moveTo(cx, cy);
    ctx.lineTo(cx + Math.cos(minAng)*radius*0.78, cy + Math.sin(minAng)*radius*0.78);
    ctx.strokeStyle="#cfcfcf";
    ctx.lineWidth=7;
    ctx.lineCap="round";
    ctx.stroke();

    // 초침
    ctx.beginPath();
    ctx.moveTo(cx, cy);
    ctx.lineTo(cx + Math.cos(secAng)*radius*0.9, cy + Math.sin(secAng)*radius*0.9);
    ctx.strokeStyle="#ff4d4d";
    ctx.lineWidth=3;
    ctx.lineCap="round";
    ctx.stroke();

    // 중심 캡
    ctx.beginPath();
    ctx.arc(cx, cy, 8, 0, Math.PI*2);
    ctx.fillStyle="#ff4d4d";
    ctx.fill();

    // 하단 디지털(작게)
    ctx.fillStyle="#bdbdbd";
    ctx.font="500 20px ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, 'Liberation Mono', 'Courier New', monospace";
    ctx.fillText(`${pad2(h)}:${pad2(m)}:${pad2(s)}`, cx, cy + radius*0.45);

    // 일시정지 표시
    if (paused){
      ctx.fillStyle="#ffcc66";
      ctx.font="700 18px system-ui, -apple-system, Segoe UI, Roboto, Arial";
      ctx.fillText("PAUSED", cx, cy - radius*0.55);
    }
  }

  function drawDigital(){
    const {h,m,s} = getExamTimeParts();
    digitalTimeEl.textContent = `${pad2(h)}:${pad2(m)}:${pad2(s)}`;

    const startStr = toTimeText(START_H, START_M, START_S);
    const endParts = getEndTimeParts();
    const endStr = toTimeText(endParts.h, endParts.m, endParts.s);

    const state = (elapsedMs >= DURATION_MS) ? "종료" : (paused ? "일시정지" : "실행 중");
    digitalSubEl.textContent = `상태: ${state} · 시작 ${startStr} · 종료 ${endStr}`;
  }

  function updateMeta(){
    const startStr = toTimeText(START_H, START_M, START_S);
    const endParts = getEndTimeParts();
    const endStr = toTimeText(endParts.h, endParts.m, endParts.s);

    startTextEl.textContent = startStr;
    endTextEl.textContent = endStr;

    elapsedTextEl.textContent = formatHMS(elapsedMs);
    remainTextEl.textContent = formatHMS(DURATION_MS - elapsedMs);

    if (elapsedMs >= DURATION_MS){
      togglePauseBtn.disabled = true;
      togglePauseBtn.textContent = "종료됨";
    } else {
      togglePauseBtn.disabled = false;
      togglePauseBtn.textContent = paused ? "재개" : "일시정지";
    }
  }

  // ===== 알림 체크 =====
  function checkAlerts(){
    if (paused) return;

    const remain = DURATION_MS - elapsedMs;

    // 5분 전(<= 5:00) 1회
    if (!warned5min && remain <= 5*60*1000 && remain > 0){
      warned5min = true;
      beep5min();
    }

    // 종료 시(<= 0) 1회
    if (!warnedEnd && remain <= 0){
      warnedEnd = true;
      beepEnd();
    }
  }

  function draw(){
    updateMeta();
    if (mode === "analog") drawAnalog();
    else drawDigital();
  }

  function tick(now){
    const dt = now - lastTick;
    lastTick = now;

    if (!paused && elapsedMs < DURATION_MS){
      elapsedMs += dt;
      if (elapsedMs > DURATION_MS) elapsedMs = DURATION_MS;
    }

    checkAlerts();
    draw();
    requestAnimationFrame(tick);
  }

  function hardReset(){
    paused = false;
    elapsedMs = 0;
    lastTick = performance.now();
    warned5min = false;
    warnedEnd = false;
    draw();
  }

  function onUserAction(){
    ensureAudio();
  }

  // ===== 이벤트 =====
  toggleModeBtn.addEventListener('click', () => {
    onUserAction();
    setMode(mode === "analog" ? "digital" : "analog");
  });

  togglePauseBtn.addEventListener('click', () => {
    onUserAction();
    if (elapsedMs >= DURATION_MS) return;
    paused = !paused;
    lastTick = performance.now();
    draw();
  });

  resetBtn.addEventListener('click', () => {
    onUserAction();
    hardReset();
  });

  applyBtn.addEventListener('click', () => {
    onUserAction();

    const sh = clampInt(startHourEl.value, 0, 23);
    const sm = clampInt(startMinEl.value, 0, 59);

    const dh = clampInt(durHourEl.value, 0, 24);
    const dm = clampInt(durMinEl.value, 0, 59);

    START_H = sh; START_M = sm; START_S = 0;
    DURATION_MS = (dh*60 + dm) * 60 * 1000;

    // 0이면 바로 종료되어버리므로 1분 보정
    if (DURATION_MS <= 0) DURATION_MS = 1 * 60 * 1000;

    hardReset();
  });

  fsBtn.addEventListener('click', async () => {
    onUserAction();
    try{
      if (!document.fullscreenElement) await document.documentElement.requestFullscreen();
      else await document.exitFullscreen();
    } catch(e) {}
  });

  // ===== 시작 =====
  setMode("analog");
  draw();
  requestAnimationFrame(tick);
})();
</script>
</body>
</html>
