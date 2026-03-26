<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Medela Supermercado</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>

<style>
body{background:#0a0a0a;color:white;font-family:Arial;text-align:center}
header{background:#b30000;padding:15px;font-size:22px}
.box{background:#1a1a1a;margin:20px;padding:20px;border-radius:10px}
button{background:#b30000;color:white;padding:8px;border:none;border-radius:6px;margin:5px}
input{padding:8px;margin:5px;border-radius:6px;border:none}
.admin{border:2px solid red}
</style>
</head>

<body>

<header>🛒 MEDELA SUPERMERCADO</header>

<!-- LOGIN -->
<div class="box">
<h3>Cadastro / Login</h3>
<input id="cpf" placeholder="CPF">
<input id="email" placeholder="Email (cadastro)">
<input id="senha" type="password" placeholder="Senha"><br>
<button onclick="registrar()">Cadastrar</button>
<button onclick="loginCPF()">Entrar</button>
</div>

<div id="app" style="display:none">

<!-- CLIENTE -->
<div class="box">
<h3>Produtos</h3>
<div id="produtos"></div>
</div>

<div class="box">
<h3>Carrinho</h3>
<div id="carrinho"></div>
<p>Total: R$ <span id="total">0</span></p>
<button onclick="finalizar()">Finalizar Pedido</button>
</div>

<!-- ADMIN -->
<div id="adminPanel" class="box admin" style="display:none">
<h3>PAINEL ADMIN</h3>

<input id="nomeProd" placeholder="Nome produto">
<input id="precoProd" placeholder="Preço">
<button onclick="addProduto()">Adicionar Produto</button>

<h4>Pedidos</h4>
<div id="pedidos"></div>

</div>

</div>

<script>
// CONFIG
const firebaseConfig = {
  apiKey: "COLE_AQUI",
  authDomain: "COLE_AQUI",
  projectId: "COLE_AQUI"
};

firebase.initializeApp(firebaseConfig);
const auth = firebase.auth();
const db = firebase.firestore();

let carrinho = [];
let usuarioAtual = null;
let isAdmin = false;

// CADASTRO
async function registrar(){
  const check = await db.collection("usuarios").where("cpf","==",cpf.value).get();
  if(!check.empty) return alert("CPF já cadastrado");

  const user = await auth.createUserWithEmailAndPassword(email.value, senha.value);

  await db.collection("usuarios").doc(user.user.uid).set({
    cpf: cpf.value,
    email: email.value,
    tipo: "cliente"
  });

  alert("Cadastro feito!");
}

// LOGIN CPF
async function loginCPF(){
  const snap = await db.collection("usuarios").where("cpf","==",cpf.value).get();

  if(snap.empty) return alert("CPF não encontrado");

  let userData = snap.docs[0].data();

  await auth.signInWithEmailAndPassword(userData.email, senha.value);
}

// USUÁRIO
auth.onAuthStateChanged(async user=>{
  if(user){
    usuarioAtual = user;

    const doc = await db.collection("usuarios").doc(user.uid).get();
    isAdmin = doc.data().tipo === "admin";

    document.getElementById("app").style.display="block";

    if(isAdmin){
      document.getElementById("adminPanel").style.display="block";
      carregarPedidos();
    }

    carregarProdutos();

    document.body.innerHTML += `<button onclick="logout()">Sair</button>`;
  }
});

// LOGOUT
function logout(){
  auth.signOut();
  location.reload();
}

// PRODUTOS
function addProduto(){
  if(!isAdmin) return alert("Apenas admin");

  db.collection("produtos").add({
    nome: nomeProd.value,
    preco: Number(precoProd.value)
  });
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
  if(carrinho.length === 0) return alert("Carrinho vazio");

  db.collection("pedidos").add({
    userId: usuarioAtual.uid,
    itens: carrinho,
    total: carrinho.reduce((t,i)=>t+i.preco,0),
    status: "Pendente",
    criadoEm: new Date()
  });

  alert("Pedido feito!");
  carrinho=[];
  atualizarCarrinho();
}

// ADMIN - PEDIDOS
function carregarPedidos(){
  db.collection("pedidos").onSnapshot(snap=>{
    let div = document.getElementById("pedidos");
    div.innerHTML="";

    snap.forEach(doc=>{
      let p = doc.data();

      div.innerHTML += `
        <div style="border:1px solid gray;margin:10px;padding:10px">
        <p>Total: R$${p.total}</p>
        <p>Status: ${p.status}</p>

        <button onclick="mudarStatus('${doc.id}','Preparando')">Preparando</button>
        <button onclick="mudarStatus('${doc.id}','Saiu')">Saiu</button>
        <button onclick="mudarStatus('${doc.id}','Entregue')">Entregue</button>
        </div>
      `;
    });
  });
}

function mudarStatus(id,status){
  db.collection("pedidos").doc(id).update({
    status: status
  });
}
</script>

</body>
</html>
