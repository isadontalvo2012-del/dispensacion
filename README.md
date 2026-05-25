<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Dispensación de Medicamentos</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600&display=swap" rel="stylesheet"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{font-family:'DM Sans',sans-serif;background:#f0f4f1;color:#1a1a1a;min-height:100vh}
header{background:#1D9E75;padding:1rem 1.5rem}
header h1{font-size:17px;font-weight:600;color:white}
header p{font-size:11px;color:rgba(255,255,255,0.8);margin-top:2px}
.container{max-width:660px;margin:0 auto;padding:1.25rem 1rem 3rem}
.card{background:white;border-radius:14px;padding:1.25rem;margin-bottom:1rem;box-shadow:0 1px 4px rgba(0,0,0,0.07)}
.card-title{font-size:11px;font-weight:600;color:#888;text-transform:uppercase;letter-spacing:0.07em;margin-bottom:1rem;display:flex;align-items:center;gap:8px}
.snum{width:22px;height:22px;border-radius:50%;background:#eee;font-size:11px;font-weight:600;display:flex;align-items:center;justify-content:center;color:#999;flex-shrink:0;transition:all .2s}
.snum.active{background:#1D9E75;color:white}
.snum.done{background:#639922;color:white}

.tipos-grid{display:grid;grid-template-columns:repeat(4,1fr);gap:8px}
.tipo-btn{display:flex;flex-direction:column;align-items:center;justify-content:center;padding:12px 6px;border-radius:12px;border:2px solid #e0e0e0;background:#fafafa;cursor:pointer;transition:all .15s;gap:4px}
.tipo-btn:hover{border-color:#1D9E75;background:#f0faf6}
.tipo-btn.selected{border-color:#1D9E75;background:#e8f8f2}
.tipo-btn .tag{font-size:15px;font-weight:700;color:#555}
.tipo-btn .desc{font-size:10px;color:#aaa;text-align:center;line-height:1.3}
.tipo-btn.selected .tag{color:#0F6E56}

.fesy-row{display:flex;align-items:center;gap:6px;flex-wrap:wrap}
.fesy-fixed{font-size:15px;font-weight:700;color:#0F6E56;background:#e8f8f2;padding:8px 12px;border-radius:8px;white-space:nowrap}
.fesy-sep{font-size:18px;color:#bbb;font-weight:300}
.fesy-input{font-size:18px;font-weight:600;padding:8px 12px;border-radius:8px;border:1.5px solid #b2e8d2;background:white;color:#0F6E56;width:130px;outline:none}
.fesy-input:focus{border-color:#1D9E75}
.fesy-preview{font-size:11px;color:#888;margin-top:8px}
.nombre-preview{margin-top:10px;background:#f5fdf8;border-radius:9px;padding:8px 12px;font-size:12px;color:#555;display:none}
.nombre-preview strong{color:#0F6E56;font-size:13px}

/* Camera */
.video-wrap{position:relative;width:100%;background:#111;border-radius:10px;overflow:hidden;aspect-ratio:4/3;display:flex;align-items:center;justify-content:center}
#videoEl{width:100%;height:100%;object-fit:cover;display:none}
#canvasEl{display:none}
#detectCanvas{display:none}
.placeholder{text-align:center;color:#666;padding:2rem}
.placeholder .icon{font-size:44px;margin-bottom:8px}
.placeholder p{font-size:13px}
.placeholder small{font-size:11px;color:#555;margin-top:4px;display:block}

/* Sensor overlay */
.sensor-overlay{position:absolute;top:0;left:0;width:100%;height:100%;pointer-events:none;display:none;flex-direction:column;align-items:center;justify-content:flex-end;padding-bottom:16px;gap:8px}
.sensor-bar-wrap{width:80%;background:rgba(0,0,0,0.4);border-radius:20px;height:10px;overflow:hidden}
.sensor-bar{height:100%;width:0%;border-radius:20px;background:#1D9E75;transition:width 0.1s}
.sensor-label{font-size:13px;font-weight:600;color:white;text-shadow:0 1px 3px rgba(0,0,0,0.5)}
.sensor-countdown{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);font-size:80px;font-weight:700;color:white;text-shadow:0 2px 10px rgba(0,0,0,0.6);display:none}
.border-stable{position:absolute;inset:0;border:4px solid #1D9E75;border-radius:10px;display:none}
.border-moving{position:absolute;inset:0;border:4px solid #f39c12;border-radius:10px;display:none}

.btn-row{display:flex;gap:8px;margin-top:.75rem;flex-wrap:wrap}
button{font-family:'DM Sans',sans-serif;font-size:13px;font-weight:500;padding:9px 15px;border-radius:9px;border:1.5px solid #ddd;background:white;color:#333;cursor:pointer;display:flex;align-items:center;gap:6px;transition:all .15s}
button:hover:not(:disabled){background:#f5f5f5}
button:active:not(:disabled){transform:scale(.97)}
button:disabled{opacity:.38;cursor:not-allowed}
.btn-green{background:#1D9E75!important;border-color:#1D9E75!important;color:white!important}
.btn-green:hover:not(:disabled){background:#0F6E56!important}
.btn-red{background:#fff0f0!important;border-color:#f5b8b8!important;color:#c0392b!important}
.btn-orange{background:#fff8f0!important;border-color:#f5d0a8!important;color:#e67e22!important}
.status{font-size:12px;color:#888;margin-top:8px;min-height:18px}
.status.ok{color:#1D9E75}
.status.err{color:#e74c3c}
.status.warn{color:#e67e22}

.photos-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(100px,1fr));gap:8px;margin-top:.5rem}
.photo-thumb{position:relative;border-radius:8px;overflow:hidden;border:1px solid #eee;aspect-ratio:4/3;background:#f5f5f5}
.photo-thumb img{width:100%;height:100%;object-fit:cover}
.del-btn{position:absolute;top:4px;right:4px;width:22px;height:22px;border-radius:50%;background:rgba(0,0,0,.55);border:none;color:white;font-size:16px;line-height:1;display:flex;align-items:center;justify-content:center;padding:0;cursor:pointer}
.del-btn:hover{background:#e74c3c}
.empty-box{text-align:center;padding:1.25rem;color:#ccc;font-size:13px;border:1.5px dashed #e5e5e5;border-radius:10px}

.docs-list{display:flex;flex-direction:column;gap:8px;margin-top:.5rem}
.doc-item{display:flex;align-items:center;justify-content:space-between;padding:10px 14px;border-radius:10px;border:1px solid #e8f8f2;background:#f5fdf8}
.doc-info{display:flex;align-items:center;gap:10px}
.doc-badge{font-size:12px;font-weight:700;color:white;background:#1D9E75;padding:3px 9px;border-radius:6px;flex-shrink:0}
.doc-name{font-size:13px;color:#333;font-weight:500}
.doc-count{font-size:11px;color:#888;margin-top:1px}
.doc-del{border:none;background:transparent;color:#ccc;cursor:pointer;font-size:20px;padding:0;line-height:1}
.doc-del:hover{color:#e74c3c}
.empty-docs{text-align:center;padding:1.25rem;color:#ccc;font-size:13px;border:1.5px dashed #e5e5e5;border-radius:10px}

.mode-toggle{display:flex;gap:8px;margin-bottom:12px}
.mode-btn{flex:1;padding:8px;border-radius:9px;border:1.5px solid #ddd;background:#fafafa;font-size:13px;font-weight:500;cursor:pointer;text-align:center;transition:all .15s}
.mode-btn.active{background:#1D9E75;border-color:#1D9E75;color:white}
</style>
</head>
<body>
<header>
  <h1>💊 Dispensación de Medicamentos</h1>
  <p>Sub Document Camera FuVision · Detección automática + PDF + ZIP</p>
</header>
<div class="container">

  <!-- Paso 1 -->
  <div class="card">
    <div class="card-title"><span class="snum active" id="n1">1</span> Número de factura y tipo</div>
    <div class="fesy-row">
      <span class="fesy-fixed">FESY</span>
      <span class="fesy-sep">—</span>
      <input class="fesy-input" type="text" id="numFesy" placeholder="0000" inputmode="numeric" oninput="actualizarPreview()" value="0000"/>
    </div>
    <div class="fesy-preview" id="fesyPreview">📄 Número: <strong>FESY0000</strong></div>
    <div class="tipos-grid" style="margin-top:14px">
      <div class="tipo-btn" onclick="selTipo('OPF')" id="t_OPF"><span class="tag">OPF</span><span class="desc">Fórmula</span></div>
      <div class="tipo-btn" onclick="selTipo('CRC')" id="t_CRC"><span class="tag">CRC</span><span class="desc">Dispensación</span></div>
      <div class="tipo-btn" onclick="selTipo('PDE')" id="t_PDE"><span class="tag">PDE</span><span class="desc">Activación</span></div>
      <div class="tipo-btn" onclick="selTipo('FESY')" id="t_FESY"><span class="tag">FESY</span><span class="desc">Factura Electrónica</span></div>
    </div>
    <div class="nombre-preview" id="nombrePreview">PDF: <strong id="nombreFinal">—</strong></div>
    <div class="status" id="tipoStatus">Escribe el número y selecciona el tipo</div>
  </div>

  <!-- Paso 2 -->
  <div class="card">
    <div class="card-title"><span class="snum" id="n2">2</span> Cámara</div>

    <div class="mode-toggle">
      <div class="mode-btn active" id="modeManual" onclick="setMode('manual')">📷 Manual</div>
      <div class="mode-btn" id="modeAuto" onclick="setMode('auto')">🤖 Automático</div>
    </div>

    <div class="video-wrap" id="videoWrap">
      <video id="videoEl" autoplay playsinline></video>
      <canvas id="canvasEl"></canvas>
      <canvas id="detectCanvas"></canvas>
      <div class="sensor-overlay" id="sensorOverlay">
        <span class="sensor-label" id="sensorLabel">Esperando documento...</span>
        <div class="sensor-bar-wrap"><div class="sensor-bar" id="sensorBar"></div></div>
      </div>
      <div class="sensor-countdown" id="countdown"></div>
      <div class="border-stable" id="borderStable"></div>
      <div class="border-moving" id="borderMoving"></div>
      <div class="placeholder" id="placeholder">
        <div class="icon">📷</div>
        <p>Cámara inactiva</p>
        <small>Completa el paso 1 y presiona "Abrir cámara"</small>
      </div>
    </div>

    <div class="btn-row">
      <button id="btnOpen" onclick="openCamera()">📷 Abrir cámara</button>
      <button id="btnSnap" class="btn-green" onclick="takePhotoManual()" disabled>⊙ Tomar foto</button>
      <button id="btnClose" onclick="closeCamera()" disabled>✕ Cerrar</button>
    </div>
    <div class="status" id="camStatus">Lista para iniciar</div>
  </div>

  <!-- Paso 3 -->
  <div class="card">
    <div class="card-title"><span class="snum" id="n3">3</span> Fotos capturadas</div>
    <div id="photosGrid" class="photos-grid"></div>
    <div class="empty-box" id="emptyPhotos">Sin fotos — abre la cámara y captura el documento</div>
    <p class="status" id="photoCount"></p>
    <div class="btn-row" style="margin-top:.75rem">
      <button class="btn-green" id="btnGuardar" onclick="guardarDocumento()" disabled>✔ Guardar PDF de este documento</button>
      <button class="btn-red" id="btnLimpiarFotos" onclick="limpiarFotos()" disabled>🗑 Limpiar</button>
    </div>
    <div class="status" id="guardarStatus"></div>
  </div>

  <!-- Paso 4 -->
  <div class="card">
    <div class="card-title"><span class="snum" id="n4">4</span> Documentos listos</div>
    <div id="docsList" class="docs-list"></div>
    <div class="empty-docs" id="emptyDocs">Aún no hay documentos guardados</div>
    <div class="btn-row" style="margin-top:1rem">
      <button class="btn-green" id="btnZip" onclick="descargarZip()" disabled style="font-size:15px;padding:12px 22px">⬇ Descargar todo en ZIP</button>
    </div>
    <div class="status" id="zipStatus"></div>
  </div>

</div>
<script>
const NIT = '900275012';
let stream = null;
let currentTipo = null;
let currentPhotos = [];
let documentos = [];
let autoMode = false;
let detectInterval = null;
let prevFrame = null;
let stableCount = 0;
let movingCount = 0;
let countdownTimer = null;
let countdownVal = 0;
let waitingAfterPhoto = false;

const video = document.getElementById('videoEl');
const canvas = document.getElementById('canvasEl');
const detectCanvas = document.getElementById('detectCanvas');
const placeholder = document.getElementById('placeholder');
const btnOpen = document.getElementById('btnOpen');
const btnSnap = document.getElementById('btnSnap');
const btnClose = document.getElementById('btnClose');
const camStatus = document.getElementById('camStatus');
const photosGrid = document.getElementById('photosGrid');
const emptyPhotos = document.getElementById('emptyPhotos');
const photoCount = document.getElementById('photoCount');
const btnGuardar = document.getElementById('btnGuardar');
const btnLimpiarFotos = document.getElementById('btnLimpiarFotos');
const guardarStatus = document.getElementById('guardarStatus');
const docsList = document.getElementById('docsList');
const emptyDocs = document.getElementById('emptyDocs');
const btnZip = document.getElementById('btnZip');
const zipStatus = document.getElementById('zipStatus');
const sensorOverlay = document.getElementById('sensorOverlay');
const sensorBar = document.getElementById('sensorBar');
const sensorLabel = document.getElementById('sensorLabel');
const countdownEl = document.getElementById('countdown');
const borderStable = document.getElementById('borderStable');
const borderMoving = document.getElementById('borderMoving');

function setStatus(el, msg, type) { el.textContent = msg; el.className = 'status'+(type?' '+type:''); }

function getNum() { return document.getElementById('numFesy').value.trim() || '0000'; }
function getNombre() { return (currentTipo||'TIPO')+'_'+NIT+'_FESY'+(getNum()||'___')+'.pdf'; }

function actualizarPreview() {
  const num = getNum();
  document.getElementById('fesyPreview').innerHTML = num ? '📄 Número: <strong>FESY'+num+'</strong>' : 'Escribe el número de la factura';
  if (currentTipo) { document.getElementById('nombrePreview').style.display='block'; document.getElementById('nombreFinal').textContent=getNombre(); }
}

function selTipo(tipo) {
  currentTipo = tipo;
  document.querySelectorAll('.tipo-btn').forEach(b=>b.classList.remove('selected'));
  document.getElementById('t_'+tipo).classList.add('selected');
  document.getElementById('nombrePreview').style.display='block';
  document.getElementById('nombreFinal').textContent=getNombre();
  setStatus(document.getElementById('tipoStatus'),'✓ '+getNombre(),'ok');
  document.getElementById('n1').className='snum done';
  document.getElementById('n2').className='snum active';
}

function setMode(mode) {
  autoMode = mode === 'auto';
  document.getElementById('modeManual').className = 'mode-btn'+(autoMode?'':' active');
  document.getElementById('modeAuto').className = 'mode-btn'+(autoMode?' active':'');
  btnSnap.style.display = autoMode ? 'none' : 'flex';
  if (stream) {
    if (autoMode) { startDetection(); sensorOverlay.style.display='flex'; }
    else { stopDetection(); sensorOverlay.style.display='none'; borderStable.style.display='none'; borderMoving.style.display='none'; }
  }
}

// Beep sound
function beep(freq, dur, vol) {
  try {
    const ctx = new (window.AudioContext||window.webkitAudioContext)();
    const o = ctx.createOscillator();
    const g = ctx.createGain();
    o.connect(g); g.connect(ctx.destination);
    o.frequency.value = freq||880;
    g.gain.value = vol||0.3;
    o.start(); o.stop(ctx.currentTime+(dur||0.15));
  } catch(e) {}
}

function beepReady() { beep(880,0.1,0.3); setTimeout(()=>beep(1100,0.15,0.3),120); }
function beepPhoto() { beep(1200,0.08,0.4); setTimeout(()=>beep(1400,0.12,0.4),90); }

async function openCamera() {
  if (!currentTipo) { setStatus(camStatus,'✗ Selecciona el tipo de documento','err'); return; }
  if (!getNum()) { setStatus(camStatus,'✗ Escribe el número de factura','err'); return; }
  try {
    canvas.style.display='none'; video.style.display='block'; placeholder.style.display='none';
    stream = await navigator.mediaDevices.getUserMedia({video:{facingMode:'environment',width:{ideal:1920},height:{ideal:1440}}});
    video.srcObject = stream; await video.play();
    btnOpen.disabled=true; btnSnap.disabled=false; btnClose.disabled=false;
    setStatus(camStatus,'✓ Cámara activa — '+getNombre(),'ok');
    if (autoMode) { startDetection(); sensorOverlay.style.display='flex'; }
  } catch(e) {
    setStatus(camStatus,'✗ No se pudo acceder a la cámara: '+(e.message||''),'err');
    placeholder.style.display='flex'; video.style.display='none';
  }
}

function closeCamera() {
  stopDetection();
  if (stream) { stream.getTracks().forEach(t=>t.stop()); stream=null; }
  video.srcObject=null; video.style.display='none'; placeholder.style.display='flex';
  placeholder.innerHTML='<div class="icon">✅</div><p style="color:#1D9E75;font-weight:500;">Cámara cerrada</p><small>Guarda el documento o abre de nuevo</small>';
  btnOpen.disabled=false; btnSnap.disabled=true; btnClose.disabled=true;
  sensorOverlay.style.display='none'; borderStable.style.display='none'; borderMoving.style.display='none';
  countdownEl.style.display='none';
  setStatus(camStatus,'✓ Cámara cerrada — '+currentPhotos.length+' foto(s)','ok');
}

// Detection engine
function startDetection() {
  prevFrame = null; stableCount = 0; movingCount = 0; waitingAfterPhoto = false;
  clearCountdown();
  detectInterval = setInterval(detectFrame, 150);
}

function stopDetection() {
  if (detectInterval) { clearInterval(detectInterval); detectInterval = null; }
  clearCountdown();
}

function clearCountdown() {
  if (countdownTimer) { clearTimeout(countdownTimer); clearInterval(countdownTimer); countdownTimer = null; }
  countdownEl.style.display = 'none'; countdownVal = 0;
}

function detectFrame() {
  if (!stream || !video.videoWidth || waitingAfterPhoto) return;
  const w = 80, h = 60;
  detectCanvas.width = w; detectCanvas.height = h;
  const ctx = detectCanvas.getContext('2d');
  ctx.drawImage(video, 0, 0, w, h);
  const frame = ctx.getImageData(0, 0, w, h).data;

  if (!prevFrame) { prevFrame = frame; return; }

  let diff = 0;
  for (let i = 0; i < frame.length; i += 4) {
    diff += Math.abs(frame[i] - prevFrame[i]);
    diff += Math.abs(frame[i+1] - prevFrame[i+1]);
    diff += Math.abs(frame[i+2] - prevFrame[i+2]);
  }
  const avgDiff = diff / (w * h * 3);
  prevFrame = frame;

  const pct = Math.min(100, avgDiff * 4);
  sensorBar.style.width = pct + '%';
  sensorBar.style.background = avgDiff > 8 ? '#e67e22' : avgDiff > 3 ? '#f1c40f' : '#1D9E75';

  if (avgDiff > 8) {
    // Moving
    movingCount++; stableCount = 0;
    clearCountdown();
    borderMoving.style.display = 'block'; borderStable.style.display = 'none';
    sensorLabel.textContent = '📄 Documento detectado — quieto...';
  } else if (avgDiff > 1.5) {
    // Slight movement
    stableCount = 0;
    borderMoving.style.display = 'none'; borderStable.style.display = 'none';
    sensorLabel.textContent = 'Estabilizando...';
    clearCountdown();
  } else {
    // Stable
    stableCount++;
    borderMoving.style.display = 'none';
    if (stableCount > 8 && movingCount > 3) {
      borderStable.style.display = 'block';
      sensorLabel.textContent = '✅ Documento estable — fotografiando...';
      if (!countdownTimer) {
        beepReady();
        countdownEl.style.display = 'block';
        countdownEl.textContent = '📸';
        countdownTimer = setTimeout(() => {
          clearCountdown();
          takePhotoAuto();
        }, 1500);
      }
    } else if (stableCount <= 8) {
      sensorLabel.textContent = 'Esperando documento...';
    }
  }
}

function takePhotoAuto() {
  waitingAfterPhoto = true;
  beepPhoto();
  captureFrame();
  borderStable.style.display = 'none';
  sensorLabel.textContent = '📸 Foto tomada — pon el siguiente o guarda';
  stableCount = 0; movingCount = 0; prevFrame = null;
  setTimeout(() => { waitingAfterPhoto = false; }, 2500);
}

function takePhotoManual() { captureFrame(); }

function captureFrame() {
  const w = video.videoWidth||1280, h = video.videoHeight||960;
  canvas.width=w; canvas.height=h;
  canvas.getContext('2d').drawImage(video,0,0,w,h);
  currentPhotos.push(canvas.toDataURL('image/jpeg',0.93));
  updatePhotosGrid();
  setStatus(camStatus,'✓ Foto '+currentPhotos.length+' capturada','ok');
  document.getElementById('n3').className='snum active';
  btnGuardar.disabled=false; btnLimpiarFotos.disabled=false;
}

function updatePhotosGrid() {
  photosGrid.innerHTML='';
  if(currentPhotos.length===0){emptyPhotos.style.display='block';photoCount.textContent='';return;}
  emptyPhotos.style.display='none';
  currentPhotos.forEach((src,i)=>{
    const div=document.createElement('div'); div.className='photo-thumb';
    const img=document.createElement('img'); img.src=src;
    const del=document.createElement('button'); del.className='del-btn'; del.innerHTML='×';
    del.onclick=()=>{currentPhotos.splice(i,1);updatePhotosGrid();if(currentPhotos.length===0){btnGuardar.disabled=true;btnLimpiarFotos.disabled=true;}};
    div.appendChild(img); div.appendChild(del); photosGrid.appendChild(div);
  });
  photoCount.textContent=currentPhotos.length+' foto(s) → '+getNombre();
}

function limpiarFotos(){currentPhotos=[];updatePhotosGrid();btnGuardar.disabled=true;btnLimpiarFotos.disabled=true;}

function generarPDFBlob(tipo,num,fotos){
  const {jsPDF}=window.jspdf;
  const doc=new jsPDF({orientation:'portrait',unit:'mm',format:'a4'});
  const W=210,H=297,M=15;
  const fecha=new Date().toLocaleString('es-CO');
  const nombre=tipo+'_'+NIT+'_FESY'+num;
  const colores={OPF:[29,158,117],CRC:[52,120,200],PDE:[180,100,20],FESY:[120,40,160]};
  const [r,g,b]=colores[tipo]||[29,158,117];
  doc.setFillColor(r,g,b); doc.rect(0,0,W,30,'F');
  doc.setTextColor(255,255,255); doc.setFontSize(17); doc.setFont('helvetica','bold');
  doc.text(nombre,M,14);
  doc.setFontSize(10); doc.setFont('helvetica','normal');
  doc.text('Dispensación de Medicamentos · Sub Document Camera FuVision',M,22);
  doc.text(fecha,W-M,22,{align:'right'});
  doc.setTextColor(30,30,30); doc.setFontSize(12); doc.setFont('helvetica','bold');
  doc.text('Documento: '+nombre,M,40);
  doc.setDrawColor(r,g,b); doc.setLineWidth(0.6); doc.line(M,44,W-M,44);
  doc.setFontSize(11); doc.setFont('helvetica','bold'); doc.setTextColor(60,60,60);
  doc.text('Fotografías ('+fotos.length+')',M,52);
  let y=57; const imgW=W-2*M;
  fotos.forEach((src,i)=>{
    const imgH=Math.round(imgW*0.75);
    if(y+imgH+10>H-14){doc.addPage();y=M;}
    doc.addImage(src,'JPEG',M,y,imgW,imgH);
    doc.setFontSize(9); doc.setFont('helvetica','normal'); doc.setTextColor(160,160,160);
    doc.text('Foto '+(i+1)+' de '+fotos.length+' · '+nombre,M,y+imgH+5);
    y+=imgH+11;
  });
  doc.setDrawColor(220,220,220); doc.line(M,H-13,W-M,H-13);
  doc.setFontSize(8); doc.setTextColor(170,170,170);
  doc.text('Generado: '+fecha+' · '+nombre,M,H-7);
  return doc.output('blob');
}

function guardarDocumento(){
  if(!currentTipo){setStatus(guardarStatus,'✗ Selecciona el tipo','err');return;}
  if(!getNum()){setStatus(guardarStatus,'✗ Escribe el número de factura','err');return;}
  if(currentPhotos.length===0){setStatus(guardarStatus,'✗ No hay fotos','err');return;}
  const num=getNum(); const nombre=getNombre();
  setStatus(guardarStatus,'Generando PDF...','');
  setTimeout(()=>{
    try{
      const blob=generarPDFBlob(currentTipo,num,[...currentPhotos]);
      documentos.push({nombre,blob,tipo:currentTipo,fotos:currentPhotos.length});
      currentPhotos=[]; updatePhotosGrid();
      btnGuardar.disabled=true; btnLimpiarFotos.disabled=true;
      setStatus(guardarStatus,'✓ Guardado: '+nombre,'ok');
      updateDocsList();
      currentTipo=null;
      document.querySelectorAll('.tipo-btn').forEach(b=>b.classList.remove('selected'));
      document.getElementById('nombrePreview').style.display='none';
      document.getElementById('n1').className='snum active';
      document.getElementById('n2').className='snum';
      document.getElementById('n3').className='snum';
      setStatus(document.getElementById('tipoStatus'),'Selecciona el siguiente tipo','');
      if(autoMode){stableCount=0;movingCount=0;prevFrame=null;sensorLabel.textContent='Esperando documento...';}
    }catch(e){setStatus(guardarStatus,'✗ Error: '+e.message,'err');}
  },100);
}

function updateDocsList(){
  docsList.innerHTML='';
  if(documentos.length===0){emptyDocs.style.display='block';btnZip.disabled=true;return;}
  emptyDocs.style.display='none'; btnZip.disabled=false;
  documentos.forEach((d,i)=>{
    const div=document.createElement('div'); div.className='doc-item';
    div.innerHTML=`<div class="doc-info"><span class="doc-badge">${d.tipo}</span><div><div class="doc-name">${d.nombre}</div><div class="doc-count">${d.fotos} foto(s)</div></div></div><button class="doc-del" onclick="eliminarDoc(${i})">×</button>`;
    docsList.appendChild(div);
  });
  document.getElementById('n4').className='snum active';
}

function eliminarDoc(i){documentos.splice(i,1);updateDocsList();}

async function descargarZip(){
  if(documentos.length===0){setStatus(zipStatus,'✗ No hay documentos','err');return;}
  setStatus(zipStatus,'Creando ZIP...','');
  try{
    const zip=new JSZip();
    documentos.forEach(d=>zip.file(d.nombre,d.blob));
    const content=await zip.generateAsync({type:'blob'});
    const url=URL.createObjectURL(content);
    const a=document.createElement('a');
    a.href=url; a.download='soporte_factura_EPS_familiar.zip';
    document.body.appendChild(a); a.click();
    document.body.removeChild(a); URL.revokeObjectURL(url);
    setStatus(zipStatus,'✓ ZIP descargado','ok');
    document.getElementById('n4').className='snum done';
  }catch(e){setStatus(zipStatus,'✗ Error: '+e.message,'err');}
}
</script>
</body>
</html>
