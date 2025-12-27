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
  .settings-btn{background: transparent; color: #fff; padding: 5px; border-radius: 50%;}
  .settings-btn:hover { background: rgba(255,255,255,0.1); }

  main{padding:20px;max-width:1100px;margin:0 auto; flex: 1; width: 100%; box-sizing: border-box;}
  
  footer { text-align: center; padding: 20px; color: var(--muted); font-size: 14px; border-top: 1px solid #e2e8f0; background: #fff; width: 100%; box-sizing: border-box; z-index: 10000; }

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
  .stat-card .value { font-size: 28px; font-weight: 800; margin-top: 5px; color: var(--blue); }
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
  .qr-card { border: 2px solid #eef2f6; padding: 15px; border-radius: 12px; text-align: center; background: #fff; }
  .qr-img { display: flex; justify-content: center; margin: 12px 0; }

  @media print {
    body * { visibility: hidden; }
    #qrGalleryModal, #qrGalleryModal * { visibility: visible; }
    #qrGalleryModal { position: absolute; left: 0; top: 0; width: 100%; z-index: 11000; background: white; }
    .no-print { display: none !important; }
    .qr-grid { grid-template-columns: repeat(3, 1fr) !important; display: grid !important; }
    footer { display: none; }
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
      
      <!-- Campos com autocomplete desativado para evitar preenchimento automático indesejado -->
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
      <button id="abrirConfigBtn" class="settings-btn hidden" title="Configurações de Acesso">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="3"></circle><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 2 2 2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 2 2 2 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 2 2 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path></svg>
      </button>
    </div>
    <div style="display:flex;gap:12px;align-items:center">
      <div id="clock">00:00:00</div>
      <div class="controls">
        <button class="danger" id="limparPontosBtn" title="Limpar todos os registros de ponto">🗑️ Limpar Pontos</button>
        <button class="qr-btn" id="abrirScannerBtn">📸 Scanner</button>
        <button class="secondary" id="abrirGalleryBtn">🖼️ Crachás</button>
        <button class="download" id="baixarBtn">📥 Exportar Excel</button>
        <button class="secondary" id="logoutBtn">Sair</button>
      </div>
    </div>
  </header>

  <main id="mainApp" class="hidden">
    <div class="admin-panel">
      <div class="stat-card">
        <h4>Total Colaboradores</h4>
        <div class="value" id="stat-total">0</div>
      </div>
      <div class="stat-card green">
        <h4>Entradas Hoje</h4>
        <div class="value" id="stat-entradas">0</div>
      </div>
      <div class="stat-card yellow">
        <h4>Saídas Hoje</h4>
        <div class="value" id="stat-saidas">0</div>
      </div>
      <div class="stat-card red">
        <h4>Horas Totais (Hoje)</h4>
        <div class="value" id="stat-horas" style="font-size: 20px;">0h 0m 0s</div>
      </div>
    </div>

    <input id="search" class="search" placeholder="🔍 Filtrar lista de colaboradores, e-mails ou registros...">

    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
      <h3 style="margin:0">Equipe</h3>
      <button class="add" id="addColabBtn">+ Novo Membro</button>
    </div>

    <table id="colabTable">
      <thead><tr><th>ID</th><th>Nome / E-mail</th><th>Cargo</th><th>Turno</th><th>Ações</th></tr></thead>
      <tbody id="colabBody"></tbody>
    </table>

    <h3>Registros de Entrada</h3>
    <table id="entradasTable">
      <thead><tr><th>ID</th><th>Nome</th><th>Data</th><th>Hora</th><th>Ações</th></tr></thead>
      <tbody id="entradasBody"></tbody>
    </table>

    <h3>Registros de Saída</h3>
    <table id="saidasTable">
      <thead><tr><th>ID</th><th>Nome</th><th>Data</th><th>Hora</th><th>Ações</th></tr></thead>
      <tbody id="saidasBody"></tbody>
    </table>

    <h3>Resumo de Tempo Trabalhado (Hoje)</h3>
    <table id="horasTable">
      <thead><tr><th>Colaborador</th><th>Data</th><th>Tempo Total</th></tr></thead>
      <tbody id="horasBody"></tbody>
    </table>
  </main>
</div>

<footer id="mainFooter">
  © 2025 – Criado por CLX
</footer>

<!-- MODAIS -->
<div id="configModal" class="modal hidden">
  <div class="modal-content">
    <h3>Gestão de Acessos</h3>
    <div style="background: #f8fafc; padding: 15px; border-radius: 8px; margin-bottom: 20px;">
      <h4 style="margin-top: 0;">Novo Usuário</h4>
      <div style="display: grid; grid-template-columns: 1fr 1fr 120px; gap: 10px;">
        <input id="newUserLogin" placeholder="Login" style="padding:8px; border:1px solid #ddd; border-radius:4px">
        <input id="newUserPass" type="password" placeholder="Senha" style="padding:8px; border:1px solid #ddd; border-radius:4px">
        <button class="add" id="saveUserBtn">Criar</button>
      </div>
    </div>
    <table id="usersTable">
      <thead><tr><th>Login</th><th>Senha</th><th>Ação</th></tr></thead>
      <tbody id="usersBody"></tbody>
    </table>
    <button class="secondary" id="fecharConfigBtn" style="margin-top: 10px;">Fechar</button>
  </div>
</div>

<div id="scannerModal" class="modal hidden">
  <div class="modal-content" style="max-width:400px; text-align:center">
    <h3>Scanner QR</h3>
    <div id="video-container">
      <video id="video-preview" autoplay playsinline></video>
      <canvas id="canvas-hidden" class="hidden"></canvas>
      <div class="scanner-line"></div>
    </div>
    <div id="scanner-feedback" style="margin-top:20px; font-weight:700;">Aguardando QR Code...</div>
    <button class="secondary" id="fecharScannerBtn" style="width:100%; margin-top: 20px">Fechar</button>
  </div>
</div>

<div id="qrGalleryModal" class="modal hidden">
  <div class="modal-content" style="max-width: 900px;">
    <div class="no-print" style="display:flex; justify-content:space-between; margin-bottom:20px">
      <h3 style="margin:0">Galeria de Crachás</h3>
      <div style="display:flex; gap:10px">
        <button class="download" id="baixarCrachasBtn">💾 Baixar Crachás (PDF)</button>
        <button class="secondary" id="fecharGalleryBtn">Fechar</button>
      </div>
    </div>
    <div class="qr-grid" id="qrGridContent"></div>
  </div>
</div>

<div id="colabModal" class="modal hidden">
  <div class="modal-content" style="max-width:400px">
    <h3 id="colabModalTitle">Novo Registro</h3>
    <input id="nomeInput" placeholder="Nome Completo" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <input id="emailInput" type="email" placeholder="E-mail Corporativo" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <input id="cargoInput" placeholder="Cargo" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <input id="turnoInput" placeholder="Turno (Ex: 08:00 - 17:00)" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; box-sizing: border-box;">
    <div style="display:flex;gap:10px;justify-content:flex-end;margin-top:15px">
      <button class="secondary" id="cancelColab">Voltar</button>
      <button class="add" id="saveColab">Guardar</button>
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

/* ---------- FUNÇÕES GLOBAIS ---------- */
window.delColab = async (id) => {
    if(confirm("Deseja remover este colaborador e todos os seus registros?")) {
        await deleteDoc(doc(db, "colaboradores", id));
    }
};

window.delPonto = async (id) => {
    if(confirm("Deseja apagar este registro de ponto?")) {
        await deleteDoc(doc(db, "pontos", id));
    }
};

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

window.delUser = async (id) => {
    if(confirm("Remover acesso?")) await deleteDoc(doc(db, "usuarios_admin", id));
};

/* ---------- RENDERIZAÇÃO ---------- */
function renderColaboradores() {
    const body = document.getElementById('colabBody');
    const term = document.getElementById('search').value.toLowerCase();
    body.innerHTML = '';
    
    colaboradores
      .filter(c => c.nome.toLowerCase().includes(term) || (c.email || "").toLowerCase().includes(term) || c.id.includes(term))
      .sort((a,b) => a.nome.localeCompare(b.nome))
      .forEach(c => {
        const emailDisplay = c.email ? `<br><small style="color:var(--muted)">${c.email}</small>` : '';
        body.innerHTML += `<tr>
        <td>${c.id}</td>
        <td><strong>${c.nome}</strong>${emailDisplay}</td>
        <td>${c.cargo}</td>
        <td>${c.turno}</td>
        <td>
            <div style="display:flex; gap:5px">
              <button class="add" onclick="window.regManual('${c.id}', 'Entrada')" title="Entrada">E</button>
              <button class="secondary" onclick="window.regManual('${c.id}', 'Saída')" title="Saída">S</button>
              <button class="danger" onclick="window.delColab('${c.id}')" title="Excluir Colaborador">X</button>
            </div>
        </td></tr>`;
    });
    updateDashboard();
}

function renderTabelas() {
    const entBody = document.getElementById('entradasBody');
    const saiBody = document.getElementById('saidasBody');
    const horasBody = document.getElementById('horasBody');
    const term = document.getElementById('search').value.toLowerCase();
    
    if(!entBody || !saiBody || !horasBody) return;

    entBody.innerHTML = ''; saiBody.innerHTML = ''; horasBody.innerHTML = '';
    
    const ptsFiltrados = pontos
      .filter(p => p.nome.toLowerCase().includes(term) || p.idColab.includes(term))
      .sort((a,b) => new Date(b.horarioISO) - new Date(a.horarioISO));

    ptsFiltrados.forEach(p => {
        const row = `<tr><td>${p.idColab}</td><td>${p.nome}</td><td>${p.data}</td><td>${p.hora}</td><td><button class="danger" onclick="window.delPonto('${p.id}')">Excluir</button></td></tr>`;
        if (p.tipo === 'Entrada') entBody.innerHTML += row; else saiBody.innerHTML += row;
    });

    const hoje = new Date().toLocaleDateString('pt-BR');
    const ptsHoje = pontos.filter(p => p.data === hoje);
    
    const resumo = {};
    ptsHoje.forEach(p => {
        if(!resumo[p.idColab]) resumo[p.idColab] = { nome: p.nome, data: p.data, entrada: null, saida: null };
        if(p.tipo === 'Entrada') resumo[p.idColab].entrada = new Date(p.horarioISO);
        if(p.tipo === 'Saída') resumo[p.idColab].saida = new Date(p.horarioISO);
    });

    let totalMsGeral = 0;
    Object.values(resumo).forEach(r => {
        let tempoTxt = "Incompleto";
        if(r.entrada && r.saida) {
            const diff = r.saida - r.entrada;
            if(diff > 0) {
              totalMsGeral += diff;
              const h = Math.floor(diff / 3600000);
              const m = Math.floor((diff % 3600000) / 60000);
              const s = Math.floor((diff % 60000) / 1000);
              tempoTxt = `${h}h ${m}m ${s}s`;
            }
        }
        horasBody.innerHTML += `<tr><td>${r.nome}</td><td>${r.data}</td><td>${tempoTxt}</td></tr>`;
    });

    const hG = Math.floor(totalMsGeral / 3600000);
    const mG = Math.floor((totalMsGeral % 3600000) / 60000);
    const sG = Math.floor((totalMsGeral % 60000) / 1000);
    document.getElementById('stat-horas').textContent = `${hG}h ${mG}m ${sG}s`;

    updateDashboard();
}

function updateDashboard() {
    const hojeStr = new Date().toLocaleDateString('pt-BR');
    const ptsHoje = pontos.filter(p => p.data === hojeStr);
    const totalEl = document.getElementById('stat-total');
    if(totalEl) totalEl.textContent = colaboradores.length;
    
    const entEl = document.getElementById('stat-entradas');
    if(entEl) entEl.textContent = ptsHoje.filter(p => p.tipo === 'Entrada').length;
    
    const saiEl = document.getElementById('stat-saidas');
    if(saiEl) saiEl.textContent = ptsHoje.filter(p => p.tipo === 'Saída').length;
}

/* ---------- LOGIN E AUTH ---------- */
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
            localStorage.setItem('ponto_user', u); localStorage.setItem('ponto_pass', p);
        } else {
            localStorage.removeItem('ponto_user'); localStorage.removeItem('ponto_pass');
        }
        
        document.getElementById('loginScreen').classList.add('hidden');
        document.getElementById('mainApp').classList.remove('hidden');
        document.getElementById('mainHeader').classList.remove('hidden');
        
        if(isMaster) document.getElementById('abrirConfigBtn').classList.remove('hidden');
        
        if(!isAppInitialized) init();
    } else {
        document.getElementById('loginMsg').textContent = "Usuário ou Senha incorretos!";
        setTimeout(() => { if(document.getElementById('loginMsg')) document.getElementById('loginMsg').textContent = ""; }, 3000);
    }
};

/* ---------- CORE INIT ---------- */
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

/* ---------- EVENTOS UI ---------- */
document.getElementById('search').oninput = () => {
    renderColaboradores();
    renderTabelas();
};

document.getElementById('saveColab').onclick = async () => {
    const n = document.getElementById('nomeInput').value, 
          em = document.getElementById('emailInput').value,
          c = document.getElementById('cargoInput').value, 
          t = document.getElementById('turnoInput').value;
    if(!n) return alert("Nome é obrigatório");
    const id = Math.floor(1000 + Math.random() * 9000).toString();
    await setDoc(doc(db, "colaboradores", id), { id, nome: n, email: em, cargo: c, turno: t });
    
    document.getElementById('nomeInput').value = '';
    document.getElementById('emailInput').value = '';
    document.getElementById('cargoInput').value = '';
    document.getElementById('turnoInput').value = '';
    document.getElementById('colabModal').classList.add('hidden');
};

document.getElementById('saveUserBtn').onclick = async () => {
    const u = document.getElementById('newUserLogin').value.trim(), p = document.getElementById('newUserPass').value.trim();
    if(!u || !p) return alert("Preencha login e senha");
    const id = Date.now().toString();
    await setDoc(doc(db, "usuarios_admin", id), { id, user: u, pass: p });
    document.getElementById('newUserLogin').value = '';
    document.getElementById('newUserPass').value = '';
};

function renderCrachas() {
    const grid = document.getElementById('qrGridContent'); grid.innerHTML = '';
    colaboradores.forEach(c => {
        const card = document.createElement('div'); card.className = 'qr-card';
        const emailLine = c.email ? `<small style="display:block;margin-bottom:5px;color:var(--muted)">${c.email}</small>` : '';
        card.innerHTML = `<strong>${c.nome}</strong>${emailLine}<small>${c.cargo}</small><div class="qr-img" id="qr-${c.id}"></div><small>ID: ${c.id}</small>`;
        grid.appendChild(card);
        new QRCode(document.getElementById(`qr-${c.id}`), { text: String(c.id), width: 120, height: 120 });
    });
}

/* NOVO: LIMPAR PONTOS */
document.getElementById('limparPontosBtn').onclick = async () => {
    if(confirm("ATENÇÃO: Deseja apagar TODOS os registros de entrada e saída do sistema? Esta ação não pode ser desfeita.")) {
        const querySnapshot = await getDocs(collection(db, "pontos"));
        const promises = [];
        querySnapshot.forEach((docSnap) => {
            promises.push(deleteDoc(doc(db, "pontos", docSnap.id)));
        });
        await Promise.all(promises);
        alert("Todos os registros de ponto foram removidos.");
    }
};

/* NOVO: BAIXAR CRACHÁS */
document.getElementById('baixarCrachasBtn').onclick = () => {
    window.print();
};

document.getElementById('abrirGalleryBtn').onclick = () => { renderCrachas(); document.getElementById('qrGalleryModal').classList.remove('hidden'); };
document.getElementById('fecharGalleryBtn').onclick = () => document.getElementById('qrGalleryModal').classList.add('hidden');
document.getElementById('abrirScannerBtn').onclick = async () => {
    document.getElementById('scannerModal').classList.remove('hidden');
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
        document.getElementById('video-preview').srcObject = stream; scanning = true; requestAnimationFrame(tick);
    } catch(e) { alert("Câmera não disponível"); }
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
                const meusPts = pontos.filter(p => p.idColab === colab.id).sort((a,b) => new Date(b.horarioISO) - new Date(a.horarioISO));
                const tipo = (meusPts.length > 0 && meusPts[0].tipo === 'Entrada') ? 'Saída' : 'Entrada';
                window.regManual(colab.id, tipo);
                document.getElementById('scanner-feedback').textContent = `REGISTRADO: ${tipo} - ${colab.nome}`;
                setTimeout(() => { if(scanning) document.getElementById('scanner-feedback').textContent = "Aguardando QR Code..."; }, 2000);
            }
        }
    }
    requestAnimationFrame(tick);
}

