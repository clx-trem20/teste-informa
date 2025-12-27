<html lang="pt-BR">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Ponto Eletrônico - Admin & Configurações</title>
<script src="https://cdn.sheetjs.com/xlsx-latest/package/dist/xlsx.full.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
<style>
  :root{
    --blue:#0b4f78; --green:#2e9b4f; --yellow:#ffb739; --red:#ef5350;
    --muted:#6b7280; --card:#ffffff; --bg:#f4f7fb; --indigo: #6366f1;
  }
  body{font-family:Inter, system-ui, -apple-system, Arial, sans-serif;background:var(--bg);margin:0;color:#111; display: flex; flex-direction: column; min-height: 100vh;}
  
  #app-container { flex: 1; display: flex; flex-direction: column; width: 100%; position: relative; }

  header{background:linear-gradient(90deg,var(--blue),#0f6b96);color:#fff;padding:12px 18px;display:flex;align-items:center;justify-content:space-between;gap:12px;flex-wrap:wrap}
  .logo{font-weight:700;font-size:18px}
  #clock{font-weight:700}
  .controls{display:flex;gap:8px;align-items:center}
  
  button{padding:8px 12px;border:none;border-radius:8px;cursor:pointer;font-weight:600; transition: all 0.2s; display: flex; align-items: center; gap: 6px;}
  button:hover{opacity: 0.9; transform: translateY(-1px);}
  .add{background:var(--green);color:#fff}
  .secondary{background:#e5e7eb;color:#111}
  .download{background:var(--yellow);color:#111}
  .danger{background:var(--red);color:#fff}
  .qr-btn{background:var(--indigo);color:#fff}
  .settings-btn{background: transparent; color: #fff; padding: 5px; border-radius: 50%; border: 1px solid rgba(255,255,255,0.2);}
  .settings-btn:hover { background: rgba(255,255,255,0.1); }

  main{padding:20px;max-width:1100px;margin:0 auto; flex: 1; width: 100%; box-sizing: border-box;}
  
  footer { text-align: center; padding: 20px; color: var(--muted); font-size: 14px; border-top: 1px solid #e2e8f0; background: #fff; width: 100%; box-sizing: border-box; }

  #loginScreen {
    position: fixed;
    inset: 0;
    background: var(--blue);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 11000;
    padding: 20px;
  }

  .login-card {
    background: #fff;
    padding: 40px 30px;
    border-radius: 16px;
    width: 100%;
    max-width: 400px;
    text-align: center;
    box-shadow: 0 20px 50px rgba(0,0,0,0.3);
  }

  .admin-panel { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 25px; margin-top: 10px; }
  .stat-card { background: var(--card); padding: 20px; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); border-left: 5px solid var(--blue); }
  .stat-card h4 { margin: 0; color: var(--muted); font-size: 13px; text-transform: uppercase; letter-spacing: 0.5px; }
  .stat-card .value { font-size: 28px; font-weight: 800; margin-top: 5px; color: var(--blue); transition: color 0.3s; }
  .stat-card.green { border-left-color: var(--green); }
  .stat-card.yellow { border-left-color: var(--yellow); }
  .stat-card.red { border-left-color: var(--red); }

  .search{width:100%;padding:12px;border-radius:8px;border:1px solid #d1d5db;margin-bottom:20px; font-size: 16px; box-sizing: border-box;}
  
  table{width:100%;border-collapse:collapse;background:var(--card);border-radius:10px;overflow:hidden;box-shadow:0 6px 24px rgba(15,23,42,0.06);margin-bottom:30px}
  th,td{padding:12px;border-bottom:1px solid #eef2f6;text-align:left;font-size:14px}
  th{background:#fbfdfe;font-weight:700; color: var(--blue)}
  
  .modal{position:fixed;inset:0;background:rgba(0,0,0,.45);display:flex;align-items:center;justify-content:center;z-index:9999}
  .modal-content{background:#fff;padding:22px;border-radius:12px;width:95%;max-width:720px;max-height: 90vh;overflow-y: auto;}
  .hidden{display:none !important}
  
  #video-container { position: relative; width: 100%; max-width: 350px; aspect-ratio: 1; margin: 0 auto; background: #000; border-radius: 12px; overflow: hidden; border: 4px solid var(--indigo); }
  #video-preview { width: 100%; height: 100%; object-fit: cover; transform: scaleX(-1); }
  .scanner-line { position: absolute; top: 0; left: 0; width: 100%; height: 3px; background: var(--indigo); animation: scan 2s infinite linear; }
  @keyframes scan { 0% { top: 0%; } 100% { top: 100%; } }
  
  .qr-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 15px; }
  .qr-card { border: 2px solid #eef2f6; padding: 15px; border-radius: 12px; text-align: center; background: #fff; page-break-inside: avoid; break-inside: avoid; }
  .qr-img { display: flex; justify-content: center; margin: 12px 0; }

  @media print {
    body * { visibility: hidden !important; height: 0; margin: 0; padding: 0; overflow: hidden; }
    #qrGalleryModal, #qrGalleryModal * { visibility: visible !important; height: auto !important; overflow: visible !important; }
    #qrGalleryModal { position: absolute !important; left: 0 !important; top: 0 !important; width: 100% !important; background: white !important; display: block !important; padding: 0 !important; margin: 0 !important; }
    .modal-content { box-shadow: none !important; max-width: 100% !important; width: 100% !important; padding: 0 !important; margin: 0 !important; }
    .no-print { display: none !important; }
    .qr-grid { display: grid !important; grid-template-columns: repeat(3, 1fr) !important; gap: 30px !important; padding: 20px !important; }
    .qr-card { border: 1px solid #eee !important; padding: 15px !important; margin: 0 !important; break-inside: avoid !important; page-break-inside: avoid !important; background-color: #fff !important; -webkit-print-color-adjust: exact; }
  }
</style>
</head>
<body>

<div id="app-container">
  <!-- Ecrã de Login -->
  <div id="loginScreen">
    <div class="login-card">
      <div style="margin-bottom: 25px;">
        <div style="background: var(--blue); color: white; width: 60px; height: 60px; border-radius: 12px; display: flex; align-items: center; justify-content: center; margin: 0 auto 15px; font-size: 24px; font-weight: bold;">CLX</div>
        <h2 style="margin:0;color:var(--blue); font-size: 22px;">Acesso ao Sistema</h2>
        <p style="color: var(--muted); font-size: 14px; margin-top: 5px;">Gerenciamento de Ponto Eletrônico</p>
      </div>
      
      <input id="user" placeholder="Usuário" autocomplete="off" style="width:100%;padding:14px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box; font-size: 16px;">
      <input id="pass" type="password" placeholder="Senha" autocomplete="new-password" style="width:100%;padding:14px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box; font-size: 16px;">
      
      <div style="display: flex; align-items: center; gap: 8px; margin: 15px 0; font-size: 15px; color: var(--muted); justify-content: start;">
        <input type="checkbox" id="rememberMe" style="width: 18px; height: 18px; cursor: pointer;">
        <label for="rememberMe" style="cursor: pointer;">Lembrar login</label>
      </div>

      <button id="loginBtn" class="add" style="width:100%;margin-top:10px; padding: 14px; justify-content: center; font-size: 16px;">Entrar no Sistema</button>
      <p id="loginMsg" style="color:var(--red);margin-top:15px;height:20px; font-size: 14px; font-weight: 600;"></p>
    </div>
  </div>

  <!-- Cabeçalho -->
  <header id="mainHeader" class="hidden">
    <div style="display:flex;gap:12px;align-items:center">
      <div class="logo">Sistema de Ponto</div>
      <button id="abrirConfigBtn" class="settings-btn hidden" title="Gerenciar Usuários">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="3"></circle><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 2 2 2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 2 2 2 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 2 2 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path></svg>
      </button>
    </div>
    <div style="display:flex;gap:12px;align-items:center">
      <div id="clock">00:00:00</div>
      <div class="controls">
        <button class="danger" id="limparPontosBtn">🗑️ Limpar Pontos</button>
        <button class="qr-btn" id="abrirScannerBtn">📸 Scanner</button>
        <button class="secondary" id="abrirGalleryBtn">🖼️ Crachás</button>
        <button class="download" id="baixarBtn">📥 Excel</button>
        <button class="secondary" id="logoutBtn">Sair</button>
      </div>
    </div>
  </header>

  <!-- Aplicação Principal -->
  <main id="mainApp" class="hidden">
    <!-- Dashboard de Estatísticas -->
    <div class="admin-panel">
      <div class="stat-card"><h4>Total Equipe</h4><div class="value" id="stat-total">0</div></div>
      <div class="stat-card green"><h4>Entradas Hoje</h4><div class="value" id="stat-entradas">0</div></div>
      <div class="stat-card yellow"><h4>Saídas Hoje</h4><div class="value" id="stat-saidas">0</div></div>
      <div class="stat-card red" style="background: #fff5f5;">
        <h4>Horas Produtivas Hoje</h4>
        <div class="value" id="stat-horas" style="color: var(--red); font-variant-numeric: tabular-nums;">0h 00m 00s</div>
      </div>
    </div>

    <input id="search" class="search" placeholder="🔍 Procurar colaborador, cargo ou ID...">

    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
      <h3 style="margin:0">Lista de Colaboradores</h3>
      <button class="add" id="addColabBtn">+ Novo Membro</button>
    </div>

    <table id="colabTable">
      <thead><tr><th>ID</th><th>Nome</th><th>Cargo</th><th>Turno</th><th>Ações</th></tr></thead>
      <tbody id="colabBody"></tbody>
    </table>

    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 30px;">
        <div>
            <h3>Entradas (Hoje)</h3>
            <table id="entradasTable">
              <thead><tr><th>ID</th><th>Nome</th><th>Hora</th><th>Ações</th></tr></thead>
              <tbody id="entradasBody"></tbody>
            </table>
        </div>
        <div>
            <h3>Saídas (Hoje)</h3>
            <table id="saidasTable">
              <thead><tr><th>ID</th><th>Nome</th><th>Hora</th><th>Ações</th></tr></thead>
              <tbody id="saidasBody"></tbody>
            </table>
        </div>
    </div>

    <!-- TABELA: RESUMO DE TEMPO (Apenas Ativos Hoje) -->
    <div style="margin-top: 20px;">
        <h3 style="color: var(--red);">Resumo de Tempo Trabalhado (Apenas Quem Bateu Ponto Hoje)</h3>
        <table id="resumoTempoTable">
            <thead style="background: #fff5f5;">
                <tr>
                    <th>ID</th>
                    <th>Colaborador</th>
                    <th>Estado Atual</th>
                    <th>Tempo Total Acumulado</th>
                </tr>
            </thead>
            <tbody id="resumoTempoBody"></tbody>
        </table>
    </div>
  </main>
</div>

<footer id="mainFooter" class="no-print">
  © 2025 – Gerido por CLX
</footer>

<!-- MODAIS -->
<div id="configModal" class="modal hidden">
  <div class="modal-content">
    <h3>Configurações de Acesso (Master Only)</h3>
    <div style="background: #f8fafc; padding: 15px; border-radius: 8px; margin-bottom: 20px;">
      <h4 style="margin-top: 0;">Novo Acesso Administrativo</h4>
      <div style="display: grid; grid-template-columns: 1fr 1fr 120px; gap: 10px;">
        <input id="newUserLogin" placeholder="Usuário" style="padding:8px; border:1px solid #ddd; border-radius:4px">
        <input id="newUserPass" type="password" placeholder="Senha" style="padding:8px; border:1px solid #ddd; border-radius:4px">
        <button class="add" id="saveUserBtn">Criar</button>
      </div>
    </div>
    <table id="usersTable">
      <thead><tr><th>Utilizador</th><th>Password</th><th>Remover</th></tr></thead>
      <tbody id="usersBody"></tbody>
    </table>
    <button class="secondary" id="fecharConfigBtn" style="margin-top: 10px;">Fechar</button>
  </div>
</div>

<div id="scannerModal" class="modal hidden">
  <div class="modal-content" style="max-width:400px; text-align:center">
    <h3>Leitor de QR Code</h3>
    <div id="video-container">
      <video id="video-preview" autoplay playsinline></video>
      <canvas id="canvas-hidden" class="hidden"></canvas>
      <div class="scanner-line"></div>
    </div>
    <div id="scanner-feedback" style="margin-top:20px; font-weight:700;">Aguardando...</div>
    <button class="secondary" id="fecharScannerBtn" style="width:100%; margin-top: 20px">Cancelar</button>
  </div>
</div>

<div id="qrGalleryModal" class="modal hidden">
  <div class="modal-content" style="max-width: 900px;">
    <div class="no-print" style="display:flex; justify-content:space-between; margin-bottom:20px">
      <h3 style="margin:0">Folha de Crachás</h3>
      <div style="display:flex; gap:10px">
        <button class="download" id="baixarCrachasBtn">🖨️ Imprimir Página</button>
        <button class="secondary" id="fecharGalleryBtn">Fechar</button>
      </div>
    </div>
    <div class="qr-grid" id="qrGridContent"></div>
  </div>
</div>

<div id="colabModal" class="modal hidden">
  <div class="modal-content" style="max-width:400px">
    <h3 id="colabModalTitle">Registar Colaborador</h3>
    <input id="nomeInput" placeholder="Nome Completo" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <input id="emailInput" placeholder="E-mail (Opcional)" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <input id="cargoInput" placeholder="Cargo/Função" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <input id="turnoInput" placeholder="Horário (ex: 09:00 - 18:00)" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <div style="display:flex;gap:10px;justify-content:flex-end;margin-top:15px">
      <button class="secondary" id="cancelColab">Voltar</button>
      <button class="add" id="saveColab">Confirmar</button>
    </div>
  </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.5.0/firebase-app.js";
import {
  getFirestore, collection, setDoc, doc, deleteDoc, onSnapshot, getDocs
} from "https://www.gstatic.com/firebasejs/10.5.0/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSyCpBiFzqOod4K32cWMr5hfx13fw6LGcPVY",
  authDomain: "ponto-eletronico-f35f9.firebaseapp.com",
  projectId: "ponto-eletronico-f35f9",
  storageBucket: "ponto-eletronico-f35f9.firebasestorage.app",
  messagingSenderId: "208638350255",
  appId: "1:208638350255:web:63d016867a67575b5e155a"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

let colaboradores = [];
let pontos = [];
let usuarios = [];
let scanning = false;
let lastScanTime = 0;
let isAppInitialized = false;

/* ---------- AUTH & ACCESS CONTROL ---------- */
onSnapshot(collection(db, "usuarios_admin"), s => {
    usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
    const b = document.getElementById('usersBody'); 
    if(!b) return;
    b.innerHTML = '';
    usuarios.forEach(u => b.innerHTML += `<tr><td>${u.user}</td><td>***</td><td><button class="danger" onclick="window.delUser('${u.id}')">X</button></td></tr>`);
});

document.getElementById('loginBtn').onclick = () => {
    const u = document.getElementById('user').value.trim();
    const p = document.getElementById('pass').value.trim();
    const isMaster = (u === 'CLX' && p === '02072007');
    const isOther = usuarios.some(x => x.user === u && x.pass === p);

    if(isMaster || isOther) {
        if(document.getElementById('rememberMe').checked) {
            localStorage.setItem('ponto_user', u); 
            localStorage.setItem('ponto_pass', p);
            localStorage.setItem('ponto_remember', 'true');
        }
        document.getElementById('loginScreen').classList.add('hidden');
        document.getElementById('mainApp').classList.remove('hidden');
        document.getElementById('mainHeader').classList.remove('hidden');
        const configBtn = document.getElementById('abrirConfigBtn');
        if(isMaster) configBtn.classList.remove('hidden');
        if(!isAppInitialized) init();
    } else {
        document.getElementById('loginMsg').textContent = "Dados inválidos!";
    }
};

document.getElementById('logoutBtn').onclick = () => {
    document.getElementById('mainApp').classList.add('hidden');
    document.getElementById('mainHeader').classList.add('hidden');
    document.getElementById('loginScreen').classList.remove('hidden');
    localStorage.removeItem('ponto_remember');
};

/* ---------- LOGICA CENTRAL ---------- */
function init() {
    isAppInitialized = true;
    onSnapshot(collection(db, "colaboradores"), s => {
        colaboradores = s.docs.map(d => ({id: d.id, ...d.data()}));
        renderColaboradores();
    });
    onSnapshot(collection(db, "pontos"), s => {
        pontos = s.docs.map(d => ({id: d.id, ...d.data()}));
        renderTabelas();
    });
}

function renderColaboradores() {
    const body = document.getElementById('colabBody');
    const term = document.getElementById('search').value.toLowerCase();
    if(!body) return;
    body.innerHTML = '';
    colaboradores.filter(c => c.nome.toLowerCase().includes(term) || c.id.includes(term))
      .sort((a,b) => a.nome.localeCompare(b.nome)).forEach(c => {
        body.innerHTML += `<tr><td>${c.id}</td><td><strong>${c.nome}</strong></td><td>${c.cargo}</td><td>${c.turno}</td>
        <td><div style="display:flex; gap:5px"><button class="add" onclick="window.regManual('${c.id}', 'Entrada')">E</button>
        <button class="secondary" onclick="window.regManual('${c.id}', 'Saída')">S</button>
        <button class="danger" onclick="window.delColab('${c.id}')">X</button></div></td></tr>`;
    });
    updateDashboard();
}

function renderTabelas() {
    const entBody = document.getElementById('entradasBody');
    const saiBody = document.getElementById('saidasBody');
    const term = document.getElementById('search').value.toLowerCase();
    if(!entBody || !saiBody) return;
    entBody.innerHTML = ''; saiBody.innerHTML = '';
    
    const hojeStr = new Date().toLocaleDateString('pt-BR');
    const ptsHoje = pontos.filter(p => p.data === hojeStr && (p.nome.toLowerCase().includes(term) || p.idColab.includes(term)))
                .sort((a,b) => new Date(b.horarioISO) - new Date(a.horarioISO));
    
    ptsHoje.forEach(p => {
        const row = `<tr><td>${p.idColab}</td><td>${p.nome}</td><td>${p.hora}</td><td><button class="danger" onclick="window.delPonto('${p.id}')">X</button></td></tr>`;
        if (p.tipo === 'Entrada') entBody.innerHTML += row; else saiBody.innerHTML += row;
    });
    updateDashboard();
}

/**
 * FORMATAÇÃO DE TEMPO
 */
function formatTime(ms) {
    const totalSegundos = Math.floor(ms / 1000);
    const h = Math.floor(totalSegundos / 3600);
    const m = Math.floor((totalSegundos % 3600) / 60);
    const s = totalSegundos % 60;
    return `${h}h ${m.toString().padStart(2, '0')}m ${s.toString().padStart(2, '0')}s`;
}

/**
 * CÁLCULO DE DASHBOARD E TABELA DE RESUMO
 */
function updateDashboard() {
    const hojeStr = new Date().toLocaleDateString('pt-BR');
    const ptsHoje = pontos.filter(p => p.data === hojeStr);
    
    const totalEl = document.getElementById('stat-total');
    const entEl = document.getElementById('stat-entradas');
    const saiEl = document.getElementById('stat-saidas');
    const horasEl = document.getElementById('stat-horas');
    const resumoBody = document.getElementById('resumoTempoBody');

    if(totalEl) totalEl.textContent = colaboradores.length;
    if(entEl) entEl.textContent = ptsHoje.filter(p => p.tipo === 'Entrada').length;
    if(saiEl) saiEl.textContent = ptsHoje.filter(p => p.tipo === 'Saída').length;
    
    let totalMsGlobal = 0;
    let colaboradoresAtivosCount = 0;
    
    if(resumoBody) resumoBody.innerHTML = '';

    // Filtramos colaboradores que bateram o ponto HOJE
    const idsAtivosHoje = [...new Set(ptsHoje.map(p => p.idColab))];

    if(idsAtivosHoje.length === 0) {
        if(resumoBody) resumoBody.innerHTML = '<tr><td colspan="4" style="text-align:center; padding: 20px; color: var(--muted)">Nenhum registo de ponto para hoje até ao momento.</td></tr>';
    } else {
        colaboradores
            .filter(c => idsAtivosHoje.includes(c.id))
            .sort((a,b) => a.nome.localeCompare(b.nome))
            .forEach(colab => {
                const cPts = ptsHoje
                    .filter(p => p.idColab === colab.id)
                    .sort((a, b) => new Date(a.horarioISO) - new Date(b.horarioISO));

                let colabMs = 0;
                let status = '<span style="color:var(--muted)">Ausente</span>';
                
                // Cálculo de tempo (Entrada -> Saída)
                for(let i = 0; i < cPts.length - 1; i++) {
                    if(cPts[i].tipo === 'Entrada' && cPts[i+1].tipo === 'Saída') {
                        const diff = new Date(cPts[i+1].horarioISO) - new Date(cPts[i].horarioISO);
                        if(diff > 0) colabMs += diff;
                        i++; 
                    }
                }

                // Definir status visual do ponto atual
                if(cPts.length > 0) {
                    const ultimo = cPts[cPts.length - 1];
                    status = ultimo.tipo === 'Entrada' ? 
                        '<span style="color:var(--green); font-weight:700">● Presente</span>' : 
                        '<span style="color:var(--red)">● Fora</span>';
                }

                totalMsGlobal += colabMs;
                colaboradoresAtivosCount++;

                if(resumoBody) {
                    resumoBody.innerHTML += `
                        <tr>
                            <td>${colab.id}</td>
                            <td><strong>${colab.nome}</strong></td>
                            <td>${status}</td>
                            <td style="font-family:monospace; font-weight:700">${formatTime(colabMs)}</td>
                        </tr>
                    `;
                }
            });
    }

    if(horasEl) {
        const novoTexto = formatTime(totalMsGlobal);
        if(horasEl.textContent !== novoTexto) {
            horasEl.textContent = novoTexto;
            horasEl.style.color = '#ff0000';
            setTimeout(() => horasEl.style.color = 'var(--red)', 200);
        }
    }
}

window.regManual = async (idColab, tipo) => {
    const c = colaboradores.find(x => x.id === idColab);
    if (!c) return;
    const now = new Date();
    const p = { 
        id: Date.now().toString(), 
        idColab, 
        nome: c.nome, 
        tipo, 
        data: now.toLocaleDateString('pt-BR'), 
        hora: now.toLocaleTimeString('pt-BR', {hour12:false}), 
        horarioISO: now.toISOString() 
    };
    await setDoc(doc(db, "pontos", p.id), p);
};

window.delColab = async (id) => { if(confirm("Eliminar colaborador?")) await deleteDoc(doc(db, "colaboradores", id)); };
window.delPonto = async (id) => { if(confirm("Apagar registo?")) await deleteDoc(doc(db, "pontos", id)); };
window.delUser = async (id) => { if(confirm("Remover acesso?")) await deleteDoc(doc(db, "usuarios_admin", id)); };

/* ---------- MODAIS & UI ---------- */
document.getElementById('abrirConfigBtn').onclick = () => document.getElementById('configModal').classList.remove('hidden');
document.getElementById('fecharConfigBtn').onclick = () => document.getElementById('configModal').classList.add('hidden');
document.getElementById('addColabBtn').onclick = () => document.getElementById('colabModal').classList.remove('hidden');
document.getElementById('cancelColab').onclick = () => document.getElementById('colabModal').classList.add('hidden');
document.getElementById('search').oninput = () => { renderColaboradores(); renderTabelas(); };

document.getElementById('saveColab').onclick = async () => {
    const n = document.getElementById('nomeInput').value;
    const id = Math.floor(1000 + Math.random() * 9000).toString();
    await setDoc(doc(db, "colaboradores", id), { 
        id, nome: n, 
        email: document.getElementById('emailInput').value, 
        cargo: document.getElementById('cargoInput').value, 
        turno: document.getElementById('turnoInput').value 
    });
    document.getElementById('colabModal').classList.add('hidden');
    document.getElementById('nomeInput').value = ''; 
};

document.getElementById('saveUserBtn').onclick = async () => {
    const u = document.getElementById('newUserLogin').value.trim(), p = document.getElementById('newUserPass').value.trim();
    if(!u || !p) return alert("Dados incompletos!");
    await setDoc(doc(db, "usuarios_admin", Date.now().toString()), { id: Date.now().toString(), user: u, pass: p });
};

document.getElementById('abrirGalleryBtn').onclick = () => {
    const grid = document.getElementById('qrGridContent'); grid.innerHTML = '';
    colaboradores.forEach(c => {
        const card = document.createElement('div'); card.className = 'qr-card';
        card.innerHTML = `<strong>${c.nome}</strong><div class="qr-img" id="qr-${c.id}"></div><small>ID: ${c.id}</small>`;
        grid.appendChild(card);
        new QRCode(document.getElementById(`qr-${c.id}`), { text: String(c.id), width: 120, height: 120 });
    });
    document.getElementById('qrGalleryModal').classList.remove('hidden');
};

document.getElementById('baixarCrachasBtn').onclick = () => window.print();
document.getElementById('fecharGalleryBtn').onclick = () => document.getElementById('qrGalleryModal').classList.add('hidden');

document.getElementById('abrirScannerBtn').onclick = async () => {
    document.getElementById('scannerModal').classList.remove('hidden');
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
        document.getElementById('video-preview').srcObject = stream; scanning = true; requestAnimationFrame(tick);
    } catch(e) { alert("Câmera bloqueada."); }
};

document.getElementById('fecharScannerBtn').onclick = () => {
    scanning = false; if(document.getElementById('video-preview').srcObject) document.getElementById('video-preview').srcObject.getTracks().forEach(t => t.stop());
    document.getElementById('scannerModal').classList.add('hidden');
};

function tick() {
    if (!scanning) return;
    const v = document.getElementById('video-preview');
    if (v.readyState === v.HAVE_ENOUGH_DATA) {
        const can = document.getElementById('canvas-hidden');
        can.height = v.videoHeight; can.width = v.videoWidth;
        const ctx = can.getContext("2d");
        ctx.drawImage(v, 0, 0, can.width, can.height);
        const code = jsQR(ctx.getImageData(0, 0, can.width, can.height).data, can.width, can.height);
        if (code && Date.now() - lastScanTime > 3000) {
            const colab = colaboradores.find(c => String(c.id) === String(code.data));
            if (colab) {
                lastScanTime = Date.now();
                const meusPts = pontos.filter(p => p.idColab === colab.id && p.data === new Date().toLocaleDateString('pt-BR')).sort((a,b) => new Date(b.horarioISO) - new Date(a.horarioISO));
                const tipo = (meusPts.length > 0 && meusPts[0].tipo === 'Entrada') ? 'Saída' : 'Entrada';
                window.regManual(colab.id, tipo);
                document.getElementById('scanner-feedback').textContent = `✓ ${tipo}: ${colab.nome}`;
            }
        }
    }
    requestAnimationFrame(tick);
}

document.getElementById('baixarBtn').onclick = () => {
    const ws = XLSX.utils.json_to_sheet(pontos);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Pontos");
    XLSX.writeFile(wb, "Relatorio_Ponto.xlsx");
};

document.getElementById('limparPontosBtn').onclick = async () => {
    if(confirm("Apagar TODOS os registos?")) {
        const snap = await getDocs(collection(db, "pontos"));
        snap.forEach(d => deleteDoc(doc(db, "pontos", d.id)));
    }
};

setInterval(() => { 
    const clockEl = document.getElementById('clock');
    if(clockEl) clockEl.textContent = new Date().toLocaleTimeString('pt-BR'); 
}, 1000);

window.addEventListener('load', () => {
    if(localStorage.getItem('ponto_remember') === 'true') {
        document.getElementById('user').value = localStorage.getItem('ponto_user');
        document.getElementById('pass').value = localStorage.getItem('ponto_pass');
        document.getElementById('rememberMe').checked = true;
    }
});
</script>
</body>
</html>
