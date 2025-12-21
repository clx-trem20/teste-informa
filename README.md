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
  el.btnExcluirPessoa.onclick = excluirPessoa;
  el.btnSalvarNota.onclick = salvarNota;
  el.btnBuscar.onclick = buscar;
  el.btnAddUsuario.onclick = addUsuario;
  document.getElementById('btnExcel').onclick = exportarExcel;
  el.btnFiltrarLixeira.onclick = filtrarLixeira;
  el.btnLimparLixeira.onclick = limparLixeira;
  el.adminGear.onclick = ()=> el.painelAdmin.style.display = el.painelAdmin.style.display==='none' ? 'block' : 'none';
});
async function carregarUsuarios(){
  const s = await getDocs(collection(db,'usuarios'));
  usuarios = [];
  s.forEach(d=>usuarios.push({id:d.id,...d.data()}));

  // Cria usuário admin padrão se não existir
  if(!usuarios.find(u=>u.usuario==='CLX')){
    await addDoc(collection(db,'usuarios'),{
      usuario:'CLX',
      senha:'0207',
      nivel:'admin',
      categoria:'',
      ativo:true
    });
  }
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
  if(u.nivel==='admin'){
    el.adminGear.style.display='block';
    carregarLixeira();
    carregarLogs();
  }
  carregarPessoas();
}

async function addUsuario(){
  await addDoc(collection(db,'usuarios'),{
    usuario: el.novoUsuario.value,
    senha: el.senhaUsuario.value,
    nivel: el.nivelUsuario.value,
    categoria: el.categoriaUsuario.value,
    ativo: true
  });
  el.novoUsuario.value='';
  el.senhaUsuario.value='';
  el.categoriaUsuario.value='';
  carregarUsuarios();
}

