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
  background-size: cover;
  padding:20px;
  min-height:100vh;
  display:flex;
  flex-direction:column;
}
.container{max-width:1100px;margin:auto;background:rgba(255,255,255,0.92);padding:20px;border-radius:8px;flex:1;box-shadow:0 10px 30px rgba(0,0,0,0.15)}
input,select,textarea,button{width:100%;padding:8px;margin-bottom:8px;border:1px solid #ccc;border-radius:4px}
button{background:#2563eb;color:#fff;border:none;border-radius:5px;cursor:pointer;font-weight:bold}
button:hover{background:#1d4ed8}
button.danger{background:#dc2626}
button.danger:hover{background:#b91c1c}
.card{border:1px solid #ddd;padding:10px;border-radius:6px;margin:6px 0;background:#fff}
.elogio{background:#dcfce7;padding:10px;margin:4px 0;border-left:5px solid #22c55e;border-radius:4px}
.reclamacao{background:#fee2e2;padding:10px;margin:4px 0;border-left:5px solid #ef4444;border-radius:4px}
.melhorar{background:#fef9c3;padding:10px;margin:4px 0;border-left:5px solid #eab308;border-radius:4px}
#login{max-width:400px;margin:100px auto;background:#fff;padding:20px;border-radius:8px;text-align:center;box-shadow:0 4px 15px rgba(0,0,0,0.1)}
#adminGear{position:fixed;top:20px;right:20px;font-size:24px;cursor:pointer;display:none;z-index:100}
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
<button id="btnLogout" style="float:right;background:#6b7280;width:auto">Sair</button>
<h1>Sistema Informa</h1>
<button id="btnExcel" style="width:auto">📊 Exportar para Excel</button>

<div id="secaoCadastro">
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
    <button id="btnSalvarPessoa">Salvar Cadastro</button>
</div>

<hr>

<h2>Adicionar Nota</h2>
<select id="pessoaNota"></select>
<select id="tipoNota">
    <option value="elogio">Elogio</option>
    <option value="reclamacao">Reclamação</option>
    <option value="melhorar">A melhorar</option>
</select>
<textarea id="nota" placeholder="Escreva a nota aqui..."></textarea>
<button id="btnSalvarNota">Salvar Nota</button>

<hr>

<h2>Pesquisar</h2>
<div style="display:flex; gap:10px">
    <input id="buscaNome" placeholder="Buscar por Nome">
    <input id="buscaCategoria" placeholder="Filtrar Categoria (Admin)">
</div>
<button id="btnBuscar">Buscar</button>
<div id="resultado"></div>

<h2 id="tituloNotas" style="display:none">📒 Notas da Pessoa</h2>
<div id="listaNotas"></div>

<h2>📊 Gráfico de Desempenho</h2>
<div style="max-width:400px; margin:auto">
    <canvas id="grafico"></canvas>
</div>
</div>

<div id="painelAdmin" class="container" style="display:none; margin-top:20px; border:2px solid #2563eb">
<h2>⚙️ Gerenciamento de Usuários</h2>
<div class="card">
    <input id="novoUsuario" placeholder="Novo Usuário">
    <input id="senhaUsuario" type="password" placeholder="Senha">
    <select id="nivelUsuario">
        <option value="user">Nível: Usuário Comum</option>
        <option value="admin">Nível: Administrador</option>
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
    <button id="btnAddUsuario">Criar Usuário</button>
</div>

<h3>👥 Usuários Cadastrados</h3>
<div id="listaUsuarios"></div>

<hr>
<h2>🗑️ Lixeira</h2>
<div class="card">
  <input id="filtroLixeiraUsuario" placeholder="Filtrar por quem excluiu">
  <button id="btnFiltrarLixeira">Filtrar</button>
  <button class="danger" id="btnLimparLixeira">Esvaziar Lixeira</button>
</div>
<div id="listaLixeira"></div>

<hr>
<h2>📜 Logs de Sistema</h2>
<div id="listaLogs" style="max-height:200px; overflow-y:auto; background:#eee; padding:10px"></div>
</div>

<footer>© 2025 – Criado por <b>CLX</b></footer>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, collection, addDoc, getDocs, updateDoc, deleteDoc, doc, query, where } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

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
    btnFiltrarLixeira: document.getElementById('btnFiltrarLixeira'),
    btnLimparLixeira: document.getElementById('btnLimparLixeira')
  };

  el.btnLogin.onclick = login;
  el.btnLogout.onclick = logout;
  el.btnSalvarPessoa.onclick = salvarPessoa;
  el.btnSalvarNota.onclick = salvarNota;
  el.btnBuscar.onclick = buscar;
  el.btnAddUsuario.onclick = addUsuario;
  document.getElementById('btnExcel').onclick = exportarExcel;
  el.btnFiltrarLixeira.onclick = filtrarLixeira;
  el.btnLimparLixeira.onclick = limparLixeira;
  el.adminGear.onclick = toggleAdmin;

  carregarUsuarios(); 
});

async function carregarUsuarios(){
  const s = await getDocs(collection(db,'usuarios'));
  usuarios = [];
  s.forEach(d=>usuarios.push({id:d.id,...d.data()}));
  renderUsuarios();
}

async function login(){
  await carregarUsuarios();
  const u = usuarios.find(u=>u.usuario===el.loginUsuario.value && u.senha===el.loginSenha.value);
  if(!u){ el.erro.innerText='Login inválido'; return; }
  if(u.ativo===false){ el.erro.innerText='Usuário bloqueado'; return; }
  
  usuarioLogado = u;
  el.login.style.display='none';
  el.sistema.style.display='block';
  
  // Se não for admin, oculta campo de busca de categoria (ele só busca na dele)
  if(u.nivel !== 'admin') {
      el.buscaCategoria.style.display = 'none';
      document.getElementById('secaoCadastro').style.display = 'none'; // Usuário comum não cadastra pessoas
  } else {
      el.adminGear.style.display='block';
      el.buscaCategoria.style.display = 'block';
      document.getElementById('secaoCadastro').style.display = 'block';
      carregarLixeira();
      carregarLogs();
  }
  carregarPessoas();
}

function logout(){
    usuarioLogado = null;
    el.sistema.style.display='none';
    el.painelAdmin.style.display='none';
    el.adminGear.style.display='none';
    el.login.style.display='block';
    el.loginUsuario.value='';
    el.loginSenha.value='';
}

async function addUsuario(){
  if(!el.novoUsuario.value || !el.senhaUsuario.value) return alert("Preencha os dados");
  await addDoc(collection(db,'usuarios'),{
    usuario: el.novoUsuario.value,
    senha: el.senhaUsuario.value,
    nivel: el.nivelUsuario.value,
    categoria: el.categoriaUsuario.value,
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

  el.pessoaNota.innerHTML='<option value="">Selecione...</option>';
  pessoas.forEach((p,i)=>{
    // FILTRO: Só mostra no select se for admin ou da mesma categoria
    if(usuarioLogado.nivel==='admin' || p.categoria===usuarioLogado.categoria){
      el.pessoaNota.add(new Option(p.nome, i));
    }
  });
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
    contato: el.contato.value,
    notas: pessoaEditando ? pessoaEditando.notas || [] : []
  };

  if(pessoaEditando){
    await updateDoc(doc(db,'pessoas',pessoaEditando.id),dados);
    pessoaEditando = null;
    alert("Perfil atualizado!");
  }else{
    await addDoc(collection(db,'pessoas'),dados);
    alert("Pessoa cadastrada!");
  }

  // Limpar campos
  ['nome','anoEntrada','matricula','email','telefone','cpf','rg','dataNascimento','contato'].forEach(k=> el[k].value='');
  el.categoria.value = "";
  carregarPessoas();
}

async function salvarNota(){
  const idx = el.pessoaNota.value;
  if(idx === "") return alert("Selecione uma pessoa");
  const p = pessoas[idx];

  // SEGURANÇA LÓGICA
  if(usuarioLogado.nivel!=='admin' && p.categoria !== usuarioLogado.categoria){
    alert('Acesso negado para esta categoria');
    return;
  }

  const novaNota = {
      tipo: el.tipoNota.value,
      texto: el.nota.value,
      autor: usuarioLogado.usuario,
      data: new Date().toLocaleDateString('pt-BR')
  };

  p.notas = p.notas || [];
  p.notas.push(novaNota);
  
  await updateDoc(doc(db,'pessoas',p.id),{notas:p.notas});
  el.nota.value='';
  alert("Nota adicionada!");
  atualizarGrafico();
}

function buscar(){
  el.resultado.innerHTML='';
  el.listaNotas.innerHTML='';
  el.tituloNotas.style.display='none';

  pessoas.filter(p=>{
    // FILTRO DE CATEGORIA PARA USUÁRIOS COMUNS
    if(usuarioLogado.nivel !== 'admin') {
        return p.categoria === usuarioLogado.categoria && 
               (!el.buscaNome.value || p.nome.toLowerCase().includes(el.buscaNome.value.toLowerCase()));
    }
    // FILTRO PARA ADMIN
    return (!el.buscaNome.value || p.nome.toLowerCase().includes(el.buscaNome.value.toLowerCase())) &&
           (!el.buscaCategoria.value || p.categoria.includes(el.buscaCategoria.value));
  }).forEach((p)=>{
    const realIdx = pessoas.indexOf(p);
    el.resultado.innerHTML+=`
      <div class='card'>
        <b>${p.nome}</b> — <small>${p.categoria}</small><br>
        <button onclick="verNotas(${realIdx})" style="width:auto">Ver notas</button>
        ${usuarioLogado.nivel==='admin' ? `<button onclick="editarPessoa(${realIdx})" style="width:auto; background:#f59e0b">Editar</button>` : ''}
        ${usuarioLogado.nivel==='admin' ? `<button class='danger' onclick="excluirPessoaDireto('${p.id}')" style="width:auto">Excluir</button>` : ''}
      </div>`;
  });
}

window.editarPessoa = function(index){
  const p = pessoas[index];
  pessoaEditando = p;
  el.nome.value = p.nome||'';
  el.categoria.value = p.categoria||'';
  el.anoEntrada.value = p.anoEntrada||'';
  el.matricula.value = p.matricula||'';
  el.email.value = p.email||'';
  el.telefone.value = p.telefone||'';
  el.cpf.value = p.cpf||'';
  el.rg.value = p.rg||'';
  el.dataNascimento.value = p.dataNascimento||'';
  el.contato.value = p.contato||'';
  window.scrollTo({top:0,behavior:'smooth'});
}

window.verNotas = function(index){
  const p = pessoas[index];
  el.listaNotas.innerHTML='';
  el.tituloNotas.style.display='block';

  if(!p.notas || p.notas.length===0){
    el.listaNotas.innerHTML='<p>Nenhuma nota cadastrada.</p>';
    return;
  }

  p.notas.forEach((n,ni)=>{
    el.listaNotas.innerHTML+=`
      <div class='${n.tipo}'>
        <b>${n.tipo.toUpperCase()}</b> — ${n.texto}<br>
        <small>Por: ${n.autor} em ${n.data}</small>
        ${usuarioLogado.nivel==='admin'?`<br><button class='danger' onclick="excluirNota(${index},${ni})" style="padding:2px 5px; font-size:10px; width:auto">Excluir Nota</button>`:''}
      </div>`;
  });
}

window.excluirNota = async function(pIndex,nIndex){
  if(!confirm('Deseja excluir esta nota permanentemente?')) return;
  const p = pessoas[pIndex];
  const notaRemovida = p.notas.splice(nIndex,1)[0];

  await addDoc(collection(db,'lixeira'),{tipo:'nota',dados:notaRemovida,pessoa:p.nome,excluidoPor:usuarioLogado.usuario,data:new Date().toLocaleString()});
  await addDoc(collection(db,'logs'),{acao:'Excluir nota',alvo:p.nome,usuario:usuarioLogado.usuario,data:new Date().toLocaleString()});

  await updateDoc(doc(db,'pessoas',p.id),{notas:p.notas});
  verNotas(pIndex);
  atualizarGrafico();
}

window.excluirPessoaDireto = async function(id){
  const p = pessoas.find(x=>x.id===id);
  if(!p || !confirm('Excluir perfil de '+p.nome+'?')) return;

  await addDoc(collection(db,'lixeira'),{tipo:'pessoa',dados:p,excluidoPor:usuarioLogado.usuario,data:new Date().toLocaleString()});
  await addDoc(collection(db,'logs'),{acao:'Excluir pessoa',alvo:p.nome,usuario:usuarioLogado.usuario,data:new Date().toLocaleString()});
  await deleteDoc(doc(db,'pessoas',id));
  carregarPessoas();
  el.resultado.innerHTML = "";
}

function renderUsuarios(){
  if(!el.listaUsuarios) return;
  el.listaUsuarios.innerHTML='';
  usuarios.forEach(u=>{
    el.listaUsuarios.innerHTML+=`
      <div class='card'>
        <b>${u.usuario}</b> [${u.nivel}] — <small>${u.categoria || 'Global'}</small><br>
        Status: ${u.ativo?'Ativo':'Bloqueado'}<br>
        <button onclick="bloquearUsuario('${u.id}',${u.ativo})" style="width:auto; font-size:11px">${u.ativo?'Bloquear':'Ativar'}</button>
        ${u.usuario!=='CLX'?`<button class='danger' onclick="excluirUsuario('${u.id}')" style="width:auto; font-size:11px">Remover</button>`:''}
      </div>`;
  });
}

window.excluirUsuario = async function(id){
  if(!confirm('Excluir este usuário do sistema?')) return;
  await deleteDoc(doc(db,'usuarios',id));
  carregarUsuarios();
}

window.bloquearUsuario = async function(id,statusAtual){
  await updateDoc(doc(db,'usuarios',id),{ativo:!statusAtual});
  carregarUsuarios();
}

function atualizarGrafico(){
  let e=0,r=0,m=0;
  pessoas.forEach(p=>{
    // Gráfico também respeita a categoria do usuário logado
    if(usuarioLogado && usuarioLogado.nivel!=='admin' && p.categoria!==usuarioLogado.categoria) return;
    p.notas?.forEach(n=>{
      if(n.tipo==='elogio') e++;
      if(n.tipo==='reclamacao') r++;
      if(n.tipo==='melhorar') m++;
    });
  });
  if(chart) chart.destroy();
  chart = new Chart(el.grafico,{
      type:'pie',
      data:{
          labels:['Elogio','Reclamação','A melhorar'],
          datasets:[{data:[e,r,m], backgroundColor:['#22c55e','#ef4444','#eab308']}]
      }
  });
}

// Funções de Admin e Excel mantidas conforme original...
function toggleAdmin() {
    el.painelAdmin.style.display = el.painelAdmin.style.display==='none' ? 'block' : 'none';
}

function exportarExcel(){
  const wb = XLSX.utils.book_new();
  const dadosExportar = [];
  
  pessoas.forEach(p=>{
    if(usuarioLogado.nivel!=='admin' && p.categoria!==usuarioLogado.categoria) return;
    
    if(!p.notas || p.notas.length===0){
        dadosExportar.push({Nome:p.nome, Categoria:p.categoria, Nota:"Sem notas"});
    } else {
        p.notas.forEach(n=>{
            dadosExportar.push({Nome:p.nome, Categoria:p.categoria, Tipo:n.tipo, Texto:n.texto, Data:n.data, Autor:n.autor});
        });
    }
  });

  const ws = XLSX.utils.json_to_sheet(dadosExportar);
  XLSX.utils.book_append_sheet(wb, ws, "Relatório");
  XLSX.writeFile(wb,'relatorio_informa.xlsx');
}

async function carregarLixeira(){
  const s = await getDocs(collection(db,'lixeira'));
  lixeira = [];
  s.forEach(d=>lixeira.push({id:d.id,...d.data()}));
  renderLixeira(lixeira);
}

function renderLixeira(lista){
  el.listaLixeira.innerHTML='';
  lista.forEach(x=>{
    el.listaLixeira.innerHTML+=`<div class='card'><b>${x.tipo.toUpperCase()}</b>: ${x.pessoa || x.dados?.nome || ''}<br><small>Por: ${x.excluidoPor}</small></div>`;
  });
}

async function carregarLogs(){
  const s = await getDocs(collection(db,'logs'));
  el.listaLogs.innerHTML='';
  s.forEach(d=>{
    const x=d.data();
    el.listaLogs.innerHTML+=`<div style='font-size:11px; border-bottom:1px solid #ccc'>${x.data} - ${x.usuario}: ${x.acao} (${x.alvo})</div>`;
  });
}

function filtrarLixeira() { alert("Filtro aplicado (Simulação)"); }
async function limparLixeira() { if(confirm("Limpar lixeira?")) { /* lógica delete docs */ } }

</script>
</body>
</html>
