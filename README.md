<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title id="siteTitle">Medela Supermercado</title>

<style id="dynamicStyles">
:root { --primary: #e53935; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }
</style>

<style>
body { margin:0; font-family: 'Segoe UI', Arial, sans-serif; background: var(--bg); overflow-x: hidden; }
.tela { display:none; min-height: 100vh; width: 100%; }
.ativa { display:block; }
.container { padding: 15px; max-width: 600px; margin: auto; }
.card { background: white; padding: 15px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); margin-bottom: 15px; }

input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
button { border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; color: white; width: 100%; }
.btn-main { background: var(--primary); }
.btn-support { background: var(--whatsapp); margin-top: 10px; display: flex; align-items: center; justify-content: center; gap: 8px; }

.header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; }

.chat-screen { display: flex; flex-direction: column; height: 100vh; background: #e5ddd5; }
.chat-messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }
.msg { max-width: 80%; padding: 10px; border-radius: 10px; font-size: 14px; }
.msg.sent { align-self: flex-end; background: #dcf8c6; }
.msg.received { align-self: flex-start; background: white; }

.chat-footer { padding: 10px; background: #f0f0f0; display: flex; gap: 5px; }

.cat-nav { display: flex; gap: 10px; overflow-x: auto; padding: 10px 0; }
.cat-btn { background: white; border: 1px solid #ddd; padding: 8px 15px; border-radius: 20px; color:#333; }
.grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
</style>
</head>

<body>

<div id="login" class="tela ativa">
<div class="container" style="text-align:center; padding-top:60px;">
<h1 id="brandName">🛒 Medela</h1>
<div class="card">
<input id="lCpf" placeholder="CPF ou 'admin'">
<input id="lSenha" type="password" placeholder="Senha">
<button class="btn-main" onclick="entrar()">Entrar</button>
<button class="btn-support" onclick="abrirSuporteVisitante()">💬 Suporte</button>
<p onclick="ir('cadastro')" style="color:var(--primary); cursor:pointer;">Criar Conta</p>
</div>
</div>
</div>

<div id="cadastro" class="tela">
<div class="container">
<h2>Criar Conta</h2>
<input id="cNome">
<input id="cCpf">
<input id="cSenha">
<button onclick="registrar()" class="btn-main">Cadastrar</button>
</div>
</div>

<div id="home" class="tela">
<div class="header">
<span id="welcome"></span>
<button onclick="voltarParaLogin()">Sair</button>
</div>

<div class="container">
<div id="catNav"></div>
<div id="listaLoja" class="grid"></div>
</div>

<button onclick="ir('chat')" style="position:fixed;bottom:20px;right:20px;width:60px;height:60px;border-radius:50%;background:var(--whatsapp)">💬</button>

<button onclick="verCarrinho()" style="position:fixed;bottom:90px;right:20px;width:60px;height:60px;border-radius:50%;background:black">🛒</button>
</div>

<div id="admin" class="tela">
<div class="header" style="background:#2c3e50">
<span>Admin</span>
<button onclick="voltarParaLogin()">Sair</button>
</div>

<div class="container">

<input id="buscaProduto" placeholder="Buscar..." onkeyup="buscarProduto()">

<div id="lista_prod_admin"></div>

<div class="card">
<input id="pNome">
<input id="pPreco">
<select id="pCat">
<option>Açougue</option>
<option>Bebida</option>
</select>
<button onclick="addProduto()">Adicionar</button>
</div>

</div>
</div>

<script>

let usuarios = JSON.parse(localStorage.getItem("m_users")) || {};
let produtos = JSON.parse(localStorage.getItem("m_prod")) || [];
let carrinho = JSON.parse(localStorage.getItem("m_cart")) || [];

function entrar(){
let cpf = lCpf.value;
let senha = lSenha.value;

if(cpf==="SUPER-MEDELA#" && senha==="MEDELA1053@"){ ir('admin'); return;}

if(usuarios[cpf] && usuarios[cpf].senha===senha){
sessaoAtiva=cpf;
ir('home');
}else alert("Erro");
}

function registrar(){
usuarios[cCpf.value]={nome:cNome.value, senha:cSenha.value};
localStorage.setItem("m_users",JSON.stringify(usuarios));
ir('login');
}

function renderizarFiltrado(){
listaLoja.innerHTML = produtos.map((p,i)=>`
<div class="card">
${p.nome}<br>R$ ${p.preco}
<button onclick="addCarrinho(${i})">Adicionar</button>
</div>`).join('');
}

function addCarrinho(i){
carrinho.push(produtos[i]);
localStorage.setItem("m_cart",JSON.stringify(carrinho));
alert("Adicionado");
}

function verCarrinho(){
alert(carrinho.map(p=>p.nome+" R$"+p.preco).join("\n"));
}

function removerProduto(i){
produtos.splice(i,1);
localStorage.setItem("m_prod",JSON.stringify(produtos));
renderAdmin();
}

function buscarProduto(){
let t = buscaProduto.value.toLowerCase();
lista_prod_admin.innerHTML = produtos
.map((p,i)=>({p,i}))
.filter(x=>x.p.nome.toLowerCase().includes(t))
.map(x=>`${x.p.nome} <button onclick="removerProduto(${x.i})">X</button>`).join('');
}

function renderAdmin(){
lista_prod_admin.innerHTML = produtos.map((p,i)=>`
${p.nome} <button onclick="removerProduto(${i})">X</button><br>
`).join('');
}

function addProduto(){
produtos.push({nome:pNome.value, preco:pPreco.value});
localStorage.setItem("m_prod",JSON.stringify(produtos));
renderAdmin();
}

function ir(t){
document.querySelectorAll('.tela').forEach(x=>x.classList.remove('ativa'));
document.getElementById(t).classList.add('ativa');
if(t==='home') renderizarFiltrado();
if(t==='admin') renderAdmin();
}

</script>

</body>
</html>