document.getElementById('baixarBtn').onclick = () => {
    const ws = XLSX.utils.json_to_sheet(pontos);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Relatório");
    XLSX.writeFile(wb, "Registros_Ponto.xlsx");
};

document.getElementById('abrirConfigBtn').onclick = () => document.getElementById('configModal').classList.remove('hidden');
document.getElementById('fecharConfigBtn').onclick = () => document.getElementById('configModal').classList.add('hidden');
document.getElementById('addColabBtn').onclick = () => document.getElementById('colabModal').classList.remove('hidden');
document.getElementById('cancelColab').onclick = () => document.getElementById('colabModal').classList.add('hidden');

document.getElementById('logoutBtn').onclick = () => {
    document.getElementById('mainApp').classList.add('hidden');
    document.getElementById('mainHeader').classList.add('hidden');
    document.getElementById('configModal').classList.add('hidden');
    document.getElementById('abrirConfigBtn').classList.add('hidden');
    document.getElementById('loginScreen').classList.remove('hidden');
    
    document.getElementById('user').value = '';
    document.getElementById('pass').value = '';
    
    if(!localStorage.getItem('ponto_user')) {
        document.getElementById('rememberMe').checked = false;
    }
};

/* MODIFICAÇÃO: Garantir limpeza total no carregamento inicial */
window.onload = () => {
    // Limpa campos para evitar que o navegador preencha dados antigos antes do JS rodar
    document.getElementById('user').value = '';
    document.getElementById('pass').value = '';

    // Só preenche se o utilizador pediu explicitamente no passado
    const rU = localStorage.getItem('ponto_user');
    const rP = localStorage.getItem('ponto_pass');
    
    if(rU && rP) { 
        document.getElementById('user').value = rU; 
        document.getElementById('pass').value = rP; 
        document.getElementById('rememberMe').checked = true; 
    } else {
        document.getElementById('rememberMe').checked = false;
    }
};

setInterval(() => { 
    const clockEl = document.getElementById('clock');
    if(clockEl) clockEl.textContent = new Date().toLocaleTimeString('pt-BR'); 
}, 1000);
</script>
</body>
</html>
