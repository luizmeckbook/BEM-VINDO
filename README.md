<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<title>Medela</title>

<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>

<style>
body{background:#0a0a0a;color:white;text-align:center;font-family:Arial}
.box{background:#1a1a1a;margin:20px;padding:20px;border-radius:10px}
button{background:red;color:white;padding:10px;border:none}
input{padding:10px;margin:5px}
</style>
</head>

<body>

<h2>🛒 MEDELA</h2>

<div class="box">
<input id="cpf" placeholder="CPF">
<input id="email" placeholder="Email">
<input id="senha" type="password" placeholder="Senha"><br>
<button onclick="registrar()">Cadastrar</button>
<button onclick="login()">Entrar</button>
</div>

<div id="app" style="display:none">
<h3>Produtos</h3>
<div id="produtos"></div>
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

// CADASTRO
async function registrar(){
  const user = await auth.createUserWithEmailAndPassword(email.value, senha.value);

  await db.collection("usuarios").doc(user.user.uid).set({
    cpf: cpf.value,
    email: email.value
  });

  alert("Cadastrado!");
}

// LOGIN
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
function carregarProdutos(){
  db.collection("produtos").onSnapshot(snap=>{
    let div = document.getElementById("produtos");
    div.innerHTML="";

    snap.forEach(doc=>{
      let p = doc.data();

      div.innerHTML += `<p>${p.nome} - R$${p.preco}</p>`;
    });
  });
}
</script>

</body>
</html>
