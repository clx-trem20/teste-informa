<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Sistema Informa</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
<style>
body{font-family:'Segoe UI',sans-serif;background:#f0f0f0;padding:20px;min-height:100vh;display:flex;flex-direction:column;}
.container{max-width:1100px;margin:auto;background:rgba(255,255,255,0.92);padding:20px;border-radius:8px;flex:1;box-shadow:0 10px 30px rgba(0,0,0,0.15);}
input,select,textarea,button{width:100%;padding:8px;margin-bottom:8px;}
button{background:#2563eb;color:#fff;border:none;border-radius:5px;cursor:pointer;}
button.danger{background:#dc2626;}
.card{border:1px solid #ddd;padding:10px;border-radius:6px;margin:6px 0;}
.elogio{background:#dcfce7;padding:6px;margin:4px 0;}
.reclamacao{background:#fee2e2;padding:6px;margin:4px 0;}
.melhorar{background:#fef9c3;padding:6px;margin:4px 0;}
#login{max-width:400px;margin:100px auto;background:#fff;padding:20px;border-radius:8px;text-align:center;}
#adminGear{position:fixed;top:20px;right:20px;font-size:24px;cursor:pointer;display:none;}
footer{text-align:center;margin-top:30px;color:#ffffff;font-size:14px;padding:12px 0;background:rgba(0,0,0,0.55);}
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
  <option value="">Selecione a categoria</option>
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
<h2>⚙️ Painel Admin</h2>
<input id="novoUsuario" placeholder="Usuário">
<input id="senhaUsuario" placeholder="Senha">
<select id="nivelUsuario">
<option value="admin">Admin</option>
<option value="user">Usuário</option>
</select>
<select id="categoriaUsuario">
  <option value="">Selecione a categoria do usuário</option>
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

<h3>👥 Usuários cadastrados</h3>
<div id="listaUsuarios"></div>

<h2>🗑️ Lixeira</h2>
<div id="listaLixeira"></div>

<h2>📜 Logs de Ações</h2>
<div id="listaLogs"></div>
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

let usuarios=[], usuarioLogado=null, pessoas=[], pessoaEditando=null, lixeira=[], logs=[], chart=null;
let el={};

window.addEventListener('DOMContentLoaded',()=>{
  // Elementos
  el.login=document.getElementById('login');
  el.loginUsuario=document.getElementById('loginUsuario');
  el.loginSenha=document.getElementById('loginSenha');
  el.btnLogin=document.getElementById('btnLogin');
  el.sistema=document.getElementById('sistema');
  el.adminGear=document.getElementById('adminGear');
  el.painelAdmin=document.getElementById('painelAdmin');
  el.btnLogout=document.getElementById('btnLogout');
  el.btnSalvarPessoa=document.getElementById('btnSalvarPessoa');
  el.btnSalvarNota=document.getElementById('btnSalvarNota');
  el.btnBuscar=document.getElementById('btnBuscar');
  el.btnAddUsuario=document.getElementById('btnAddUsuario');
  el.listaUsuarios=document.getElementById('listaUsuarios');
  el.pessoaNota=document.getElementById('pessoaNota');
  el.tipoNota=document.getElementById('tipoNota');
  el.nota=document.getElementById('nota');
  el.nome=document.getElementById('nome');
  el.categoria=document.getElementById('categoria');
  el.anoEntrada=document.getElementById('anoEntrada');
  el.matricula=document.getElementById('matricula');
  el.email=document.getElementById('email');
  el.telefone=document.getElementById('telefone');
  el.cpf=document.getElementById('cpf');
  el.rg=document.getElementById('rg');
  el.dataNascimento=document.getElementById('dataNascimento');
  el.contato=document.getElementById('contato');
  el.buscaNome=document.getElementById('buscaNome');
  el.buscaCategoria=document.getElementById('buscaCategoria');
  el.resultado=document.getElementById('resultado');
  el.grafico=document.getElementById('grafico');
  el.listaLixeira=document.getElementById('listaLixeira');
  el.listaLogs=document.getElementById('listaLogs');

  el.btnLogin.onclick=login;
  el.btnLogout.onclick=()=>location.reload();
});

async function carregarUsuarios(){
  const s = await getDocs(collection(db,'usuarios'));
  usuarios = [];
  s.forEach(d=>usuarios.push({id:d.id,...d.data()}));
}

async function login(){
  await carregarUsuarios();
  const u = usuarios.find(u=>u.usuario.toLowerCase()===el.loginUsuario.value.toLowerCase() && u.senha===el.loginSenha.value);
  if(!u){ alert('Login inválido'); return; }
  usuarioLogado=u;
  el.login.style.display='none';
  el.sistema.style.display='block';
  if(u.nivel==='admin'){
    el.adminGear.style.display='block';
    el.painelAdmin.style.display='block';
  }else{
    el.adminGear.style.display='none';
    el.painelAdmin.style.display='none';
  }
  alert(`Bem-vindo ${u.usuario}, categoria: ${u.categoria || 'Todas'}`);
}

// Aqui você adicionaria todas funções de:
// - carregarPessoas()
// - salvarPessoa()
// - salvarNota()
// - buscar()
// - exportExcel()
// - atualizarGrafico()
// - carregarLixeira()
// - restaurarItem()
// - carregarLogs()
// - addUsuario()
// - renderUsuarios()

</script>
</body>
</html>
