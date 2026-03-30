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

.link{
  color:blue;
  margin-top:10px;
  display:block;
}

.header{
  background:#e53935;
  color:white;
  padding:20px;
}
</style>
</head>

<body>

<!-- LOGIN -->
<div id="login" class="tela login">
  <h2>🛒 Medela</h2>
  <input id="cpfLogin" placeholder="CPF">
  <input id="senhaLogin" type="password" placeholder="Senha">
  <button onclick="entrar()">Entrar</button>
  <span class="link" onclick="ir('cadastro')">Criar conta</span>
</div>

<!-- CADASTRO -->
<div id="cadastro" class="tela login">
  <h2>Cadastrar</h2>
  <input id="cpfCad" placeholder="CPF">
  <input id="senhaCad" type="password" placeholder="Senha">
  <button onclick="cadastrar()">Cadastrar</button>
  <span class="link" onclick="ir('login')">Voltar</span>
</div>

<!-- HOME -->
<div id="home" class="tela">
  <div class="header">
    <h3 id="userNome"></h3>
  </div>

  <div style="padding:20px;">
    <h2>Bem-vindo ao Medela 🛒</h2>
    <button onclick="sair()">Sair</button>
  </div>
</div>

<script>

/* AO ABRIR */
window.onload = function(){
  let logado = localStorage.getItem("logado");

  if(logado){
    document.getElementById("userNome").innerText = "Olá, " + logado;
    ir("home");
  }else{
    ir("login");
  }
};

/* TROCAR TELA */
function ir(tela){
  document.querySelectorAll(".tela").forEach(t=>t.classList.remove("ativa"));
  document.getElementById(tela).classList.add("ativa");
}

/* CADASTRAR */
function cadastrar(){
  let cpf = document.getElementById("cpfCad").value.trim();
  let senha = document.getElementById("senhaCad").value.trim();

  if(cpf=="" || senha==""){
    alert("Preencha tudo!");
    return;
  }

  let usuarios = JSON.parse(localStorage.getItem("usuarios")) || {};

  if(usuarios[cpf]){
    alert("CPF já cadastrado!");
    return;
  }

  usuarios[cpf] = senha;

  localStorage.setItem("usuarios", JSON.stringify(usuarios));

  alert("Cadastro realizado!");
  ir("login");
}

/* LOGIN REAL */
function entrar(){
  let cpf = document.getElementById("cpfLogin").value.trim();
  let senha = document.getElementById("senhaLogin").value.trim();

  let usuarios = JSON.parse(localStorage.getItem("usuarios")) || {};

  if(!usuarios[cpf]){
    alert("CPF não cadastrado!");
    return;
  }

  if(usuarios[cpf] !== senha){
    alert("Senha incorreta!");
    return;
  }

  localStorage.setItem("logado", cpf);

  document.getElementById("userNome").innerText = "Olá, " + cpf;

  ir("home");
}

/* SAIR */
function sair(){
  localStorage.removeItem("logado");
  location.reload();
}

</script>

</body>
</html>
