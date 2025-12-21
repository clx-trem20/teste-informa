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
  <option value="">Selecione categoria</option>
  <option>Meio Ambiente</option>
  <option>Linguagens</option>
  <option>Comunicações</option>
  <option>Edição de Vídeo</option>
  <option>Cultura</option>
  <option>Secretaria</option>
  <option>Esportes</option>
  <option>Presidência</option>
  <option>Informações</option>
  <option>Designer</option>
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
<select id="categoriaUsuario">
  <option value="">Categoria do usuário</option>
  <option>Meio Ambiente</option>
  <option>Linguagens</option>
  <option>Comunicações</option>
  <option>Edição de Vídeo</option>
  <option>Cultura</option>
  <option>Secretaria</option>
  <option>Esportes</option>
  <option>Presidência</option>
  <option>Informações</option>
  <option>Designer</option>
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

const categorias = ["Meio Ambiente","Linguagens","Comunicações","Edição de Vídeo","Cultura","Secretaria","Esportes","Presidência","Informações","Designer"];

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
    categoriaUsuario: document.getElementById('categoriaUsuario'),
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
  el.btnExcluirPessoa.onclick = excluirPessoa;
  el.btnSalvarNota.onclick = salvarNota;
  el.btnBuscar.onclick = buscar;
  el.btnAddUsuario.onclick = addUsuario;
  document.getElementById('btnExcel').onclick = exportarExcel;
  el.btnFiltrarLixeira.onclick = filtrarLixeira;
  el.btnLimparLixeira.onclick = limparLixeira;
  el.adminGear.onclick = ()=> el.painelAdmin.style.display = el.painelAdmin.style.display==='none' ? 'block' : 'none';
});

// ... o restante do código mantém todas as funções atuais (carregarUsuarios, login, addUsuario, carregarPessoas, salvarPessoa, notas, lixeira, logs, gráficos, exportar Excel) ...
// No momento de popular select pessoaNota, filtramos apenas pela categoria do usuário se ele for nivel user
