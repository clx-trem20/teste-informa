<html lang="pt-BR">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Ponto Eletrônico - Admin & Configurações</title>
    <script src="https://cdn.sheetjs.com/xlsx-latest/package/dist/xlsx.full.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <style>
        :root {
            --blue: #0b4f78; --green: #2e9b4f; --yellow: #ffb739; --red: #ef5350;
            --muted: #6b7280; --card: #ffffff; --bg: #f4f7fb; --indigo: #6366f1;
        }
        body { font-family: Inter, system-ui, -apple-system, Arial, sans-serif; background: var(--bg); margin: 0; color: #111; display: flex; flex-direction: column; min-height: 100vh; }
        #app-container { flex: 1; display: flex; flex-direction: column; width: 100%; position: relative; }
        header { background: linear-gradient(90deg, var(--blue), #0f6b96); color: #fff; padding: 12px 18px; display: flex; align-items: center; justify-content: space-between; gap: 12px; flex-wrap: wrap; }
        .logo { font-weight: 700; font-size: 18px; }
        #clock { font-weight: 700; font-variant-numeric: tabular-nums; }
        .controls { display: flex; gap: 8px; align-items: center; }
        button { padding: 8px 12px; border: none; border-radius: 8px; cursor: pointer; font-weight: 600; transition: all 0.2s; display: flex; align-items: center; gap: 6px; }
        button:hover { opacity: 0.9; transform: translateY(-1px); }
        .add { background: var(--green); color: #fff; }
        .secondary { background: #e5e7eb; color: #111; }
        .download { background: var(--yellow); color: #111; }
        .danger { background: var(--red); color: #fff; }
        .info-btn { background: var(--blue); color: #fff; }
        .qr-btn { background: var(--indigo); color: #fff; }
        .settings-btn { background: transparent; color: #fff; padding: 5px; border-radius: 50%; border: 1px solid rgba(255,255,255,0.2); }
        .settings-btn:hover { background: rgba(255,255,255,0.1); }
        main { padding: 20px; max-width: 1200px; margin: 0 auto; flex: 1; width: 100%; box-sizing: border-box; }
        footer { text-align: center; padding: 20px; color: var(--muted); font-size: 14px; border-top: 1px solid #e2e8f0; background: #fff; width: 100%; box-sizing: border-box; }
        #loginScreen { position: fixed; inset: 0; background: var(--blue); display: flex; align-items: center; justify-content: center; z-index: 11000; padding: 20px; }
        .login-card { background: #fff; padding: 40px 30px; border-radius: 16px; width: 100%; max-width: 400px; text-align: center; box-shadow: 0 20px 50px rgba(0,0,0,0.3); }
        .admin-panel { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 25px; margin-top: 10px; }
        .stat-card { background: var(--card); padding: 20px; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); border-left: 5px solid var(--blue); }
        .stat-card h4 { margin: 0; color: var(--muted); font-size: 13px; text-transform: uppercase; letter-spacing: 0.5px; }
        .stat-card .value { font-size: 28px; font-weight: 800; margin-top: 5px; color: var(--blue); }
        .stat-card.green { border-left-color: var(--green); }
        .stat-card.yellow { border-left-color: var(--yellow); }
        .stat-card.red { border-left-color: var(--red); }
        .search { width: 100%; padding: 12px; border-radius: 8px; border: 1px solid #d1d5db; margin-bottom: 20px; font-size: 16px; box-sizing: border-box; }
        table { width: 100%; border-collapse: collapse; background: var(--card); border-radius: 10px; overflow: hidden; box-shadow: 0 6px 24px rgba(15,23,42,0.06); margin-bottom: 30px; }
        th, td { padding: 12px; border-bottom: 1px solid #eef2f6; text-align: left; font-size: 14px; }
        th { background: #fbfdfe; font-weight: 700; color: var(--blue); }
        .modal { position: fixed; inset: 0; background: rgba(0,0,0,.45); display: flex; align-items: center; justify-content: center; z-index: 9999; }
        .modal-content { background: #fff; padding: 22px; border-radius: 12px; width: 95%; max-width: 800px; max-height: 90vh; overflow-y: auto; }
        .hidden { display: none !important; }
        #video-container { position: relative; width: 100%; max-width: 350px; aspect-ratio: 1; margin: 0 auto; background: #000; border-radius: 12px; overflow: hidden; border: 4px solid var(--indigo); }
        #video-preview { width: 100%; height: 100%; object-fit: cover; transform: scaleX(-1); }
        .scanner-line { position: absolute; top: 0; left: 0; width: 100%; height: 3px; background: var(--indigo); animation: scan 2s infinite linear; }
        @keyframes scan { 0% { top: 0%; } 100% { top: 100%; } }
        .qr-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 15px; }
        .qr-card { border: 2px solid #eef2f6; padding: 15px; border-radius: 12px; text-align: center; background: #fff; page-break-inside: avoid; }
        .qr-img { display: flex; justify-content: center; margin: 12px 0; }
        .report-badge { padding: 4px 8px; border-radius: 4px; font-size: 12px; font-weight: bold; }
        .badge-entrada { background: #dcfce7; color: #166534; }
        .badge-saida { background: #fee2e2; color: #991b1b; }
        .turno-text { font-size: 12px; color: var(--muted); font-weight: 500; }
        
        @media print {
            body { background: white; }
            #app-container, header, footer, main, .no-print, .modal:not(#qrGalleryModal):not(#reportModal) { 
                display: none !important; 
            }
            .modal { 
                position: relative !important; 
                background: none !important; 
                display: block !important; 
                inset: auto !important;
                z-index: auto !important;
            }
            .modal-content { 
                width: 100% !important; 
                max-width: none !important; 
                box-shadow: none !important; 
                padding: 0 !important;
                margin: 0 !important;
            }
            .qr-grid { 
                display: grid !important; 
                grid-template-columns: 1fr 1fr 1fr !important; 
                gap: 10px !important; 
            }
            .qr-card { 
                border: 1px solid #eee !important;
                break-inside: avoid;
            }
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
            <input id="user" placeholder="Usuário" autocomplete="off" style="width:100%;padding:14px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; font-size: 16px;">
            <input id="pass" type="password" placeholder="Senha" autocomplete="new-password" style="width:100%;padding:14px;margin:8px 0;border-radius:8px;border:1px solid #e5e7eb; font-size: 16px;">
            <div style="display: flex; align-items: center; gap: 8px; margin: 15px 0; font-size: 15px; color: var(--muted);">
                <input type="checkbox" id="rememberMe"> <label for="rememberMe">Lembrar login</label>
            </div>
            <button id="loginBtn" class="add" style="width:100%; padding: 14px; justify-content: center;">Entrar no Sistema</button>
            <p id="loginMsg" style="color:var(--red);margin-top:15px; font-size: 14px; font-weight: 600;"></p>
        </div>
    </div>

    <!-- Cabeçalho -->
    <header id="mainHeader" class="hidden">
        <div style="display:flex;gap:12px;align-items:center">
            <div class="logo">Sistema de Ponto</div>
            <button id="abrirConfigBtn" class="settings-btn hidden" title="Gerenciar Usuários">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="3"></circle><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 2 2 2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 2 2 2 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 2 2 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path></svg>
            </button>
        </div>
        <div style="display:flex;gap:12px;align-items:center">
            <div id="clock">00:00:00</div>
            <div class="controls">
                <button class="danger" id="limparPontosBtn">🗑️ Limpar</button>
                <button class="qr-btn" id="abrirScannerBtn">📸 Scanner</button>
                <button class="secondary" id="abrirGalleryBtn">🖼️ Crachás</button>
                <button class="download" id="baixarBtn">📥 Excel</button>
                <button class="secondary" id="logoutBtn">Sair</button>
            </div>
        </div>
    </header>

    <main id="mainApp" class="hidden">
        <div class="admin-panel">
            <div class="stat-card"><h4>Total Equipe</h4><div class="value" id="stat-total">0</div></div>
            <div class="stat-card green"><h4>Entradas Hoje</h4><div class="value" id="stat-entradas">0</div></div>
            <div class="stat-card yellow"><h4>Saídas Hoje</h4><div class="value" id="stat-saidas">0</div></div>
            <div class="stat-card red" style="background: #fff5f5;">
                <h4>Produtividade Hoje</h4>
                <div class="value" id="stat-horas" style="color: var(--red);">0h 00m 00s</div>
            </div>
        </div>

        <input id="search" class="search" placeholder="🔍 Procurar colaborador por nome, email ou cargo...">

        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
            <h3 style="margin:0">Lista de Colaboradores</h3>
            <button class="add" id="addColabBtn">+ Novo</button>
        </div>

        <table id="colabTableMain">
            <thead>
                <tr>
                    <th style="width: 100px;">ID</th>
                    <th>Nome / E-mail</th>
                    <th style="width: 180px;">Cargo</th>
                    <th style="width: 180px;">Turno/Horário</th>
                    <th style="width: 180px; text-align: center;">Ações</th>
                </tr>
            </thead>
            <tbody id="colabBody"></tbody>
        </table>

        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px;">
            <div><h3>Entradas Recentes</h3><table><tbody id="entradasBody"></tbody></table></div>
            <div><h3>Saídas Recentes</h3><table><tbody id="saidasBody"></tbody></table></div>
        </div>

        <h3 style="color: var(--red);">Monitoramento em Tempo Real</h3>
        <table id="resumoTempoTable">
            <thead><tr><th>ID</th><th>Nome</th><th>Estado</th><th>Total Horas Hoje</th></tr></thead>
            <tbody id="resumoTempoBody"></tbody>
        </table>
    </main>
</div>

<footer id="mainFooter" class="no-print">© 2025 – Gerido por CLX</footer>

<!-- MODAIS -->
<div id="colabModal" class="modal hidden no-print">
    <div class="modal-content" style="max-width:400px">
        <h3>Registar Colaborador</h3>
        <input id="nomeInput" placeholder="Nome Completo" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #ddd;">
        <input id="emailInput" type="email" placeholder="E-mail" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #ddd;">
        <input id="cargoInput" placeholder="Cargo" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #ddd;">
        <input id="turnoInput" placeholder="Horário (ex: 08:00 - 17:00)" style="width:100%;padding:12px;margin:8px 0;border-radius:8px;border:1px solid #ddd;">
        <div style="display:flex;gap:10px;justify-content:flex-end;margin-top:15px">
            <button class="secondary" id="cancelColab">Voltar</button>
            <button class="add" id="saveColab">Salvar Colaborador</button>
        </div>
    </div>
</div>

<div id="scannerModal" class="modal hidden no-print">
    <div class="modal-content" style="max-width:400px; text-align:center">
        <h3>Leitor de Crachá</h3>
        <div id="video-container">
            <video id="video-preview" autoplay playsinline></video>
            <canvas id="canvas-hidden" class="hidden"></canvas>
            <div class="scanner-line"></div>
        </div>
        <div id="scanner-feedback" style="margin-top:20px; font-weight:700; color: var(--indigo); font-size: 18px;">Aponte o QR Code...</div>
        <button class="secondary" id="fecharScannerBtn" style="width:100%; margin-top:20px">Fechar Câmera</button>
    </div>
</div>

<div id="qrGalleryModal" class="modal hidden">
    <div class="modal-content">
        <div style="display:flex;justify-content:space-between;margin-bottom:20px" class="no-print">
            <h3>Crachás Gerados</h3>
            <div style="display:flex; gap: 8px;">
                <button class="download" onclick="window.print()">🖨️ Imprimir Tudo</button>
                <button class="secondary" id="fecharGalleryBtn">Fechar</button>
            </div>
        </div>
        <div class="qr-grid" id="qrGridContent"></div>
    </div>
</div>

<div id="configModal" class="modal hidden no-print">
    <div class="modal-content">
        <h3>Contas Administrativas</h3>
        <div style="display:grid;grid-template-columns:1fr 1fr 100px;gap:10px;margin-bottom:15px">
            <input id="newUserLogin" placeholder="Login">
            <input id="newUserPass" type="password" placeholder="Senha">
            <button class="add" id="saveUserBtn">Adicionar</button>
        </div>
        <table>
            <thead><tr><th>Usuário</th><th>Senha</th><th>Ação</th></tr></thead>
            <tbody id="usersBody"></tbody>
        </table>
        <button class="secondary" id="fecharConfigBtn" style="margin-top: 15px;">Fechar</button>
    </div>
</div>

<div id="reportModal" class="modal hidden">
    <div class="modal-content">
        <div style="display:flex;justify-content:space-between;align-items:center; border-bottom: 2px solid #eee; padding-bottom: 10px; margin-bottom: 15px;">
            <div>
                <h3 id="reportName" style="margin:0">Relatório</h3>
                <p id="reportTurno" class="turno-text" style="margin: 5px 0 0 0;"></p>
            </div>
            <button class="secondary no-print" id="fecharReportBtn">X</button>
        </div>
        <div id="reportTotalHours" style="font-size:32px; font-weight:800; color:var(--red); text-align:center; margin: 20px 0;">0h 00m</div>
        <table style="margin-top:15px">
            <thead><tr><th>Data</th><th>Hora</th><th>Tipo de Registro</th></tr></thead>
            <tbody id="reportTableBody"></tbody>
        </table>
        <button class="download no-print" onclick="window.print()" style="margin-top: 20px; width: 100%; justify-content: center;">🖨️ Imprimir Relatório</button>
    </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.5.0/firebase-app.js";
import { getFirestore, collection, setDoc, doc, deleteDoc, onSnapshot, getDocs } from "https://www.gstatic.com/firebasejs/10.5.0/firebase-firestore.js";

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

let colaboradores = [], pontos = [], usuarios = [];
let scanning = false, lastScanTime = 0, isAppInitialized = false;

/* --- AUTH --- */
onSnapshot(collection(db, "usuarios_admin"), s => {
    usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
    const b = document.getElementById('usersBody');
    if(!b) return; b.innerHTML = '';
    usuarios.forEach(u => b.innerHTML += `<tr><td>${u.user}</td><td>***</td><td><button class="danger" onclick="window.delUser('${u.id}')">X</button></td></tr>`);
});

const login = (u, p) => {
    const isMaster = (u === 'CLX' && p === '02072007');
    const isOther = usuarios.some(x => x.user === u && x.pass === p);
    if(isMaster || isOther) {
        if(document.getElementById('rememberMe').checked) {
            localStorage.setItem('ponto_user', u); localStorage.setItem('ponto_pass', p); localStorage.setItem('ponto_remember', 'true');
        }
        document.getElementById('loginScreen').classList.add('hidden');
        document.getElementById('mainApp').classList.remove('hidden');
        document.getElementById('mainHeader').classList.remove('hidden');
        if(isMaster) document.getElementById('abrirConfigBtn').classList.remove('hidden');
        if(!isAppInitialized) init();
    } else {
        document.getElementById('loginMsg').textContent = "Credenciais inválidas!";
    }
};

document.getElementById('loginBtn').onclick = () => login(document.getElementById('user').value.trim(), document.getElementById('pass').value.trim());
document.getElementById('logoutBtn').onclick = () => { localStorage.removeItem('ponto_remember'); location.reload(); };

/* --- CORE --- */
function init() {
    isAppInitialized = true;
    onSnapshot(collection(db, "colaboradores"), s => {
        colaboradores = s.docs.map(d => ({id: d.id, ...d.data()}));
        renderColabs();
    });
    onSnapshot(collection(db, "pontos"), s => {
        pontos = s.docs.map(d => ({id: d.id, ...d.data()}));
        renderTabelas();
    });
}

const renderColabs = () => {
    const body = document.getElementById('colabBody');
    const term = document.getElementById('search').value.toLowerCase();
    body.innerHTML = '';
    
    colaboradores.filter(c => 
        c.nome.toLowerCase().includes(term) || 
        (c.email && c.email.toLowerCase().includes(term)) ||
        (c.cargo && c.cargo.toLowerCase().includes(term)) ||
        (c.id && c.id.includes(term))
    )
    .forEach(c => {
        body.innerHTML += `<tr>
        <td style="font-weight:700; padding-right: 20px;">${c.id}</td>
        <td>
            <div style="font-weight:600">${c.nome}</div>
            <div style="color:var(--muted); font-size:12px">${c.email || '-'}</div>
        </td>
        <td>${c.cargo}</td>
        <td class="turno-text">${c.turno || '-'}</td>
        <td>
            <div style="display:flex; gap:6px; justify-content: center;">
                <button class="info-btn" title="Ver Relatório" onclick="window.abrirRelatorio('${c.id}')">📄</button>
                <button class="add" title="Entrada Manual" onclick="window.regManual('${c.id}','Entrada')">E</button>
                <button class="secondary" title="Saída Manual" onclick="window.regManual('${c.id}','Saída')">S</button>
                <button class="danger" title="Apagar" onclick="window.delColab('${c.id}')">X</button>
            </div>
        </td></tr>`;
    });
    updateDash();
};

const renderTabelas = () => {
    const e = document.getElementById('entradasBody'), s = document.getElementById('saidasBody');
    e.innerHTML = ''; s.innerHTML = '';
    const hoje = new Date().toLocaleDateString('pt-BR');
    
    const ptsHoje = pontos.filter(p => p.data === hoje).sort((a,b) => b.id - a.id);
    
    ptsHoje.forEach(p => {
        const row = `<tr><td>${p.idColab}</td><td><b>${p.nome}</b></td><td>${p.hora}</td><td><button class="danger no-print" style="padding:4px 8px" onclick="window.delPonto('${p.id}')">X</button></td></tr>`;
        if(p.tipo === 'Entrada') e.innerHTML += row; else s.innerHTML += row;
    });
    updateDash();
};

const formatTime = ms => {
    let s = Math.floor(ms/1000);
    let h = Math.floor(s/3600); s%=3600;
    let m = Math.floor(s/60); s%=60;
    return `${h}h ${m}m ${s.toString().padStart(2, '0')}s`;
};

const updateDash = () => {
    const hoje = new Date().toLocaleDateString('pt-BR');
    const ptsHoje = pontos.filter(p => p.data === hoje);
    document.getElementById('stat-total').textContent = colaboradores.length;
    document.getElementById('stat-entradas').textContent = ptsHoje.filter(p => p.tipo === 'Entrada').length;
    document.getElementById('stat-saidas').textContent = ptsHoje.filter(p => p.tipo === 'Saída').length;
    
    let totalMs = 0;
    const resBody = document.getElementById('resumoTempoBody');
    resBody.innerHTML = '';
    
    colaboradores.forEach(c => {
        const cPts = ptsHoje.filter(p => p.idColab === c.id).sort((a,b) => new Date(a.horarioISO) - new Date(b.horarioISO));
        let cMs = 0;
        for(let i=0; i<cPts.length-1; i++) {
            if(cPts[i].tipo==='Entrada' && cPts[i+1].tipo==='Saída') {
                cMs += new Date(cPts[i+1].horarioISO) - new Date(cPts[i].horarioISO);
                i++;
            }
        }
        totalMs += cMs;
        if(cPts.length > 0) {
            const status = cPts[cPts.length-1].tipo === 'Entrada' ? '✅ Presente' : '🔴 Saiu';
            resBody.innerHTML += `<tr><td>${c.id}</td><td><b>${c.nome}</b></td><td>${status}</td><td>${formatTime(cMs)}</td></tr>`;
        }
    });
    document.getElementById('stat-horas').textContent = formatTime(totalMs);
};

/* --- ACTIONS --- */
window.regManual = async (id, tipo) => {
    const c = colaboradores.find(x => x.id === id);
    if(!c) return;
    const now = new Date();
    const p = { id: Date.now().toString(), idColab: id, nome: c.nome, tipo, data: now.toLocaleDateString('pt-BR'), hora: now.toLocaleTimeString('pt-BR'), horarioISO: now.toISOString() };
    await setDoc(doc(db, "pontos", p.id), p);
};

window.abrirRelatorio = id => {
    const c = colaboradores.find(x => x.id === id);
    if(!c) return;
    
    document.getElementById('reportName').textContent = c.nome;
    document.getElementById('reportTurno').textContent = `Horário Definido: ${c.turno || 'Não informado'} | Cargo: ${c.cargo || 'Não informado'}`;
    
    const meus = pontos.filter(p => p.idColab === id).sort((a,b) => new Date(b.horarioISO) - new Date(a.horarioISO));
    const body = document.getElementById('reportTableBody');
    body.innerHTML = '';
    
    let totalMs = 0;
    const ordenados = [...meus].sort((a,b) => new Date(a.horarioISO) - new Date(b.horarioISO));
    for(let i=0; i<ordenados.length-1; i++) {
        if(ordenados[i].tipo==='Entrada' && ordenados[i+1].tipo==='Saída') {
            totalMs += new Date(ordenados[i+1].horarioISO) - new Date(ordenados[i].horarioISO);
            i++;
        }
    }
    document.getElementById('reportTotalHours').textContent = formatTime(totalMs);

    meus.forEach(p => {
        body.innerHTML += `<tr><td>${p.data}</td><td>${p.hora}</td><td><span class="report-badge ${p.tipo==='Entrada'?'badge-entrada':'badge-saida'}">${p.tipo}</span></td></tr>`;
    });
    document.getElementById('reportModal').classList.remove('hidden');
};

window.delColab = async id => { if(confirm("Deseja eliminar este colaborador?")) await deleteDoc(doc(db, "colaboradores", id)); };
window.delPonto = async id => { if(confirm("Remover este registo?")) await deleteDoc(doc(db, "pontos", id)); };
window.delUser = async id => { if(confirm("Remover este admin?")) await deleteDoc(doc(db, "usuarios_admin", id)); };

document.getElementById('addColabBtn').onclick = () => document.getElementById('colabModal').classList.remove('hidden');
document.getElementById('cancelColab').onclick = () => document.getElementById('colabModal').classList.add('hidden');
document.getElementById('fecharReportBtn').onclick = () => document.getElementById('reportModal').classList.add('hidden');
document.getElementById('abrirConfigBtn').onclick = () => document.getElementById('configModal').classList.remove('hidden');
document.getElementById('fecharConfigBtn').onclick = () => document.getElementById('configModal').classList.add('hidden');
document.getElementById('search').oninput = renderColabs;

document.getElementById('saveColab').onclick = async () => {
    const id = Math.floor(1000 + Math.random() * 9000).toString();
    const nome = document.getElementById('nomeInput').value.trim();
    const email = document.getElementById('emailInput').value.trim();
    const cargo = document.getElementById('cargoInput').value.trim();
    const turno = document.getElementById('turnoInput').value.trim();

    if(!nome) { alert("O nome é obrigatório!"); return; }

    await setDoc(doc(db, "colaboradores", id), { id, nome, email, cargo, turno });
    document.getElementById('colabModal').classList.add('hidden');
    document.querySelectorAll('#colabModal input').forEach(i => i.value = '');
};

document.getElementById('saveUserBtn').onclick = async () => {
    const u = document.getElementById('newUserLogin').value, p = document.getElementById('newUserPass').value;
    if(!u || !p) return;
    await setDoc(doc(db, "usuarios_admin", Date.now().toString()), { user: u, pass: p });
    document.getElementById('newUserLogin').value = ''; document.getElementById('newUserPass').value = '';
};

document.getElementById('abrirGalleryBtn').onclick = () => {
    const g = document.getElementById('qrGridContent'); g.innerHTML = '';
    colaboradores.forEach(c => {
        const d = document.createElement('div'); d.className='qr-card';
        d.innerHTML = `<div style="font-size:14px; margin-bottom:5px"><b>${c.nome}</b></div><div id="q-${c.id}" class="qr-img"></div><div style="font-size:11px; margin-top:5px; color:var(--muted)">ID: ${c.id} | ${c.cargo || '-'}</div>`;
        g.appendChild(d);
        new QRCode(document.getElementById(`q-${c.id}`), { text: c.id, width: 120, height: 120 });
    });
    document.getElementById('qrGalleryModal').classList.remove('hidden');
};
document.getElementById('fecharGalleryBtn').onclick = () => document.getElementById('qrGalleryModal').classList.add('hidden');

/* --- SCANNER --- */
document.getElementById('abrirScannerBtn').onclick = async () => {
    document.getElementById('scannerModal').classList.remove('hidden');
    try {
        const s = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
        document.getElementById('video-preview').srcObject = s; scanning = true; requestAnimationFrame(tick);
    } catch { alert("Câmara não encontrada ou acesso negado."); }
};

document.getElementById('fecharScannerBtn').onclick = () => {
    scanning = false;
    const s = document.getElementById('video-preview').srcObject;
    if(s) s.getTracks().forEach(t => t.stop());
    document.getElementById('scannerModal').classList.add('hidden');
};

function tick() {
    if(!scanning) return;
    const v = document.getElementById('video-preview');
    if(v.readyState === v.HAVE_ENOUGH_DATA) {
        const c = document.getElementById('canvas-hidden');
        c.width = v.videoWidth; c.height = v.videoHeight;
        const ctx = c.getContext("2d");
        ctx.drawImage(v, 0, 0);
        const code = jsQR(ctx.getImageData(0,0,c.width,c.height).data, c.width, c.height);
        if(code && Date.now() - lastScanTime > 3000) {
            const colab = colaboradores.find(x => x.id === code.data);
            if(colab) {
                lastScanTime = Date.now();
                const hoje = new Date().toLocaleDateString('pt-BR');
                const p = pontos.filter(x => x.idColab === colab.id && x.data === hoje).sort((a,b) => new Date(b.horarioISO) - new Date(b.horarioISO));
                const tipo = (p.length > 0 && p[0].tipo === 'Entrada') ? 'Saída' : 'Entrada';
                window.regManual(colab.id, tipo);
                document.getElementById('scanner-feedback').textContent = `REGISTADO: ${tipo} - ${colab.nome}`;
                setTimeout(() => { if(scanning) document.getElementById('scanner-feedback').textContent = "Aponte o QR Code..."; }, 2500);
            }
        }
    }
    requestAnimationFrame(tick);
}

/* --- EXPORT EXCEL COM ABAS SEPARADAS --- */
document.getElementById('baixarBtn').onclick = () => {
    // Filtrar dados
    const entradas = pontos.filter(p => p.tipo === 'Entrada').map(p => {
        const c = colaboradores.find(x => x.id === p.idColab);
        return {
            'ID': p.idColab, 'Colaborador': p.nome, 'Email': c ? c.email : '-',
            'Cargo': c ? c.cargo : '-', 'Turno': c ? c.turno : '-',
            'Data': p.data, 'Hora': p.hora
        };
    });

    const saidas = pontos.filter(p => p.tipo === 'Saída').map(p => {
        const c = colaboradores.find(x => x.id === p.idColab);
        return {
            'ID': p.idColab, 'Colaborador': p.nome, 'Email': c ? c.email : '-',
            'Cargo': c ? c.cargo : '-', 'Turno': c ? c.turno : '-',
            'Data': p.data, 'Hora': p.hora
        };
    });

    // Criar abas
    const wsEntradas = XLSX.utils.json_to_sheet(entradas);
    const wsSaidas = XLSX.utils.json_to_sheet(saidas);
    
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, wsEntradas, "Entradas");
    XLSX.utils.book_append_sheet(wb, wsSaidas, "Saídas");
    
    XLSX.writeFile(wb, `Ponto_CLX_Separado_${new Date().toLocaleDateString().replace(/\//g,'-')}.xlsx`);
};

document.getElementById('limparPontosBtn').onclick = async () => {
    if(confirm("ATENÇÃO: Deseja apagar TODOS os registos de ponto do sistema? Esta ação não pode ser desfeita.")) {
        const s = await getDocs(collection(db, "pontos"));
        s.forEach(d => deleteDoc(doc(db, "pontos", d.id)));
    }
};

setInterval(() => { document.getElementById('clock').textContent = new Date().toLocaleTimeString('pt-BR'); }, 1000);

window.addEventListener('load', () => {
    if(localStorage.getItem('ponto_remember') === 'true') {
        const u = localStorage.getItem('ponto_user'), p = localStorage.getItem('ponto_pass');
        document.getElementById('user').value = u; document.getElementById('pass').value = p;
        document.getElementById('rememberMe').checked = true;
        setTimeout(() => login(u, p), 500);
    }
});
</script>
</body>
</html>
