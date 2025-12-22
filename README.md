<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Sistema Informa - Enterprise v5.1 (Restaurado)</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
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
.elogio { background:#f0fdf4; border-left:5px solid var(--success); padding:10px; margin:8px 0; position: relative;}
.reclamacao { background:#fef2f2; border-left:5px solid var(--danger); padding:10px; margin:8px 0; position: relative;}
.melhorar { background:#fffbeb; border-left:5px solid var(--warning); padding:10px; margin:8px 0; position: relative;}
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
        <h1>Painel - Informa</h1>
        <button id="btnLogout" class="secondary" style="width:auto">Sair</button>
    </div>
    
    <button id="btnExcel" style="width:auto; background:var(--success)">📊 Exportar Relatório Excel</button>

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
            <input id="email" placeholder="E-mail">
            <input id="telefone" placeholder="Telefone Principal" maxlength="15">
            <input id="contato" placeholder="Número de Contato" maxlength="15">
            <input id="cpf" placeholder="CPF" maxlength="14">
            <input id="rg" placeholder="RG" maxlength="12">
            <input id="dataNascimento" type="date">
            <input id="anoEntrada" placeholder="Ano de Entrada">
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

    <h4>👥 Usuários do Sistema</h4>
    <div id="listaUsuarios"></div>

    <hr>
    <h4>🗑️ Lixeira de Itens Excluídos</h4>
    <button class="danger btn-mini" id="btnLimparLixeira">Esvaziar Lixeira</button>
    <div id="listaLixeira" style="font-size: 11px; margin-top:10px;"></div>
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

let usuarios = [], usuarioLogado = null, pessoas = [], pessoaEditando = null, chart = null;
let el = {};

window.addEventListener('DOMContentLoaded', async () => {
    const IDs = ['login','sistema','adminGear','painelAdmin','erro','loginUsuario','loginSenha','btnLogin','btnLogout','btnSalvarPessoa','btnSalvarNota','btnBuscar','btnAddUsuario','listaUsuarios','nome','categoria','anoEntrada','matricula','email','telefone','contato','cpf','rg','dataNascimento','pessoaNota','tipoNota','nota','buscaNome','buscaCategoria','resultado','grafico','listaNotas','secaoNotas','novoUsuario','senhaUsuario','nivelUsuario','categoriaUsuario','listaLixeira','btnLimparLixeira','btnExcel','gavetaExcluidas','listaExcluidas','secaoCadastro'];
    IDs.forEach(id => el[id] = document.getElementById(id));

    el.btnLogin.onclick = login;
    el.btnLogout.onclick = logout;
    el.btnSalvarPessoa.onclick = salvarPessoa;
    el.btnSalvarNota.onclick = salvarNota;
    el.btnBuscar.onclick = buscar;
    el.btnAddUsuario.onclick = addUsuario;
    el.btnLimparLixeira.onclick = limparLixeira;
    if(el.btnExcel) el.btnExcel.onclick = exportarExcel;
    el.adminGear.onclick = () => el.painelAdmin.style.display = el.painelAdmin.style.display==='none' ? 'block' : 'none';

    // Máscaras
    el.cpf.oninput = (e) => e.target.value = e.target.value.replace(/\D/g,"").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2");
    el.rg.oninput = (e) => e.target.value = e.target.value.replace(/\D/g,"").replace(/(\d{2})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2");
    const maskTel = (e) => { let v = e.target.value.replace(/\D/g,""); v = v.replace(/^(\d{2})(\d)/g,"($1) $2"); v = v.replace(/(\d)(\d{4})$/,"$1-$2"); e.target.value = v; };
    el.telefone.oninput = maskTel; el.contato.oninput = maskTel;

    await carregarUsuarios();
    const sessao = localStorage.getItem('sessao_informa');
    if(sessao) {
        const uS = JSON.parse(sessao);
        const uV = usuarios.find(x => x.usuario === uS.usuario && x.senha === uS.senha);
        if(uV && uV.ativo) entrarNoSistema(uV);
    }
});

async function login(){
    const s = await getDocs(collection(db, 'usuarios'));
    usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
    const u = usuarios.find(u => u.usuario === el.loginUsuario.value && u.senha === el.loginSenha.value);
    if(!u) return el.erro.innerText = "Falha no login.";
    if(!u.ativo) return el.erro.innerText = "Acesso bloqueado.";
    localStorage.setItem('sessao_informa', JSON.stringify(u));
    entrarNoSistema(u);
}

function entrarNoSistema(u) {
    usuarioLogado = u;
    if(el.login) el.login.style.display = 'none';
    if(el.sistema) el.sistema.style.display = 'block';
    if(u.nivel === 'admin'){
        if(el.adminGear) el.adminGear.style.display = 'block';
        if(el.secaoCadastro) el.secaoCadastro.style.display = 'block';
        carregarLixeira();
    } else {
        if(el.buscaCategoria) { el.buscaCategoria.value = u.categoria; el.buscaCategoria.disabled = true; }
    }
    carregarPessoas();
}

function logout() { localStorage.removeItem('sessao_informa'); location.reload(); }

async function carregarUsuarios(){
    const s = await getDocs(collection(db, 'usuarios'));
    usuarios = s.docs.map(d => ({id: d.id, ...d.data()}));
    el.listaUsuarios.innerHTML = "";
    usuarios.forEach(u => {
        el.listaUsuarios.innerHTML += `<div class="card ${u.ativo ? '' : 'bloqueado'}">
            <b>${u.usuario}</b> (${u.nivel})
            <button class="btn-mini success" onclick="window.resetarSenha('${u.id}', '${u.usuario}')">Reset Senha</button>
            <button class="btn-mini secondary" onclick="window.toggleUser('${u.id}', ${u.ativo})">${u.ativo ? 'Bloquear' : 'Ativar'}</button>
            ${u.usuario !== 'CLX' ? `<button class="danger btn-mini" onclick="window.excluirUsuario('${u.id}')">Excluir</button>` : ''}
        </div>`;
    });
}

window.resetarSenha = async (id, nome) => {
    const nS = prompt(`Nova senha para ${nome}:`);
    if(nS) { await updateDoc(doc(db, 'usuarios', id), { senha: nS }); carregarUsuarios(); }
};
window.toggleUser = async (id, stat) => { await updateDoc(doc(db, 'usuarios', id), { ativo: !stat }); carregarUsuarios(); };
window.excluirUsuario = async (id) => { if(confirm("Excluir acesso?")) { await deleteDoc(doc(db, 'usuarios', id)); carregarUsuarios(); } };

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
    const d = { nome: el.nome.value, categoria: el.categoria.value, matricula: el.matricula.value, email: el.email.value, telefone: el.telefone.value, contato: el.contato.value, cpf: el.cpf.value, rg: el.rg.value, dataNascimento: el.dataNascimento.value, anoEntrada: el.anoEntrada.value, notas: pessoaEditando ? (pessoaEditando.notas || []) : [], notasExcluidas: pessoaEditando ? (pessoaEditando.notasExcluidas || []) : [] };
    if(pessoaEditando) await updateDoc(doc(db, 'pessoas', pessoaEditando.id), d);
    else await addDoc(collection(db, 'pessoas'), d);
    alert("Salvo!"); ['nome','matricula','email','telefone','contato','cpf','rg','dataNascimento','anoEntrada'].forEach(f => el[f].value = "");
    pessoaEditando = null; carregarPessoas();
}

async function salvarNota(){
    const p = pessoas[el.pessoaNota.value];
    const n = { tipo: el.tipoNota.value, texto: el.nota.value, autor: usuarioLogado.usuario, data: new Date().toLocaleDateString('pt-BR') };
    p.notas = p.notas || []; p.notas.push(n);
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas });
    el.nota.value = ""; alert("Nota salva!"); atualizarGrafico();
}

window.apagarNota = async (pIdx, nIdx) => {
    if(!confirm("Mover para gaveta?")) return;
    const p = pessoas[pIdx];
    const nRem = p.notas.splice(nIdx, 1)[0];
    p.notasExcluidas = p.notasExcluidas || []; p.notasExcluidas.push(nRem);
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas, notasExcluidas: p.notasExcluidas });
    window.verNotas(pIdx); atualizarGrafico();
};