async function carregarPessoas(){
  const s = await getDocs(collection(db,'pessoas'));
  pessoas = [];
  s.forEach(d=>pessoas.push({id:d.id,...d.data()}));

  // Preencher select de pessoas disponíveis para notas
  el.pessoaNota.innerHTML='';
  pessoas.forEach((p,i)=>{
    if(usuarioLogado.nivel==='admin' || p.categoria===usuarioLogado.categoria){
      el.pessoaNota.add(new Option(p.nome,i));
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
  }else{
    await addDoc(collection(db,'pessoas'),dados);
  }

  Object.keys(dados).forEach(k=> el[k].value='');
  carregarPessoas();
}

window.excluirPessoa = async function(){
  const p = pessoas[el.pessoaNota.value];
  if(!p) return;
  if(!confirm('⚠️ Confirma excluir este perfil?')) return;
  if(!confirm('❗ Essa ação é irreversível. Deseja continuar?')) return;

  await addDoc(collection(db,'lixeira'),{tipo:'pessoa',dados:p,excluidoPor:usuarioLogado.usuario,data:new Date().toISOString()});
  await addDoc(collection(db,'logs'),{acao:'Excluir pessoa',alvo:p.nome,usuario:usuarioLogado.usuario,data:new Date().toISOString()});
  await deleteDoc(doc(db,'pessoas',p.id));
  carregarPessoas();
}

async function salvarNota(){
  const p = pessoas[el.pessoaNota.value];
  if(!p) return;
  if(usuarioLogado.nivel!=='admin' && p.categoria !== usuarioLogado.categoria){
    alert('Você não pode adicionar notas para esta categoria');
    return;
  }
  p.notas.push({tipo:el.tipoNota.value,texto:el.nota.value,autor:usuarioLogado.usuario,data:new Date().toLocaleDateString()});
  await updateDoc(doc(db,'pessoas',p.id),{notas:p.notas});
  el.nota.value='';
  atualizarGrafico();
}

function buscar(){
  el.resultado.innerHTML='';
  el.listaNotas.innerHTML='';
  el.tituloNotas.style.display='none';

  pessoas.filter(p=>{
    if(usuarioLogado.nivel!=='admin' && p.categoria !== usuarioLogado.categoria) return false;
    return (!el.buscaNome.value || p.nome.includes(el.buscaNome.value)) &&
           (!el.buscaCategoria.value || p.categoria.includes(el.buscaCategoria.value));
  }).forEach((p,i)=>{
    el.resultado.innerHTML+=`
      <div class='card'>
        <b>${p.nome}</b> (${p.categoria})
        <button onclick="editarPessoa(${i})">Editar</button>
        <button onclick="verNotas(${i})">Ver notas</button>
        ${usuarioLogado.nivel==='admin'?`<button class='danger' onclick="excluirPessoaDireto('${p.id}')">Excluir</button>`:''}
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
        <small>${n.autor} • ${n.data}</small>
        ${usuarioLogado.nivel==='admin'?`<br><button class='danger' onclick="excluirNota(${index},${ni})">Excluir nota</button>`:''}
      </div>`;
  });
}

window.excluirNota = async function(pIndex,nIndex){
  if(!confirm('⚠️ Confirma excluir esta nota?')) return;
  if(!confirm('❗ Deseja realmente apagar a nota?')) return;
  const p = pessoas[pIndex];
  const nota = p.notas[nIndex];

  await addDoc(collection(db,'lixeira'),{tipo:'nota',dados:nota,pessoa:p.nome,excluidoPor:usuarioLogado.usuario,data:new Date().toISOString()});
  await addDoc(collection(db,'logs'),{acao:'Excluir nota',alvo:p.nome,usuario:usuarioLogado.usuario,data:new Date().toISOString()});

  p.notas.splice(nIndex,1);
  await updateDoc(doc(db,'pessoas',p.id),{notas:p.notas});
  verNotas(pIndex);
  atualizarGrafico();
}

window.excluirPessoaDireto = async function(id){
  const p = pessoas.find(x=>x.id===id);
  if(!p) return;
  if(!confirm('⚠️ Confirma excluir este perfil?')) return;
  if(!confirm('❗ Essa ação é irreversível. Deseja continuar?')) return;

  await addDoc(collection(db,'lixeira'),{tipo:'pessoa',dados:p,excluidoPor:usuarioLogado.usuario,data:new Date().toISOString()});
  await addDoc(collection(db,'logs'),{acao:'Excluir pessoa',alvo:p.nome,usuario:usuarioLogado.usuario,data:new Date().toISOString()});
  await deleteDoc(doc(db,'pessoas',id));
  carregarPessoas();
}

function renderUsuarios(){
  if(!el.listaUsuarios) return;
  el.listaUsuarios.innerHTML='';
  usuarios.forEach(u=>{
    el.listaUsuarios.innerHTML+=`
      <div class='card'>
        <b>${u.usuario}</b> (${u.nivel}) - ${u.ativo?'Ativo':'Bloqueado'}<br>
        Categoria: ${u.categoria||'-'}<br>
        <input placeholder='Nova senha' id='senha_${u.id}'>
        <button onclick="trocarSenha('${u.id}')">Trocar senha</button>
        <button class='danger' onclick="bloquearUsuario('${u.id}',${u.ativo})">${u.ativo?'Bloquear':'Desbloquear'}</button>
        ${u.usuario!=='CLX'?`<button class='danger' onclick="excluirUsuario('${u.id}')">Excluir usuário</button>`:''}
      </div>`;
  });
}

window.excluirUsuario = async function(id){
  const u = usuarios.find(x=>x.id===id);
  if(!u || u.usuario==='CLX') return;
  if(!confirm('⚠️ Confirma excluir este usuário?')) return;
  if(!confirm('❗ Essa ação é irreversível. Deseja continuar?')) return;

  await addDoc(collection(db,'lixeira'),{tipo:'usuario',dados:u,excluidoPor:usuarioLogado.usuario,data:new Date().toISOString()});
  await addDoc(collection(db,'logs'),{acao:'Excluir usuário',alvo:u.usuario,usuario:usuarioLogado.usuario,data:new Date().toISOString()});
  await deleteDoc(doc(db,'usuarios',id));
  carregarUsuarios();
}

window.trocarSenha = async function(id){
  const nova = document.getElementById('senha_'+id).value;
  if(!nova) return alert('Informe a senha');
  await updateDoc(doc(db,'usuarios',id),{senha:nova});
  alert('Senha alterada');
}

window.bloquearUsuario = async function(id,ativo){
  await updateDoc(doc(db,'usuarios',id),{ativo:!ativo});
  carregarUsuarios();
}

function exportarExcel(){
  const wb = XLSX.utils.book_new();
  const porCategoria = {};

  pessoas.forEach(p=>{
    if(!porCategoria[p.categoria]) porCategoria[p.categoria]=[];
    if(!p.notas || p.notas.length===0){
      porCategoria[p.categoria].push({
        Nome:p.nome,
        Categoria:p.categoria,
        TipoNota:'',
        Nota:'',
        Autor:'',
        Data:''
      });
    }else{
      p.notas.forEach(n=>{
        porCategoria[p.categoria].push({
          Nome:p.nome,
          Categoria:p.categoria,
          TipoNota:n.tipo,
          Nota:n.texto,
          Autor:n.autor,
          Data:n.data
        });
      });
    }
  });

  Object.keys(porCategoria).forEach(cat=>{
    const ws = XLSX.utils.json_to_sheet(porCategoria[cat]);
    XLSX.utils.book_append_sheet(wb, ws, cat.substring(0,31));
  });

  XLSX.writeFile(wb,'informa_cadastros_notas.xlsx');
}

function atualizarGrafico(){
  let e=0,r=0,m=0;
  pessoas.forEach(p=>{
    if(usuarioLogado.nivel!=='admin' && p.categoria!==usuarioLogado.categoria) return;
    p.notas?.forEach(n=>{
      if(n.tipo==='elogio') e++;
      if(n.tipo==='reclamacao') r++;
      if(n.tipo==='melhorar') m++;
    });
  });
  if(chart) chart.destroy();
  chart = new Chart(el.grafico,{type:'pie',data:{labels:['Elogio','Reclamação','A melhorar'],datasets:[{data:[e,r,m]}]}});
}

async function carregarLixeira(){
  if(usuarioLogado.nivel!=='admin') return;
  const s = await getDocs(collection(db,'lixeira'));
  lixeira = [];
  s.forEach(d=>lixeira.push({id:d.id,...d.data()}));
  renderLixeira(lixeira);
}

function renderLixeira(lista){
  el.listaLixeira.innerHTML='';
  lista.forEach(x=>{
    el.listaLixeira.innerHTML+=`
      <div class='card'>
        <b>${x.tipo}</b> — ${x.excluidoPor}<br>
        <small>${x.data}</small><br>
        <button onclick="restaurarItem('${x.id}')">Restaurar</button>
      </div>`;
  });
}

window.restaurarItem = async function(id){
  const x = lixeira.find(i=>i.id===id);
  if(!x) return;
  if(!confirm('Restaurar este item?')) return;

  if(x.tipo==='pessoa'){
    await addDoc(collection(db,'pessoas'),x.dados);
  }
  if(x.tipo==='nota'){
    const s = await getDocs(collection(db,'pessoas'));
    s.forEach(async d=>{
      if(d.data().nome===x.pessoa){
        const notas = d.data().notas||[];
        notas.push(x.dados);
        await updateDoc(doc(db,'pessoas',d.id),{notas});
      }
    });
  }
  if(x.tipo==='usuario'){
    await addDoc(collection(db,'usuarios'),x.dados);
  }

  await deleteDoc(doc(db,'lixeira',id));
  carregarLixeira();
}

function filtrarLixeira(){
  let lista = [...lixeira];
  if(el.filtroLixeiraUsuario.value)
    lista = lista.filter(x=>x.excluidoPor.includes(el.filtroLixeiraUsuario.value));
  if(el.filtroLixeiraData.value)
    lista = lista.filter(x=>x.data.startsWith(el.filtroLixeiraData.value));
  renderLixeira(lista);
}

async function limparLixeira(){
  if(!confirm('Limpar TODA a lixeira?')) return;
  if(!confirm('Essa ação é irreversível. Continuar?')) return;
  const s = await getDocs(collection(db,'lixeira'));
  s.forEach(async d=> await deleteDoc(doc(db,'lixeira',d.id)));
  carregarLixeira();
}

async function carregarLogs(){
  if(usuarioLogado.nivel!=='admin') return;
  const s = await getDocs(collection(db,'logs'));
  el.listaLogs.innerHTML='';
  s.forEach(d=>{
    const x=d.data();
    el.listaLogs.innerHTML+=`<div class='card'>${x.acao} — <b>${x.alvo}</b><br>${x.usuario} • ${x.data}</div>`;
  });
}
</script>
</body>
</html>
