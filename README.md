<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Medela Sistema Master</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>

<style>
body{background:#0a0a0a;color:white;font-family:Arial;text-align:center}
header{background:#b30000;padding:15px;font-size:22px}
.box{background:#1a1a1a;margin:20px;padding:20px;border-radius:10px}
button{background:#b30000;color:white;padding:8px;border:none;border-radius:6px}
input{padding:8px;margin:5px;border-radius:6px;border:none}
</style>
</head>

<body>

<header>🛒 MEDELA MASTER</header>

<div class="box">
<h3>Login</h3>
<input id="email" placeholder="Email">
<input id="senha" type="password" placeholder="Senha"><br>
<button onclick="registrar()">Registrar</button>
<button onclick="login()">Entrar</button>
</div>

<div id="app" style="display:none">

<!-- ADMIN -->
<div class="box">
<h3>Admin - Produtos</h3>
<input id="nomeProd" placeholder="Nome">
<input id="precoProd" placeholder="Preço">
<button onclick="addProduto()">Adicionar</button>
</div>

<!-- PRODUTOS -->
<div class="box">
<h3>Produtos</h3>
<div id="produtos"></div>
</div>

<!-- CARRINHO -->
<div class="box">
<h3>Carrinho</h3>
<div id="carrinho"></div>
<p>Total: R$ <span id="total">0</span></p>
<button onclick="finalizar()">Finalizar Pedido</button>
</div>

</div>

<script>
// 🔥 COLE SUA CONFIG AQUI
const firebaseConfig = {
  apiKey: "COLE_AQUI",
  authDomain: "COLE_AQUI",
  projectId: "COLE_AQUI"
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();

let carrinho = [];

// LOGIN
function registrar(){
  auth.createUserWithEmailAndPassword(email.value, senha.value);
}

function login(){
  auth.signInWithEmailAndPassword(email.value, senha.value);
}

// USUÁRIO
auth.onAuthStateChanged(user=>{
  if(user){
    document.getElementById("app").style.display="block";
    carregarProdutos();
  }
});

// PRODUTOS
function addProduto(){
  db.collection("produtos").add({
    nome: nomeProd.value,
    preco: Number(precoProd.value)
  });
}

function deletarProduto(id){
  db.collection("produtos").doc(id).delete();
}

function carregarProdutos(){
  db.collection("produtos").onSnapshot(snap=>{
    let div = document.getElementById("produtos");
    div.innerHTML="";

    snap.forEach(doc=>{
      let p = doc.data();

      div.innerHTML += `
        <p>${p.nome} - R$${p.preco}
        <button onclick="addCarrinho('${p.nome}',${p.preco})">+</button>
        <button onclick="deletarProduto('${doc.id}')">X</button>
        </p>
      `;
    });
  });
}

// CARRINHO
function addCarrinho(nome,preco){
  carrinho.push({nome,preco});
  atualizarCarrinho();
}

function atualizarCarrinho(){
  let div = document.getElementById("carrinho");
  div.innerHTML="";
  let total=0;

  carrinho.forEach((i,index)=>{
    total+=i.preco;

    div.innerHTML += `
      ${i.nome} - R$${i.preco}
      <button onclick="remover(${index})">X</button><br>
    `;
  });

  document.getElementById("total").innerText=total;
}

function remover(i){
  carrinho.splice(i,1);
  atualizarCarrinho();
}

// PEDIDO
function finalizar(){
  db.collection("pedidos").add({
    itens: carrinho,
    total: carrinho.reduce((t,i)=>t+i.preco,0),
    data: new Date()
  });

  alert("Pedido enviado!");
  carrinho=[];
  atualizarCarrinho();
}
</script>

</body>
</html>
