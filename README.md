<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Medela Supermercado</title>

<style>
body{ margin:0; font-family:Arial; background:#f5f5f5; }
.tela{ display:none; }
.ativa{ display:block; }

.login{ text-align:center; padding:40px; }

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

.header{
  background:#e53935;
  color:white;
  padding:20px;
}

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
<div id="login" class="tela login">
  <h2>🛒 Medela</h2>
  <input id="cpf" placeholder="CPF">
  <input id="senha" type="password" placeholder="Senha">
  <button onclick="entrar()">Entrar</button>
</div>

<!-- HOME -->
<div id="home" class="tela">
  <div class="header">
    <h3 id="userNome">Bem-vindo 👋</h3>
  </div>

  <div class="produtos" id="produtos"></div>

  <div class="nav">
    <div onclick="ir('home')">🏠</div>
    <div onclick="sair()">🚪</div>
  </div>
</div>

<script>
let produtos = [
  {nome:"Carne", preco:30, img:"https://i.imgur.com/7yUvePI.png"},
  {nome:"Frango", preco:12, img:"https://i.imgur.com/2nCt3Sbl.jpg"}
];

/* VERIFICAR LOGIN AO ABRIR */
window.onload = function(){
  let user = localStorage.getItem("user");

  if(user){
    ir("home");
    document.getElementById("userNome").innerText = "Olá, " + user;
  }else{
    ir("login");
  }
};

/* LOGIN */
function entrar(){
  let cpf = document.getElementById("cpf").value.trim();
  let senha = document.getElementById("senha").value.trim();

  if(cpf === "" || senha === ""){
    alert("Preencha CPF e senha!");
    return;
  }

  localStorage.setItem("user", cpf);

  document.getElementById("userNome").innerText = "Olá, " + cpf;

  ir("home");
}

/* TROCAR TELA */
function ir(tela){
  document.querySelectorAll(".tela").forEach(t=>t.classList.remove("ativa"));
  document.getElementById(tela).classList.add("ativa");

  if(tela=="home") carregarProdutos();
}

/* PRODUTOS */
function carregarProdutos(){
  let div = document.getElementById("produtos");
  div.innerHTML="";

  produtos.forEach(p=>{
    div.innerHTML += `
      <div class="produto">
        <img src="${p.img}">
        <p>${p.nome}</p>
        <b>R$ ${p.preco}</b>
      </div>
    `;
  });
}

/* SAIR */
function sair(){
  localStorage.removeItem("user");
  location.reload();
}
</script>

</body>
</html>
