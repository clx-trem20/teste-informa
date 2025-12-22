<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Sistema Informa - Enterprise v4.1</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
:root { --primary: #2563eb; --danger: #dc2626; --success: #10b981; --warning: #eab308; }
body{ font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #f0f2f5; padding:20px; min-height:100vh; display:flex; flex-direction:column; margin:0; }
.container{ max-width:1100px; margin:20px auto; background:#fff; padding:25px; border-radius:12px; flex:1; box-shadow:0 10px 25px rgba(0,0,0,0.05); }
input, select, textarea, button { width:100%; padding:10px; margin-bottom:10px; border:1px solid #ddd; border-radius:6px; box-sizing: border-box; font-size: 14px; }
button { background: var(--primary); color:#fff; border:none; cursor:pointer; font-weight:bold; transition: 0.2s; }
button:hover { opacity: 0.9; transform: translateY(-1px); }
button.danger { background: var(--danger); }
button.secondary { background: #64748b; }
button.success { background: var(--success); }
.card { border:1px solid #eee; padding:15px; border-radius:8px; margin:10px 0; background:#fff; position: relative; }
.bloqueado { background: #fee2e2 !important; border: 1px solid #ef4444; }
.elogio { border-left:5px solid var(--success); background:#f0fdf4; padding:10px; margin:8px 0; position: relative; }
.reclamacao { border-left:5px solid var(--danger); background:#fef2f2; padding:10px; margin:8px 0; position: relative; }
.melhorar { border-left:5px solid var(--warning); background:#fffbeb; padding:10px; margin:8px 0; position: relative; }
.excluida { border-left:5px solid #64748b; background:#f1f5f9; opacity: 0.7; padding:10px; margin:8px 0; position: relative; font-style: italic; }
.btn-del-nota { position: absolute; top: 5px; right: 10px; color: var(--danger); cursor: pointer; font-weight: bold; font-size: 18px; }
.btn-restore-nota { position: absolute; top: 5px; right: 35px; color: var(--success); cursor: pointer; font-size: 18px; }
#login { max-width:400px; margin:100px auto; background:#fff; padding:40px; border-radius:12px; text-align:center; box-shadow:0 15px 35px rgba(0,0,0,0.1); }
#adminGear { position:fixed; top:20px; right:20px; font-size:28px; cursor:pointer; display:none; z-index:100; background: #fff; border-radius: 50%; padding: 5px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
footer { text-align:center; padding:20px; color:#666; font-size:13px; }
.grid-form { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 10px; }
hr { border: 0; border-top: 1px solid #eee; margin: 25px 0; }
.btn-mini { width: auto; padding: 5px 10px; font-size: 11px; margin: 2px; }
</style>
</head>
<body>

<div id="login">
    <h2>🔐 Sistema Informa</h2>
    <input id="loginUsuario" placeholder="Usuário">
    <input id="loginSenha" type="password" placeholder="Senha">
    <button id="btnLogin">Entrar</button>
    <p id="erro" style="color:var(--danger); font-size:13px"></p>
</div>

<div id="adminGear">⚙️</div>

<div class="container" id="sistema" style="display:none">
    <div style="display:flex; justify-content:space-between; align-items:center">
        <h1>Dashboard</h1>
        <button id="btnLogout" class="secondary" style="width:auto">Sair</button>
    </div>
    
    <div id="secaoCadastro" style="display:none">
        <hr>
        <h3>📝 Cadastro de Colaboradores</h3>
        <div class="grid-form">
            <input id="nome" placeholder="Nome Completo">
            <select id="categoria">
                <option value="">Selecione a Categoria</option>
                <option value="Meio Ambiente">Meio Ambiente</option>
                <option value="Linguagens">Linguagens</option>
                <option value="Comunicações">Comunicações</option>
                <option value="Edição de Vídeo">Edição de Vídeo</option>
                <option value="Cultura">Cultura</option>
                <option value="Secretaria">Secretaria</option>
                <option value="Esportes">Esportes</option>
                <option value="Presidência">Presidência</option>
                <option value="Informações">Informações</option>
                <option value="Designer">Designer</option>
            </select>
            <input id="matricula" placeholder="Matrícula">
            <input id="cpf" placeholder="CPF">
        </div>
        <button id="btnSalvarPessoa">Salvar Colaborador</button>
    </div>

    <hr>
    <h3>✍️ Registrar Nota</h3>
    <div class="grid-form">
        <select id="pessoaNota"></select>
        <select id="tipoNota">
            <option value="elogio">Elogio</option>
            <option value="reclamacao">Reclamação</option>
            <option value="melhorar">A melhorar</option>
        </select>
    </div>
    <textarea id="nota" placeholder="Escreva o feedback aqui..."></textarea>
    <button id="btnSalvarNota">Salvar Nota</button>

    <hr>
    <h3>🔍 Pesquisar</h3>
    <div class="grid-form">
        <input id="buscaNome" placeholder="Nome do colaborador">
    </div>
    <button id="btnBuscar">Consultar</button>
    <div id="resultado"></div>

    <div id="secaoNotas" style="display:none; margin-top:20px; background: #fff; padding: 15px; border: 1px solid #ddd; border-radius: 8px;">
        <h3 id="tituloNotas">Histórico</h3>
        <div id="listaNotas"></div>
        <div id="gavetaExcluidas" style="margin-top:20px; border-top: 1px dashed #ccc; padding-top:10px; display:none;">
            <h4 style="color:#64748b">📁 Gaveta de Notas Excluídas (Admin)</h4>
            <div id="listaExcluidas"></div>
        </div>
    </div>

    <hr>
    <div style="max-width:300px; margin:auto">
        <canvas id="grafico"></canvas>
    </div>
</div>

<div id="painelAdmin" class="container" style="display:none; border-top: 5px solid var(--primary)">
    <h2>⚙️ Gestão Administrativa</h2>
    <div class="card">
        <h4>Criar Novo Usuário</h4>
        <input id="novoUsuario" placeholder="Username">
        <input id="senhaUsuario" type="password" placeholder="Senha">
        <select id="nivelUsuario">
            <option value="user">Usuário Comum</option>
            <option value="admin">Administrador</option>
        </select>
        <select id="categoriaUsuario">
            <option value="">-- Categoria (Se for comum) --</option>
            <option value="Meio Ambiente">Meio Ambiente</option>
            <option value="Designer">Designer</option>
            </select>
        <button id="btnAddUsuario">Adicionar Usuário</button>
    </div>

    <h4>👥 Usuários do Sistema</h4>
    <div id="listaUsuarios"></div>
</div>

<footer>© 2025 – Sistema Informa – Criado por <b>CLX</b></footer>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, getDocs, updateDoc, deleteDoc, doc } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

const firebaseConfig = {
    apiKey: "AIzaSyCtJytArZciWTcAaVI--bY7mSiFVE-K6Zw",
    authDomain: "informa-a8d4d.firebaseapp.com",
    projectId: "informa-a8d4d",
    storageBucket: "informa-a8d4d.firebasestorage.app",
    messagingSenderId: "201808467376",
    appId: "1:201808467376:web:bb06f0fd7e57dfa747b275"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

let usuarios = [], usuarioLogado = null, pessoas = [], chart = null;
let el = {};

window.addEventListener('DOMContentLoaded', async () => {
    const IDs = ['login','sistema','adminGear','painelAdmin','erro','loginUsuario','loginSenha','btnLogin','btnLogout','btnSalvarPessoa','btnSalvarNota','btnBuscar','btnAddUsuario','listaUsuarios','nome','categoria','matricula','cpf','pessoaNota','tipoNota','nota','buscaNome','resultado','grafico','listaNotas','secaoNotas','novoUsuario','senhaUsuario','nivelUsuario','categoriaUsuario','gavetaExcluidas','listaExcluidas'];
    IDs.forEach(id => {
        const element = document.getElementById(id);
        if(element) el[id] = element;
    });

    el.btnLogin.onclick = login;
    el.btnLogout.onclick = logout;
    el.btnSalvarPessoa.onclick = salvarPessoa;
    el.btnSalvarNota.onclick = salvarNota;
    el.btnBuscar.onclick = buscar;
    el.btnAddUsuario.onclick = addUsuario;
    el.adminGear.onclick = () => el.painelAdmin.style.display = el.painelAdmin.style.display==='none' ? 'block' : 'none';

    await carregarUsuarios();
    const sessao = localStorage.getItem('sessao_informa');
    if(sessao) {
        const u = JSON.parse(sessao);
        const valid = usuarios.find(x => x.usuario === u.usuario && x.senha === u.senha);
        if(valid && valid.ativo) entrarNoSistema(valid);
    }
});

async function login(){
    const s = await getDocs(collection(db, 'usuarios'));
    usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
    const u = usuarios.find(u => u.usuario === el.loginUsuario.value && u.senha === el.loginSenha.value);
    if(!u) return el.erro.innerText = "Falha no login.";
    if(!u.ativo) return el.erro.innerText = "Conta bloqueada.";
    localStorage.setItem('sessao_informa', JSON.stringify(u));
    entrarNoSistema(u);
}

function entrarNoSistema(u) {
    usuarioLogado = u;
    el.login.style.display = 'none';
    el.sistema.style.display = 'block';
    if(u.nivel === 'admin') {
        el.adminGear.style.display = 'block';
        el.secaoCadastro.style.display = 'block';
    }
    carregarPessoas();
}

function logout() { localStorage.removeItem('sessao_informa'); location.reload(); }

async function carregarUsuarios(){
    const s = await getDocs(collection(db, 'usuarios'));
    usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
    renderUsuarios();
}

function renderUsuarios(){
    el.listaUsuarios.innerHTML = "";
    usuarios.forEach(u => {
        el.listaUsuarios.innerHTML += `<div class="card ${u.ativo ? '' : 'bloqueado'}">
            <b>${u.usuario}</b> (${u.nivel})
            <button class="btn-mini" onclick="window.toggleUser('${u.id}', ${u.ativo})">Alterar Status</button>
        </div>`;
    });
}

// EXPOSTAS GLOBALMENTE PARA EVITAR ERROS DE DEFINIÇÃO
window.toggleUser = async function(id, status){
    await updateDoc(doc(db, 'usuarios', id), { ativo: !status });
    carregarUsuarios();
}

async function addUsuario(){
    if(!el.novoUsuario.value || !el.senhaUsuario.value) return alert("Preencha tudo");
    await addDoc(collection(db, 'usuarios'), { 
        usuario: el.novoUsuario.value, 
        senha: el.senhaUsuario.value, 
        nivel: el.nivelUsuario.value, 
        categoria: el.categoriaUsuario.value, 
        ativo: true 
    });
    el.novoUsuario.value = ""; el.senhaUsuario.value = "";
    carregarUsuarios();
}

async function carregarPessoas(){
    const s = await getDocs(collection(db, 'pessoas'));
    pessoas = s.docs.map(d => ({id: d.id, ...d.data()}));
    el.pessoaNota.innerHTML = '<option value="">Selecione...</option>';
    pessoas.forEach((p, i) => {
        if(usuarioLogado.nivel === 'admin' || p.categoria === usuarioLogado.categoria)
            el.pessoaNota.add(new Option(p.nome, i));
    });
    atualizarGrafico();
}

async function salvarPessoa(){
    await addDoc(collection(db, 'pessoas'), { 
        nome: el.nome.value, 
        categoria: el.categoria.value, 
        matricula: el.matricula.value, 
        cpf: el.cpf.value, 
        notas: [], 
        notasExcluidas: [] 
    });
    alert("Salvo!");
    carregarPessoas();
}

async function salvarNota(){
    const p = pessoas[el.pessoaNota.value];
    const n = { tipo: el.tipoNota.value, texto: el.nota.value, autor: usuarioLogado.usuario, data: new Date().toLocaleDateString('pt-BR') };
    p.notas = p.notas || []; p.notas.push(n);
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas });
    el.nota.value = ""; atualizarGrafico();
}

window.apagarNota = async function(pIdx, nIdx) {
    const p = pessoas[pIdx];
    const notaRemovida = p.notas.splice(nIdx, 1)[0];
    p.notasExcluidas = p.notasExcluidas || [];
    p.notasExcluidas.push(notaRemovida);
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas, notasExcluidas: p.notasExcluidas });
    verNotas(pIdx);
    atualizarGrafico();
}

window.restaurarNota = async function(pIdx, nIdx) {
    const p = pessoas[pIdx];
    const notaRestaurada = p.notasExcluidas.splice(nIdx, 1)[0];
    p.notas.push(notaRestaurada);
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas, notasExcluidas: p.notasExcluidas });
    verNotas(pIdx);
    atualizarGrafico();
}

window.verNotas = function(idx){
    const p = pessoas[idx];
    el.secaoNotas.style.display = 'block';
    el.listaNotas.innerHTML = "";
    p.notas?.forEach((n, ni) => {
        const btn = usuarioLogado.nivel === 'admin' ? `<span class="btn-del-nota" onclick="window.apagarNota(${idx}, ${ni})">🗑️</span>` : '';
        el.listaNotas.innerHTML += `<div class="${n.tipo}">${btn}<strong>${n.tipo}</strong>: ${n.texto}</div>`;
    });
    if(usuarioLogado.nivel === 'admin' && p.notasExcluidas?.length > 0) {
        el.gavetaExcluidas.style.display = 'block';
        el.listaExcluidas.innerHTML = "";
        p.notasExcluidas.forEach((n, ni) => {
            el.listaExcluidas.innerHTML += `<div class="excluida"><span class="btn-restore-nota" onclick="window.restaurarNota(${idx}, ${ni})">🔄</span>${n.texto}</div>`;
        });
    } else { el.gavetaExcluidas.style.display = 'none'; }
}

function buscar(){
    el.resultado.innerHTML = "";
    pessoas.filter(p => (usuarioLogado.nivel==='admin'||p.categoria===usuarioLogado.categoria) && p.nome.toLowerCase().includes(el.buscaNome.value.toLowerCase()))
    .forEach(p => {
        const idx = pessoas.indexOf(p);
        el.resultado.innerHTML += `<div class="card">${p.nome} <button class="btn-mini" onclick="window.verNotas(${idx})">Ver</button></div>`;
    });
}

function atualizarGrafico(){
    let e=0, r=0, m=0;
    pessoas.filter(p => usuarioLogado.nivel==='admin'||p.categoria===usuarioLogado.categoria).forEach(p => {
        p.notas?.forEach(n => { if(n.tipo==='elogio') e++; else if(n.tipo==='reclamacao') r++; else m++; });
    });
    if(chart) chart.destroy();
    chart = new Chart(el.grafico, { type: 'pie', data: { labels: ['Elogios', 'Reclamações', 'Melhorar'], datasets: [{ data: [e,r,m], backgroundColor: ['#10b981','#ef4444','#f59e0b'] }] } });
}
</script>
</body>
</html>
