<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Login - Medela</title>

<style>
body { font-family: Arial; background: #f5f5f5; }
.box {
  max-width: 300px;
  margin: 100px auto;
  background: white;
  padding: 20px;
  border-radius: 10px;
}
input, button {
  width: 100%;
  padding: 10px;
  margin-top: 10px;
}
button {
  background: #b30000;
  color: white;
  border: none;
}
</style>
</head>
<body>

<div class="box">
<h2>Login</h2>

<input id="cpf" placeholder="CPF">
<input id="senha" type="password" placeholder="Senha">
<button onclick="login()">Entrar</button>

</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import {
  getFirestore,
  collection,
  query,
  where,
  getDocs
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "SUA_KEY",
  authDomain: "SEU_DOMINIO",
  projectId: "SEU_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

window.login = async () => {
  const cpf = document.getElementById("cpf").value;
  const senha = document.getElementById("senha").value;

  const q = query(
    collection(db, "usuarios"),
    where("cpf", "==", cpf),
    where("senha", "==", senha)
  );

  const res = await getDocs(q);

  if (res.empty) {
    alert("Login inválido");
  } else {
    localStorage.setItem("cpf", cpf);
    window.location.href = "index.html";
  }
};
</script>

</body>
</html>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Açougue Medela</title>

<style>
body { font-family: Arial; margin: 0; background: #f5f5f5; }

header {
  background: #b30000;
  color: white;
  padding: 15px;
}

.container { padding: 15px; }

.produto {
  background: white;
  margin: 10px 0;
  padding: 15px;
  border-radius: 10px;
}

button {
  background: #b30000;
  color: white;
  padding: 10px;
  border: none;
}

#chatBox {
  background: #e5ddd5;
  height: 300px;
  overflow-y: auto;
  padding: 10px;
}

.msg { padding: 10px; margin: 5px; }

.cliente { background: #dcf8c6; text-align: right; }

.admin { background: white; }
</style>

</head>
<body>

<header>🥩 Açougue Medela</header>

<div class="container">

<button onclick="sair()">Sair</button>

<h2>Produtos</h2>
<div id="produtos"></div>

<h2>Carrinho</h2>
<div id="carrinho"></div>
<p id="total"></p>
<button onclick="finalizar()">Finalizar Pedido</button>

<h2>Status</h2>
<p id="status"></p>

<h2>Chat</h2>
<div id="chatBox"></div>
<input id="msg" placeholder="Mensagem">
<button onclick="enviarMensagem()">Enviar</button>

</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import {
  getFirestore,
  collection,
  getDocs,
  addDoc,
  onSnapshot,
  query,
  where,
  orderBy
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "SUA_KEY",
  authDomain: "SEU_DOMINIO",
  projectId: "SEU_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

// 🔐 PROTEÇÃO
const cpf = localStorage.getItem("cpf");
if (!cpf) window.location.href = "login.html";

window.sair = () => {
  localStorage.removeItem("cpf");
  window.location.href = "login.html";
};

// 🛒 CARRINHO
let carrinho = [];

window.add = (nome, preco) => {
  carrinho.push({ nome, preco });
  render();
};

function render() {
  let total = 0;
  let html = "";

  carrinho.forEach(p => {
    total += p.preco;
    html += `<p>${p.nome} - R$${p.preco}</p>`;
  });

  document.getElementById("carrinho").innerHTML = html;
  document.getElementById("total").innerText = "Total: R$" + total;
}

// 📦 PRODUTOS
async function carregarProdutos() {
  const snap = await getDocs(collection(db, "produtos"));
  let html = "";

  snap.forEach(doc => {
    let p = doc.data();
    html += `
      <div class="produto">
        ${p.nome} - R$${p.preco}
        <button onclick="add('${p.nome}', ${p.preco})">+</button>
      </div>
    `;
  });

  document.getElementById("produtos").innerHTML = html;
}

// 🧾 PEDIDO
window.finalizar = async () => {
  await addDoc(collection(db, "pedidos"), {
    cpf,
    carrinho,
    status: "Em preparo"
  });

  alert("Pedido feito!");
};

// 📡 STATUS
onSnapshot(collection(db, "pedidos"), snap => {
  snap.forEach(doc => {
    let d = doc.data();
    if (d.cpf === cpf) {
      document.getElementById("status").innerText = d.status;
    }
  });
});

// 💬 CHAT
window.enviarMensagem = async () => {
  const texto = document.getElementById("msg").value;

  if (!texto) return;

  await addDoc(collection(db, "chats"), {
    cpf,
    mensagem: texto,
    tipo: "cliente",
    data: new Date()
  });

  document.getElementById("msg").value = "";
};

const q = query(
  collection(db, "chats"),
  where("cpf", "==", cpf),
  orderBy("data")
);

onSnapshot(q, snap => {
  let html = "";

  snap.forEach(doc => {
    let m = doc.data();

    if (m.tipo === "cliente") {
      html += `<div class="msg cliente">${m.mensagem}</div>`;
    } else {
      html += `<div class="msg admin">${m.mensagem}</div>`;
    }
  });

  const box = document.getElementById("chatBox");
  box.innerHTML = html;
  box.scrollTop = box.scrollHeight;
});

// START
carregarProdutos();
</script>

</body>
</html>
