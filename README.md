<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Sistema Informa - Completo</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
<style>
:root { --primary: #2563eb; --danger: #dc2626; --success: #10b981; --warning: #eab308; }
body{ font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #f0f2f5; padding:20px; min-height:100vh; display:flex; flex-direction:column; margin:0; }
.container{ max-width:1100px; margin:20px auto; background:#fff; padding:25px; border-radius:12px; flex:1; box-shadow:0 10px 25px rgba(0,0,0,0.05); }
input, select, textarea, button { width:100%; padding:12px; margin-bottom:12px; border:1px solid #ddd; border-radius:6px; box-sizing: border-box; font-size: 14px; }
button { background: var(--primary); color:#fff; border:none; cursor:pointer; font-weight:bold; transition: 0.2s; }
button:hover { opacity: 0.9; transform: translateY(-1px); }
button.danger { background: var(--danger); }
button.secondary { background: #64748b; }
.card { border:1px solid #eee; padding:15px; border-radius:8px; margin:10px 0; background:#fff; position: relative; }
.elogio { background:#f0fdf4; border-left:5px solid var(--success); padding:10px; margin:8px 0; }
.reclamacao { background:#fef2f2; border-left:5px solid var(--danger); padding:10px; margin:8px 0; }
.melhorar { background:#fffbeb; border-left:5px solid var(--warning); padding:10px; margin:8px 0; }
#login { max-width:400px; margin:100px auto; background:#fff; padding:40px; border-radius:12px; text-align:center; box-shadow:0 15px 35px rgba(0,0,0,0.1); }
#adminGear { position:fixed; top:20px; right:20px; font-size:28px; cursor:pointer; display:none; z-index:100; background: #fff; border-radius: 50%; padding: 5px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
footer { text-align:center; padding:20px; color:#666; font-size:13px; }
.grid-2 { display:grid; grid-template-columns: 1fr 1fr; gap:12px; }
.grid-3 { display:grid; grid-template-columns: 1fr 1fr 1fr; gap:12px; }
hr { border: 0; border-top: 1px solid #eee; margin: 25px 0; }
#listaLogs { max-height: 250px; overflow-y: auto; font-size: 12px; background: #fafafa; border: 1px solid #eee; padding: 10px; }
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

<div id="adminGear" title="Painel do Administrador">⚙️</div>

<div class="container" id="sistema" style="display:none">
    <div style="display:flex; justify-content:space-between; align-items:center">
        <h1>Dashboard</h1>
        <button id="btnLogout" class="secondary" style="width:auto">Sair</button>
    </div>
    
    <button id="btnExcel" style="width:auto; background:var(--success)">📊 Exportar Relatório Excel</button>

    <div id="secaoCadastro" style="display:none">
        <hr>
        <h3>📝 Cadastrar Colaborador</h3>
        <div class="grid-2">
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
        </div>
        <div class="grid-3">
            <input id="anoEntrada" placeholder="Ano Entrada">
            <input id="matricula" placeholder="Matrícula">
            <input id="email" placeholder="E-mail">
        </div>
        <button id="btnSalvarPessoa">Salvar Colaborador</button>
    </div>

    <hr>
    <h3>✍️ Registrar Nova Nota</h3>
    <div class="grid-2">
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
    <h3>🔍 Pesquisar Perfis</h3>
    <div class="grid-2">
        <input id="buscaNome" placeholder="Nome do colaborador">
        <select id="buscaCategoria">
            <option value="">Todas as Categorias</option>
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
    </div>
    <button id="btnBuscar">Consultar</button>
    <div id="resultado"></div>

    <div id="secaoNotas" style="display:none; margin-top:20px">
        <h3 id="tituloNotas">Histórico</h3>
        <div id="listaNotas"></div>
    </div>

    <hr>
    <div style="max-width:350px; margin:auto">
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
            <option value="user">Usuário Comum (Restrito à Categoria)</option>
            <option value="admin">Administrador (Acesso Total)</option>
        </select>
        <select id="categoriaUsuario">
            <option value="">-- Categoria do Usuário (Se for comum) --</option>
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
        <button id="btnAddUsuario">Adicionar Usuário</button>
    </div>

    <h4>👥 Usuários Ativos</h4>
    <div id="listaUsuarios"></div>

    <hr>
    <h4>🗑️ Lixeira de Itens Excluídos</h4>
    <button class="danger" id="btnLimparLixeira" style="width:auto">Esvaziar Lixeira</button>
    <div id="listaLixeira"></div>

    <hr>
    <h4>📜 Logs de Atividades</h4>
    <div id="listaLogs"></div>
</div>

<footer>© 2025 – Sistema Informa – Criado por <b>CLX</b></footer>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, getDocs, updateDoc, deleteDoc, doc, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

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

let usuarios = [], usuarioLogado = null, pessoas = [], pessoaEditando = null, chart = null;
let el = {};

window.addEventListener('DOMContentLoaded', () => {
    // Mapeamento de elementos
    const IDs = ['login','sistema','adminGear','painelAdmin','erro','loginUsuario','loginSenha','btnLogin','btnLogout','btnSalvarPessoa','btnSalvarNota','btnBuscar','btnAddUsuario','listaUsuarios','nome','categoria','anoEntrada','matricula','email','pessoaNota','tipoNota','nota','buscaNome','buscaCategoria','resultado','grafico','listaNotas','secaoNotas','novoUsuario','senhaUsuario','nivelUsuario','categoriaUsuario','listaLixeira','listaLogs','btnLimparLixeira'];
    IDs.forEach(id => el[id] = document.getElementById(id));

    el.btnLogin.onclick = login;
    el.btnLogout.onclick = () => location.reload();
    el.btnSalvarPessoa.onclick = salvarPessoa;
    el.btnSalvarNota.onclick = salvarNota;
    el.btnBuscar.onclick = buscar;
    el.btnAddUsuario.onclick = addUsuario;
    el.btnLimparLixeira.onclick = limparLixeira;
    el.adminGear.onclick = () => el.painelAdmin.style.display = el.painelAdmin.style.display==='none' ? 'block' : 'none';
    document.getElementById('btnExcel').onclick = exportarExcel;

    carregarUsuarios();
});

async function login(){
    const u = usuarios.find(u => u.usuario === el.loginUsuario.value && u.senha === el.loginSenha.value);
    if(!u) return el.erro.innerText = "Usuário/Senha incorretos";
    if(!u.ativo) return el.erro.innerText = "Usuário desativado";

    usuarioLogado = u;
    el.login.style.display = 'none';
    el.sistema.style.display = 'block';

    if(u.nivel === 'admin'){
        el.adminGear.style.display = 'block';
        document.getElementById('secaoCadastro').style.display = 'block';
        el.buscaCategoria.disabled = false;
        carregarLixeira();
        carregarLogs();
    } else {
        el.buscaCategoria.value = u.categoria;
        el.buscaCategoria.disabled = true;
    }
    carregarPessoas();
}

async function carregarUsuarios(){
    const s = await getDocs(collection(db, 'usuarios'));
    usuarios = [];
    s.forEach(d => usuarios.push({id: d.id, ...d.data()}));
    renderUsuarios();
}

async function carregarPessoas(){
    const s = await getDocs(collection(db, 'pessoas'));
    pessoas = [];
    s.forEach(d => pessoas.push({id: d.id, ...d.data()}));
    
    el.pessoaNota.innerHTML = '<option value="">Selecione...</option>';
    pessoas.forEach((p, i) => {
        if(usuarioLogado.nivel === 'admin' || p.categoria === usuarioLogado.categoria){
            el.pessoaNota.add(new Option(p.nome, i));
        }
    });
    atualizarGrafico();
}

async function salvarPessoa(){
    const dados = {
        nome: el.nome.value, categoria: el.categoria.value, anoEntrada: el.anoEntrada.value,
        matricula: el.matricula.value, email: el.email.value, 
        notas: pessoaEditando ? pessoaEditando.notas : []
    };
    if(pessoaEditando) await updateDoc(doc(db,'pessoas',pessoaEditando.id), dados);
    else await addDoc(collection(db,'pessoas'), dados);
    
    alert("Colaborador salvo!");
    pessoaEditando = null;
    carregarPessoas();
}

async function salvarNota(){
    const p = pessoas[el.pessoaNota.value];
    if(!p || !el.nota.value) return alert("Selecione alguém e escreva a nota");

    const novaNota = { tipo: el.tipoNota.value, texto: el.nota.value, autor: usuarioLogado.usuario, data: new Date().toLocaleDateString('pt-BR') };
    p.notas = p.notas || [];
    p.notas.push(novaNota);
    
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas });
    el.nota.value = "";
    alert("Nota salva!");
    atualizarGrafico();
}

function buscar(){
    el.resultado.innerHTML = "";
    el.secaoNotas.style.display = 'none';
    const filtrados = pessoas.filter(p => {
        const matchNome = p.nome.toLowerCase().includes(el.buscaNome.value.toLowerCase());
        const matchCat = el.buscaCategoria.value === "" || p.categoria === el.buscaCategoria.value;
        return (usuarioLogado.nivel === 'admin' ? (matchNome && matchCat) : (p.categoria === usuarioLogado.categoria && matchNome));
    });

    filtrados.forEach(p => {
        const idx = pessoas.indexOf(p);
        el.resultado.innerHTML += `
            <div class="card">
                <b>${p.nome}</b> (${p.categoria})
                <div style="margin-top:8px">
                    <button onclick="verNotas(${idx})" style="width:auto">Ver Notas</button>
                    ${usuarioLogado.nivel==='admin' ? `<button onclick="editarPessoa(${idx})" class="secondary" style="width:auto">Editar</button>` : ''}
                    ${usuarioLogado.nivel==='admin' ? `<button onclick="excluirPessoa('${p.id}')" class="danger" style="width:auto">Excluir</button>` : ''}
                </div>
            </div>`;
    });
}

window.verNotas = function(idx){
    const p = pessoas[idx];
    el.secaoNotas.style.display = 'block';
    document.getElementById('tituloNotas').innerText = "Histórico: " + p.nome;
    el.listaNotas.innerHTML = p.notas?.length ? "" : "Sem registros.";
    p.notas?.forEach((n, ni) => {
        el.listaNotas.innerHTML += `
            <div class="${n.tipo}">
                <strong>${n.tipo.toUpperCase()}</strong> - ${n.texto}<br>
                <small>Por ${n.autor} em ${n.data}</small>
                ${usuarioLogado.nivel==='admin' ? `<button onclick="removerNota(${idx}, ${ni})" class="danger" style="width:auto; padding:2px 5px; font-size:10px; float:right">X</button>` : ''}
            </div>`;
    });
}

window.removerNota = async function(pIdx, nIdx){
    if(!confirm("Remover esta nota?")) return;
    const notaRemovida = pessoas[pIdx].notas.splice(nIdx, 1)[0];
    await addDoc(collection(db, 'lixeira'), { tipo: 'nota', dados: notaRemovida, pessoa: pessoas[pIdx].nome, excluidoPor: usuarioLogado.usuario, data: new Date().toLocaleString() });
    await updateDoc(doc(db, 'pessoas', pessoas[pIdx].id), { notas: pessoas[pIdx].notas });
    verNotas(pIdx);
}

window.excluirPessoa = async function(id){
    if(!confirm("Excluir colaborador?")) return;
    const p = pessoas.find(x => x.id === id);
    await addDoc(collection(db, 'lixeira'), { tipo: 'pessoa', dados: p, excluidoPor: usuarioLogado.usuario, data: new Date().toLocaleString() });
    await addDoc(collection(db, 'logs'), { acao: 'Excluiu Colaborador', alvo: p.nome, usuario: usuarioLogado.usuario, data: new Date().toLocaleString() });
    await deleteDoc(doc(db, 'pessoas', id));
    carregarPessoas();
}

window.editarPessoa = function(idx){
    const p = pessoas[idx];
    pessoaEditando = p;
    el.nome.value = p.nome; el.categoria.value = p.categoria;
    el.anoEntrada.value = p.anoEntrada; el.matricula.value = p.matricula;
    window.scrollTo(0,0);
}

async function addUsuario(){
    await addDoc(collection(db, 'usuarios'), { usuario: el.novoUsuario.value, senha: el.senhaUsuario.value, nivel: el.nivelUsuario.value, categoria: el.categoriaUsuario.value, ativo: true });
    alert("Usuário criado!");
    carregarUsuarios();
}

function renderUsuarios(){
    el.listaUsuarios.innerHTML = "";
    usuarios.forEach(u => {
        el.listaUsuarios.innerHTML += `<div class="card">${u.usuario} (${u.nivel}) - ${u.categoria || 'Global'} <button onclick="toggleUser('${u.id}', ${u.ativo})" style="width:auto; float:right">${u.ativo?'Bloquear':'Ativar'}</button></div>`;
    });
}

window.toggleUser = async function(id, status){
    await updateDoc(doc(db, 'usuarios', id), { ativo: !status });
    carregarUsuarios();
}

async function carregarLixeira(){
    const s = await getDocs(collection(db, 'lixeira'));
    el.listaLixeira.innerHTML = "";
    s.forEach(d => {
        const x = d.data();
        el.listaLixeira.innerHTML += `<div class="card" style="font-size:11px"><b>${x.tipo.toUpperCase()}</b> de ${x.pessoa || x.dados?.nome}<br>Excluído por: ${x.excluidoPor}</div>`;
    });
}

async function carregarLogs(){
    const s = await getDocs(collection(db, 'logs'));
    el.listaLogs.innerHTML = "";
    s.forEach(d => {
        const x = d.data();
        el.listaLogs.innerHTML += `<div>${x.data} - ${x.usuario}: ${x.acao} (${x.alvo})</div>`;
    });
}

async function limparLixeira(){
    if(!confirm("Esvaziar permanentemente?")) return;
    const s = await getDocs(collection(db, 'lixeira'));
    s.forEach(async d => await deleteDoc(doc(db, 'lixeira', d.id)));
    carregarLixeira();
}

function atualizarGrafico(){
    let e=0, r=0, m=0;
    pessoas.forEach(p => {
        if(usuarioLogado.nivel === 'admin' || p.categoria === usuarioLogado.categoria){
            p.notas?.forEach(n => { if(n.tipo==='elogio') e++; else if(n.tipo==='reclamacao') r++; else m++; });
        }
    });
    if(chart) chart.destroy();
    chart = new Chart(el.grafico, { type: 'pie', data: { labels: ['Elogios', 'Reclamações', 'Melhorar'], datasets: [{ data: [e,r,m], backgroundColor: ['#10b981','#ef4444','#f59e0b'] }] } });
}

function exportarExcel(){
    let exportData = [];
    pessoas.forEach(p => {
        if(usuarioLogado.nivel === 'admin' || p.categoria === usuarioLogado.categoria){
            p.notas?.forEach(n => exportData.push({ Nome: p.nome, Categoria: p.categoria, Tipo: n.tipo, Nota: n.texto, Data: n.data, Autor: n.autor }));
        }
    });
    const ws = XLSX.utils.json_to_sheet(exportData);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Relatório");
    XLSX.writeFile(wb, "Relatorio_Feedback.xlsx");
}
</script>
</body>
</html>
