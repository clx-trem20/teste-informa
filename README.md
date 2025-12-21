<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Sistema Informa</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
<style>
body{
  font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: url('img/fundo.jpg') no-repeat center top fixed;
  background-size: contain;
  padding:20px;
  min-height:100vh;
  display:flex;
  flex-direction:column;
}
.container{max-width:1100px;margin:auto;background:rgba(255,255,255,0.92);padding:20px;border-radius:8px;flex:1;box-shadow:0 10px 30px rgba(0,0,0,0.15)}
input,select,textarea,button{width:100%;padding:8px;margin-bottom:8px}
button{background:#2563eb;color:#fff;border:none;border-radius:5px;cursor:pointer}
button.danger{background:#dc2626}
.card{border:1px solid #ddd;padding:10px;border-radius:6px;margin:6px 0}
.elogio{background:#dcfce7;padding:6px;margin:4px 0}
.reclamacao{background:#fee2e2;padding:6px;margin:4px 0}
.melhorar{background:#fef9c3;padding:6px;margin:4px 0}
#login{max-width:400px;margin:100px auto;background:#fff;padding:20px;border-radius:8px;text-align:center}
#adminGear{position:fixed;top:20px;right:20px;font-size:24px;cursor:pointer;display:none}
footer{
  text-align:center;
  margin-top:30px;
  color:#ffffff;
  font-size:14px;
  padding:12px 0;
  background:rgba(0,0,0,0.55);
}
</style>
</head>
<body>

<div id="login">
<h2>🔐 Login</h2>
<input id="loginUsuario" placeholder="Usuário">
<input id="loginSenha" type="password" placeholder="Senha">
<button id="btnLogin">Entrar</button>
<p id="erro" style="color:red"></p>
</div>

<div id="adminGear">⚙️</div>

<div class="container" id="sistema" style="display:none">
<button id="btnLogout" style="float:right;background:#6b7280">Sair</button>
<h1>Sistema Informa</h1>
<button id="btnExcel">📊 Exportar para Excel</button>

<h2>Cadastrar / Editar Pessoa</h2>
<input id="nome" placeholder="Nome completo">
<select id="categoria">
  <option value="">Selecione Categoria</option>
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
<input id="anoEntrada" placeholder="Ano que entrou">
<input id="matricula" placeholder="Matrícula">
<input id="email" placeholder="E-mail">
<input id="telefone" placeholder="Telefone">
<input id="cpf" placeholder="CPF">
<input id="rg" placeholder="RG">
<input id="dataNascimento" type="date">
<input id="contato" placeholder="Número de contato">
<button id="btnSalvarPessoa">Salvar</button>
<button class="danger" id="btnExcluirPessoa">Excluir Perfil</button>

<h2>Adicionar Nota</h2>
<select id="pessoaNota"></select>
<select id="tipoNota">
<option value="elogio">Elogio</option>
<option value="reclamacao">Reclamação</option>
<option value="melhorar">A melhorar</option>
</select>
<textarea id="nota"></textarea>
<button id="btnSalvarNota">Salvar Nota</button>

<h2>Pesquisar</h2>
<input id="buscaNome" placeholder="Nome">
<input id="buscaCategoria" placeholder="Categoria">
<button id="btnBuscar">Buscar</button>
<div id="resultado"></div>

<h2 id="tituloNotas" style="display:none">📒 Notas da Pessoa</h2>
<div id="listaNotas"></div>

<h2>📊 Gráfico por tipo de nota</h2>
<canvas id="grafico"></canvas>
</div>

<div id="painelAdmin" class="container" style="display:none">
<h2>🗑️ Lixeira (Admin)</h2>
<div class="card">
  <input id="filtroLixeiraUsuario" placeholder="Filtrar por usuário">
  <input id="filtroLixeiraData" type="date">
  <button id="btnFiltrarLixeira">Filtrar</button>
  <button class="danger" id="btnLimparLixeira">Limpar Lixeira</button>
</div>
<div id="listaLixeira"></div>
<h2>📜 Logs de ações (Admin)</h2>
<div id="listaLogs"></div>
<h2>⚙️ Painel Admin</h2>
<input id="novoUsuario" placeholder="Usuário">
<input id="senhaUsuario" placeholder="Senha">
<select id="nivelUsuario">
<option value="admin">Admin</option>
<option value="user">Usuário</option>
</select>
<button id="btnAddUsuario">Adicionar Usuário</button>
<h3>👥 Usuários cadastrados</h3>
<div id="listaUsuarios"></div>
</div>

<footer>© 2025 – Criado por <b>CLX</b></footer>

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

let usuarios = [], usuarioLogado = null, pessoas = [], pessoaEditando = null, chart = null, lixeira=[], logs=[];
let el = {};

// -------------------- Funções --------------------
function renderUsuarios(){
  if(!el.listaUsuarios) return;
  el.listaUsuarios.innerHTML='';
  usuarios.forEach(u=>{
    el.listaUsuarios.innerHTML+=`
      <div class='card'>
        <b>${u.usuario}</b> (${u.nivel}) - ${u.ativo?'Ativo':'Bloqueado'}<br>
        <input placeholder='Nova senha' id='senha_${u.id}'>
        <button onclick="trocarSenha('${u.id}')">Trocar senha</button>
        <button class='danger' onclick="bloquearUsuario('${u.id}',${u.ativo})">${u.ativo?'Bloquear':'Desbloquear'}</button>
        ${u.usuario!=='CLX'?`<button class='danger' onclick="excluirUsuario('${u.id}')">Excluir usuário</button>`:''}
      </div>`;
  });
}

async function carregarUsuarios(){
  const s = await getDocs(collection(db,'usuarios'));
  usuarios = [];
  s.forEach(d=>usuarios.push({id:d.id,...d.data()}));

  // Garantir que o admin CLX exista
  if(!usuarios.find(u=>u.usuario==='CLX')){
    await addDoc(collection(db,'usuarios'),{usuario:'CLX',senha:'02072007',nivel:'admin',ativo:true});
    await carregarUsuarios(); // recarregar
    return;
  }

  renderUsuarios();
}

// ---------------- Login ----------------
async function login(){
  await carregarUsuarios();
  const u = usuarios.find(u=>u.usuario===el.loginUsuario.value && u.senha===el.loginSenha.value);
  if(!u){ el.erro.innerText='Login inválido'; return; }
  if(u.ativo===false){ el.erro.innerText='Usuário bloqueado'; return; }
  usuarioLogado = u;
  el.login.style.display='none';
  el.sistema.style.display='block';
  if(u.nivel==='admin'){
    el.adminGear.style.display='block';
    carregarLixeira();
    carregarLogs();
  }
  carregarPessoas();
}

// ---------------- Outras funções importantes ----------------
async function addUsuario(){
  await addDoc(collection(db,'usuarios'),{
    usuario: el.novoUsuario.value,
    senha: el.senhaUsuario.value,
    nivel: el.nivelUsuario.value,
    ativo: true
  });
  el.novoUsuario.value='';
  el.senhaUsuario.value='';
  carregarUsuarios();
}

async function carregarPessoas(){
  const s = await getDocs(collection(db,'pessoas'));
  pessoas = [];
  s.forEach(d=>pessoas.push({id:d.id,...d.data()}));
  el.pessoaNota.innerHTML='';
  pessoas.forEach((p,i)=>el.pessoaNota.add(new Option(p.nome,i)));
  atualizarGrafico();
}

async function salvarPessoa(){
  const dados={
    nome: el.nome.value,
    categoria: el.categoria.value,
    anoEntrada: el.anoEntrada.value,
    matricula: el.matricula.value,
    email: el.email.value,
    telefone: el.telefone.value,
    cpf: el.cpf.value,
    rg: el.rg.value,
    dataNascimento: el.dataNascimento.value,
    contato: el.contato.value
  };

  if(pessoaEditando){
    await updateDoc(doc(db,'pessoas',pessoaEditando.id),dados);
    pessoaEditando = null;
  }else{
    await addDoc(collection(db,'pessoas'),{...dados,notas:[]});
  }

  Object.keys(dados).forEach(k=> el[k].value='');
  carregarPessoas();
}

// Funções de notas, lixeira e logs seguem igual
// ... (Mantém todas as funções que já enviamos, como salvarNota, excluirNota, exportarExcel, atualizarGrafico, carregarLixeira, renderLixeira, etc.)

// -------------------- Inicialização --------------------
window.addEventListener('DOMContentLoaded',()=>{
  el = {
    login: document.getElementById('login'),
    sistema: document.getElementById('sistema'),
    adminGear: document.getElementById('adminGear'),
    painelAdmin: document.getElementById('painelAdmin'),
    erro: document.getElementById('erro'),
    loginUsuario: document.getElementById('loginUsuario'),
    loginSenha: document.getElementById('loginSenha'),
    btnLogin: document.getElementById('btnLogin'),
    btnLogout: document.getElementById('btnLogout'),
    btnSalvarPessoa: document.getElementById('btnSalvarPessoa'),
    btnExcluirPessoa: document.getElementById('btnExcluirPessoa'),
    btnSalvarNota: document.getElementById('btnSalvarNota'),
    btnBuscar: document.getElementById('btnBuscar'),
    btnAddUsuario: document.getElementById('btnAddUsuario'),
    listaUsuarios: document.getElementById('listaUsuarios'),
    nome: document.getElementById('nome'),
    categoria: document.getElementById('categoria'),
    anoEntrada: document.getElementById('anoEntrada'),
    matricula: document.getElementById('matricula'),
    email: document.getElementById('email'),
    telefone: document.getElementById('telefone'),
    cpf: document.getElementById('cpf'),
    rg: document.getElementById('rg'),
    dataNascimento: document.getElementById('dataNascimento'),
    contato: document.getElementById('contato'),
    pessoaNota: document.getElementById('pessoaNota'),
    tipoNota: document.getElementById('tipoNota'),
    nota: document.getElementById('nota'),
    buscaNome: document.getElementById('buscaNome'),
    buscaCategoria: document.getElementById('buscaCategoria'),
    resultado: document.getElementById('resultado'),
    grafico: document.getElementById('grafico'),
    listaNotas: document.getElementById('listaNotas'),
    tituloNotas: document.getElementById('tituloNotas'),
    novoUsuario: document.getElementById('novoUsuario'),
    senhaUsuario: document.getElementById('senhaUsuario'),
    nivelUsuario: document.getElementById('nivelUsuario'),
    listaLixeira: document.getElementById('listaLixeira'),
    listaLogs: document.getElementById('listaLogs'),
    filtroLixeiraUsuario: document.getElementById('filtroLixeiraUsuario'),
    filtroLixeiraData: document.getElementById('filtroLixeiraData'),
    btnFiltrarLixeira: document.getElementById('btnFiltrarLixeira'),
    btnLimparLixeira: document.getElementById('btnLimparLixeira')
  };

  el.btnLogin.onclick = login;
  el.btnLogout.onclick = ()=>{
    usuarioLogado = null;
    el.sistema.style.display='none';
    el.painelAdmin.style.display='none';
    el.adminGear.style.display='none';
    el.login.style.display='block';
    el.loginUsuario.value='';
    el.loginSenha.value='';
    el.erro.innerText='';
  };
  el.btnSalvarPessoa.onclick = salvarPessoa;
  // Continuar ligando botões para notas, busca, excel, lixeira etc.
});
</script>
</body>
</html>