window.restaurarNota = async (pIdx, nIdx) => {
    const p = pessoas[pIdx];
    const nRes = p.notasExcluidas.splice(nIdx, 1)[0];
    p.notas.push(nRes);
    await updateDoc(doc(db, 'pessoas', p.id), { notas: p.notas, notasExcluidas: p.notasExcluidas });
    window.verNotas(pIdx); atualizarGrafico();
};

window.verNotas = function(idx){
    const p = pessoas[idx]; el.secaoNotas.style.display = 'block'; el.listaNotas.innerHTML = "";
    p.notas?.forEach((n, ni) => {
        const btn = usuarioLogado.nivel === 'admin' ? `<span class="btn-del-nota" onclick="window.apagarNota(${idx}, ${ni})">🗑️</span>` : '';
        el.listaNotas.innerHTML += `<div class="${n.tipo}">${btn}<strong>${n.tipo.toUpperCase()}</strong>: ${n.texto}<br><small>${n.data} por ${n.autor}</small></div>`;
    });
    if(usuarioLogado.nivel === 'admin' && p.notasExcluidas?.length > 0) {
        el.gavetaExcluidas.style.display = 'block'; el.listaExcluidas.innerHTML = "";
        p.notasExcluidas.forEach((n, ni) => { el.listaExcluidas.innerHTML += `<div class="excluida"><span class="btn-restore-nota" onclick="window.restaurarNota(${idx}, ${ni})">🔄</span>${n.texto}</div>`; });
    } else { el.gavetaExcluidas.style.display = 'none'; }
}

