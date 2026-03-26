<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Medela Supermercado</title>

<style>
body{
  margin:0;
  font-family:Arial;
  background:#f5f5f5;
}

/* TELAS */
.tela{ display:none; }
.ativa{ display:block; }

/* LOGIN */
.login{
  text-align:center;
  padding:40px;
}

input{
  width:90%;
  padding:10px;
  margin:10px;
  border-radius:6px;
  border:1px solid #ccc;
}

button{
  background:#e53935;
  color:white;
  border:none;
  padding:10px;
  border-radius:6px;
  width:90%;
}

/* HEADER */
.header{
  background:#e53935;
  color:white;
  padding:20px;
}

/* PRODUTOS */
.produtos{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:10px;
  padding:10px;
}

.produto{
  background:white;
  padding:10px;
  border-radius:10px;
  text-align:center;
}

.produto img{
  width:100%;
  height:120px;
  object-fit:cover;
}

/* CARRINHO */
.carrinho{
  padding:10px;
}

.item{
  background:white;
  margin:5px;
  padding:10px;
  border-radius:8px;
}

/* NAV */
.nav{
  position:fixed;
  bottom:0;
  width:100%;
  background:white;
  display:flex;
  justify-content:space-around;
  padding:10px;
  border-top:1px solid #ccc;
}
</style>
</head>

<body>

<!-- LOGIN -->
<div id="login" class="tela ativa login">
  <h2>🛒 Medela</h2>
  <input id="cpf" placeholder="CPF">
  <input id="senha" type="password" placeholder="Senha">
  <button onclick="entrar()">Entrar</button>
</div>

<!-- HOME -->
<div id="home" class="tela">
  <div class="header">
    <h3>Bem-vindo 👋</h3>
  </div>

  <div class="produtos" id="produtos"></div>

  <div class="nav">
    <div onclick="ir('home')">🏠</div>
    <div onclick="ir('carrinho')">🛒</div>
    <div onclick="sair()">🚪</div>
  </div>
</div>

<!-- CARRINHO -->
<div id="carrinho" class="tela carrinho">
  <h3>🛒 Carrinho</h3>
  <div id="lista"></div>
  <h2 id="total"></h2>

  <button onclick="finalizar()">Finalizar Pedido</button>

  <div class="nav">
    <div onclick="ir('home')">🏠</div>
    <div onclick="ir('carrinho')">🛒</div>
    <div onclick="sair()">🚪</div>
  </div>
</div>

<script>
let produtos = [
  {nome:"Carne", preco:30, img:"https://i.imgur.com/7yUvePI.png"},
  {nome:"Frango", preco:12, img:"https://i.imgur.com/2nCt3Sbl.jpg"},
  {nome:"Arroz", preco:20, img:"https://i.imgur.com/Mq7FZ5M.png"},
  {nome:"Refrigerante", preco:8, img:"https://i.imgur.com/Y4R1b7N.png"}
];

let carrinho = JSON.parse(localStorage.getItem("carrinho")) || [];

/* LOGIN */
function entrar(){
  let cpf = document.getElementById("cpf").value;
  let senha = document.getElementById("senha").value;

  if(cpf && senha){
    localStorage.setItem("user", cpf);
    ir("home");
  }else{
    alert("Preencha tudo");
  }
}

/* TROCAR TELA */
function ir(tela){
  document.querySelectorAll(".tela").forEach(t=>t.classList.remove("ativa"));
  document.getElementById(tela).classList.add("ativa");

  if(tela=="home") carregarProdutos();
  if(tela=="carrinho") carregarCarrinho();
}

/* PRODUTOS */
function carregarProdutos(){
  let div = document.getElementById("produtos");
  div.innerHTML="";

  produtos.forEach((p,i)=>{
    div.innerHTML += `
      <div class="produto">
        <img src="${p.img}">
        <p>${p.nome}</p>
        <b>R$ ${p.preco}</b>
        <button onclick="add(${i})">Comprar</button>
      </div>
    `;
  });
}

/* ADD CARRINHO */
function add(i){
  carrinho.push(produtos[i]);
  localStorage.setItem("carrinho", JSON.stringify(carrinho));
  alert("Adicionado!");
}

/* CARRINHO */
function carregarCarrinho(){
  let div = document.getElementById("lista");
  let total = 0;
  div.innerHTML="";

  carrinho.forEach((p,i)=>{
    total += p.preco;

    div.innerHTML += `
      <div class="item">
        ${p.nome} - R$ ${p.preco}
        <button onclick="remover(${i})">❌</button>
      </div>
    `;
  });

  document.getElementById("total").innerText = "Total: R$ " + total;
}

/* REMOVER */
function remover(i){
  carrinho.splice(i,1);
  localStorage.setItem("carrinho", JSON.stringify(carrinho));
  carregarCarrinho();
}

/* FINALIZAR */
function finalizar(){
  alert("Pedido enviado 🚚");
  carrinho = [];
  localStorage.removeItem("carrinho");
  ir("home");
}

/* SAIR */
function sair(){
  localStorage.removeItem("user");
  location.reload();
}
</script>

</body>
</html>
