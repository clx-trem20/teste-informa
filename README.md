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
  background: #f0f2f5;
  padding:20px;
  min-height:100vh;
  display:flex;
  flex-direction:column;
}
.container{max-width:1100px;margin:auto;background:rgba(255,255,255,0.95);padding:20px;border-radius:8px;flex:1;box-shadow:0 10px 30px rgba(0,0,0,0.1)}
input,select,textarea,button{width:100%;padding:10px;margin-bottom:10px;border:1px solid #ccc;border-radius:5px;box-sizing: border-box;}
button{background:#2563eb;color:#fff;border:none;cursor:pointer;font-weight:bold;transition: 0.3s;}
button:hover{background:#1d4ed8}
button.danger{background:#dc2626}
button.danger:hover{background:#b91c1c}
.card{border:1px solid #eee;padding:15px;border-radius:8px;margin:10px 0;background:#fff;box-shadow: 0 2px 5px rgba(0,0,0,0.05)}
.elogio{background:#dcfce7;padding:10px;margin:8px 0;border-left:5px solid #22c55e;border-radius:4px}
.reclamacao{background:#fee2e2;padding:10px;margin:8px 0;border-left:5px solid #ef4444;border-radius:4px}
.melhorar{background:#fef9c3;padding:10px;margin:8px 0;border-left:5px solid #eab308;border-radius:4px}
#login{max-width:400px;margin:100px auto;background:#fff;padding:30px;border-radius:8px;text-align:center;box-shadow:0 4px 20px rgba(0,0,0,0.1)}
#adminGear{position:fixed;top:20px;right:20px;font-size:28px;cursor:pointer;display:none;z-index:100}
footer{text-align:center;margin-top:30px;color:#666;font-size:14px;padding:15px;background:#ddd;border-radius: 8px 8px 0 0;}
hr{margin: 20px 0; border: 0; border-top: 1px solid #eee;}
</style>
</head>
<body>

<div id="login">
<h2>🔐 Acesso ao Sistema</h2>
<input id="loginUsuario" placeholder="Usuário">
<input id="loginSenha" type="password" placeholder="Senha">
<button id="btnLogin">Entrar</button>
<p id="erro" style="color:red"></p>
</div>

<div id="adminGear">⚙️</div>

<div class="container" id="sistema" style="display:none">
<button id="btnLogout" style="float:right;background:#6b7280;width:auto;padding:5px 15px">Sair</button>
<h1>Informa Dashboard</h1>
<button id="btnExcel" style="width:auto; background:#10b981">📊 Relatório Excel</button>

<div id="secaoCadastro">
    <h3>📝 Cadastrar / Editar Colaborador</h3>
    <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px">
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
    </div>
    <div style="display:grid; grid-template-columns: 1fr 1fr 1fr; gap:10px">
        <input id="anoEntrada" placeholder="Ano de Entrada">
        <input id="matricula" placeholder="Matrícula">
        <input id="email" placeholder="E-mail">
    </div>
    <div style="display:grid; grid-template-columns: 1fr 1fr 1fr; gap:10px">
        <input id="cpf" placeholder="CPF">
        <input id="rg" placeholder="RG">
        <input id="telefone" placeholder="Telefone">
    </div>
    <button id="btnSalvarPessoa">Salvar Cadastro</button>
</div>

<hr>

<h3>✍️ Adicionar Nova Nota</h3>
<div style="display:grid; grid-template-columns: 2fr 1fr; gap:10px">
    <select id="pessoaNota"></select>
    <select id="tipoNota">
        <option value="elogio">Elogio</option>
        <option value="reclamacao">Reclamação</option>
        <option value="melhorar">A melhorar</option>
    </select>
</div>
<textarea id="nota" placeholder="Descreva aqui o feedback..."></textarea>
<button id="btnSalvarNota">Publicar Nota</button>

<hr>

<h3>🔍 Consulta de Perfis e Notas</h3>
<div style="display:flex; gap:10px">
    <input id="buscaNome" placeholder="Nome da pessoa">
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
<button id="btnBuscar">Pesquisar</button>
<div id="resultado"></div>

<div id="secaoNotas" style="display:none; background:#f9fafb; padding:15px; border-radius:8px; margin-top:20px">
    <h3 id="tituloNotas">📒 Histórico de Notas</h3>
    <div id="listaNotas"></div>
</div>

<h3>📊 Estatísticas</h3>
<div style="max-width:300px; margin:auto">
    <canvas id="grafico"></canvas>
</div>
</div>

<div id="painelAdmin" class="container" style="display:none; margin-top:20px; border-top:4px solid #2563eb">
<h2>Gerenciamento Admin</h2>
<div class="card">
    <h4>Criar Novo Usuário</h4>
    <input id="novoUsuario" placeholder="Nome de Usuário">
    <input id="senhaUsuario" type="password" placeholder="Senha">
    <select id="nivelUsuario">
        <option value="user">Acesso: Usuário Comum</option>
        <option value="admin">Acesso: Administrador</option>
    </select>
    <select id="categoriaUsuario">
        <option value="">Acesso Global (Admin)</option>
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
    <button id="btnAddUsuario">Cadastrar Usuário</button>
</div>

<h4>Usuários do Sistema</h4>
<div id="listaUsuarios"></div>
</div>

<footer>© 2025 – Sistema Informa – Desenvolvido por <b>CLX</b></footer>

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
    pessoaNota: document.getElementById('pessoaNota'),
    tipoNota: document.getElementById('tipoNota'),
    nota: document.getElementById('nota'),
    buscaNome: document.getElementById('buscaNome'),
    buscaCategoria: document.getElementById('buscaCategoria'),
    resultado: document.getElementById('resultado'),
    grafico: document.getElementById('grafico'),
    listaNotas: document.getElementById('listaNotas'),
    secaoNotas: document.getElementById('secaoNotas'),
    novoUsuario: document.getElementById('novoUsuario'),
    senhaUsuario: document.getElementById('senhaUsuario'),
    nivelUsuario: document.getElementById('nivelUsuario'),
    categoriaUsuario: document.getElementById('categoriaUsuario')
  };

  el.btnLogin.onclick = login;
  el.btnLogout.onclick = () => location.reload();
  el.btnSalvarPessoa.onclick = salvarPessoa;
  el.btnSalvarNota.onclick = salvarNota;
  el.btnBuscar.onclick = buscar;
  el.btnAddUsuario.onclick = addUsuario;
  el.adminGear.onclick = () => el.painelAdmin.style.display = el.painelAdmin.style.display==='none' ? 'block' : 'none';
  document.getElementById('btnExcel').onclick = exportarExcel;

  carregarUsuarios();
});

async function carregarUsuarios(){
  const s = await getDocs(collection(db,'usuarios'));
  usuarios = [];
  s.forEach(d=>usuarios.push({id:d.id,...d.data()}));
  renderUsuarios();
}

async function login(){
  const u = usuarios.find(u=>u.usuario===el.loginUsuario.value && u.senha===el.loginSenha.value);
  if(!u){ el.erro.innerText='Usuário ou senha incorretos'; return; }
  if(!u.ativo){ el.erro.innerText='Conta desativada'; return; }
  
  usuarioLogado = u;
  el.login.style.display='none';
  el.sistema.style.display='block';
  
  if(u.nivel === 'admin') {
      el.adminGear.style.display='block';
      el.buscaCategoria.disabled = false;
  } else {
      el.buscaCategoria.value = u.categoria;
      el.buscaCategoria.disabled = true;
      document.getElementById('secaoCadastro').style.display = 'none';
  }
  carregarPessoas();
}

async function carregarPessoas(){
  const s = await getDocs(collection(db,'pessoas'));
  pessoas = [];
  s.forEach(d=>pessoas.push({id:d.id,...d.data()}));
  
  el.pessoaNota.innerHTML = '<option value="">Selecione o colaborador...</option>';
  pessoas.forEach((p,i)=>{
    if(usuarioLogado.nivel==='admin' || p.categoria === usuarioLogado.categoria){
      el.pessoaNota.add(new Option(p.nome, i));
    }
  });
  atualizarGrafico();
}

async function salvarPessoa(){
  const dados = {
    nome: el.nome.value, categoria: el.categoria.value,
    anoEntrada: el.anoEntrada.value, matricula: el.matricula.value,
    email: el.email.value, cpf: el.cpf.value, rg: el.rg.value,
    telefone: el.telefone.value, notas: pessoaEditando ? pessoaEditando.notas || [] : []
  };
  if(pessoaEditando){
    await updateDoc(doc(db,'pessoas',pessoaEditando.id),dados);
    pessoaEditando = null;
  } else {
    await addDoc(collection(db,'pessoas'),dados);
  }
  alert("Dados salvos com sucesso!");
  carregarPessoas();
}

async function salvarNota(){
  const idx = el.pessoaNota.value;
  if(!idx || !el.nota.value) return alert("Preencha a nota e o colaborador");
  const p = pessoas[idx];

  const novaNota = {
      tipo: el.tipoNota.value,
      texto: el.nota.value,
      autor: usuarioLogado.usuario,
      data: new Date().toLocaleDateString('pt-BR')
  };
  p.notas = p.notas || [];
  p.notas.push(novaNota);
  await updateDoc(doc(db,'pessoas',p.id),{notas:p.notas});
  el.nota.value = "";
  alert("Nota adicionada!");
  atualizarGrafico();
}

function buscar(){
  el.resultado.innerHTML='';
  el.secaoNotas.style.display = 'none';

  const filtroNome = el.buscaNome.value.toLowerCase();
  const filtroCat = el.buscaCategoria.value;

  const filtrados = pessoas.filter(p => {
    const matchNome = p.nome.toLowerCase().includes(filtroNome);
    const matchCat = filtroCat === "" || p.categoria === filtroCat;
    
    // Se for user comum, ele só vê a própria categoria independente do que digitar
    if(usuarioLogado.nivel !== 'admin') {
        return p.categoria === usuarioLogado.categoria && matchNome;
    }
    return matchNome && matchCat;
  });

  if(filtrados.length === 0) el.resultado.innerHTML = "<p>Nenhum resultado encontrado.</p>";

  filtrados.forEach(p => {
    const realIdx = pessoas.indexOf(p);
    el.resultado.innerHTML += `
      <div class='card'>
        <b>${p.nome}</b> (${p.categoria})<br>
        <button onclick="verNotas(${realIdx})" style="width:auto; margin-top:10px">Ver Notas</button>
        ${usuarioLogado.nivel==='admin' ? `<button onclick="excluirPessoa('${p.id}')" class="danger" style="width:auto">Excluir</button>` : ''}
      </div>`;
  });
}

window.verNotas = function(index){
  const p = pessoas[index];
  el.listaNotas.innerHTML = "";
  el.secaoNotas.style.display = 'block';
  document.getElementById('tituloNotas').innerText = "Notas de " + p.nome;

  if(!p.notas || p.notas.length === 0) {
      el.listaNotas.innerHTML = "<p>Sem notas registradas.</p>";
      return;
  }

  p.notas.forEach((n, ni) => {
    el.listaNotas.innerHTML += `
      <div class="${n.tipo}">
        <strong>${n.tipo.toUpperCase()}</strong>: ${n.texto}<br>
        <small>Por: ${n.autor} em ${n.data}</small>
        ${usuarioLogado.nivel==='admin' ? `<br><button class="danger" onclick="removerNota(${index}, ${ni})" style="width:auto; font-size:10px; padding:2px 5px">Excluir</button>` : ''}
      </div>`;
  });
  el.secaoNotas.scrollIntoView({behavior: 'smooth'});
}

window.removerNota = async function(pIdx, nIdx){
    if(!confirm("Excluir nota?")) return;
    pessoas[pIdx].notas.splice(nIdx, 1);
    await updateDoc(doc(db,'pessoas', pessoas[pIdx].id), {notas: pessoas[pIdx].notas});
    verNotas(pIdx);
    atualizarGrafico();
}

window.excluirPessoa = async function(id){
    if(!confirm("Excluir colaborador permanentemente?")) return;
    await deleteDoc(doc(db,'pessoas', id));
    carregarPessoas();
    el.resultado.innerHTML = "";
}

async function addUsuario(){
  await addDoc(collection(db,'usuarios'),{
    usuario: el.novoUsuario.value,
    senha: el.senhaUsuario.value,
    nivel: el.nivelUsuario.value,
    categoria: el.categoriaUsuario.value,
    ativo: true
  });
  alert("Usuário criado!");
  carregarUsuarios();
}

function renderUsuarios(){
  el.listaUsuarios.innerHTML = "";
  usuarios.forEach(u => {
    el.listaUsuarios.innerHTML += `
      <div class="card">
        <b>${u.usuario}</b> (${u.nivel}) - ${u.categoria || 'Global'}<br>
        <button onclick="toggleUser('${u.id}', ${u.ativo})" style="width:auto; font-size:10px">${u.ativo?'Bloquear':'Desbloquear'}</button>
      </div>`;
  });
}

window.toggleUser = async function(id, status){
    await updateDoc(doc(db,'usuarios', id), {ativo: !status});
    carregarUsuarios();
}

function atualizarGrafico(){
  let e=0, r=0, m=0;
  pessoas.forEach(p => {
    if(usuarioLogado.nivel==='admin' || p.categoria === usuarioLogado.categoria){
      p.notas?.forEach(n => {
        if(n.tipo==='elogio') e++;
        else if(n.tipo==='reclamacao') r++;
        else m++;
      });
    }
  });
  if(chart) chart.destroy();
  chart = new Chart(el.grafico, {
    type: 'doughnut',
    data: {
      labels: ['Elogios', 'Reclamações', 'Melhorar'],
      datasets: [{ data: [e,r,m], backgroundColor: ['#22c55e', '#ef4444', '#eab308'] }]
    }
  });
}

function exportarExcel(){
    let data = [];
    pessoas.forEach(p => {
        if(usuarioLogado.nivel==='admin' || p.categoria === usuarioLogado.categoria){
            if(p.notas) p.notas.forEach(n => {
                data.push({ Nome: p.nome, Categoria: p.categoria, Tipo: n.tipo, Nota: n.texto, Data: n.data, Autor: n.autor });
            });
        }
    });
    const ws = XLSX.utils.json_to_sheet(data);
    const wb = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(wb, ws, "Notas");
    XLSX.writeFile(wb, "Relatorio_Informa.xlsx");
}
</script>
</body>
</html>