function buscar(){
    el.resultado.innerHTML = ""; el.secaoNotas.style.display = 'none';
    const filt = pessoas.filter(p => {
        const n = p.nome.toLowerCase().includes(el.buscaNome.value.toLowerCase());
        const c = el.buscaCategoria.value === "" || p.categoria === el.buscaCategoria.value;
        return (usuarioLogado.nivel === 'admin' ? (n && c) : (p.categoria === usuarioLogado.categoria && n));
    });
    filt.forEach(p => {
        const idx = pessoas.indexOf(p);
        el.resultado.innerHTML += `<div class="card"><b>${p.nome}</b> (${p.categoria})<br>
            <button class="btn-mini" onclick="verNotas(${idx})">Notas</button>
            ${usuarioLogado.nivel==='admin' ? `<button class="btn-mini secondary" onclick="editarPessoa(${idx})">Editar</button> <button class="btn-mini danger" onclick="excluirPessoa('${p.id}')">Excluir</button>` : ''}
        </div>`;
    });
}

window.excluirPessoa = async (id) => {
    if(confirm("Mover para lixeira?")){
        const p = pessoas.find(x => x.id === id);
        await addDoc(collection(db, 'lixeira'), { dados: p, data: new Date().toLocaleString() });
        await deleteDoc(doc(db, 'pessoas', id)); carregarPessoas(); buscar();
    }
};

window.editarPessoa = (idx) => {
    const p = pessoas[idx]; pessoaEditando = p;
    ['nome','categoria','matricula','email','telefone','contato','cpf','rg','dataNascimento','anoEntrada'].forEach(f => el[f].value = p[f] || "");
    window.scrollTo(0,0);
};

async function carregarLixeira(){
    const s = await getDocs(collection(db, 'lixeira'));
    el.listaLixeira.innerHTML = "<b>Lixeira:</b>";
    s.forEach(d => el.listaLixeira.innerHTML += `<div>- ${d.data().dados?.nome}</div>`);
}

async function limparLixeira(){
    if(!confirm("Limpar lixeira?")) return;
    const s = await getDocs(collection(db, 'lixeira'));
    s.forEach(async d => await deleteDoc(doc(db, 'lixeira', d.id))); carregarLixeira();
}

async function addUsuario(){
    await addDoc(collection(db, 'usuarios'), { usuario: el.novoUsuario.value, senha: el.senhaUsuario.value, nivel: el.nivelUsuario.value, categoria: el.categoriaUsuario.value, ativo: true });
    el.novoUsuario.value = ""; el.senhaUsuario.value = ""; carregarUsuarios();
}

function atualizarGrafico(){
    let e=0, r=0, m=0;
    pessoas.forEach(p => {
        if(usuarioLogado.nivel === 'admin' || p.categoria === usuarioLogado.categoria)
            p.notas?.forEach(n => { if(n.tipo==='elogio') e++; else if(n.tipo==='reclamacao') r++; else m++; });
    });
    if(chart) chart.destroy();
    chart = new Chart(el.grafico, { type: 'pie', data: { labels: ['Elogios', 'Reclamações', 'Melhorar'], datasets: [{ data: [e,r,m], backgroundColor: ['#10b981','#ef4444','#f59e0b'] }] } });
}

function exportarExcel(){
    const data = pessoas.map(p => ({ 
        Nome: p.nome, Categoria: p.categoria, CPF: p.cpf, RG: p.rg, 
        Elogios: p.notas?.filter(n=>n.tipo==='elogio').length || 0, 
        Reclamacoes: p.notas?.filter(n=>n.tipo==='reclamacao').length || 0 
    }));
    const ws = XLSX.utils.json_to_sheet(data);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Relatório");
    XLSX.writeFile(wb, "Relatorio_Informa_v5.xlsx");
}
</script>
</body>
</html>
