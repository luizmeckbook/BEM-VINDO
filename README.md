<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Medela Supermercado Pro</title>
<style>
:root { --primary: #e53935; --bg: #f5f5f5; --white: #ffffff; --admin: #2c3e50; }
body { margin:0; font-family: 'Segoe UI', Arial; background: var(--bg); color: #333; }
.tela { display:none; min-height: 100vh; }
.ativa { display:block; }

/* UI Elements */
input, select { width:90%; padding:12px; margin:10px 0; border-radius:8px; border:1px solid #ddd; box-sizing: border-box; }
button { background: var(--primary); color:white; border:none; padding:12px; border-radius:8px; width:90%; cursor:pointer; font-weight:bold; }
button.secondary { background: #555; margin-top: 5px; }
.header { background: var(--primary); color:white; padding:15px; display:flex; justify-content: space-between; align-items: center; position: sticky; top:0; z-index: 10; }

/* Grid de Produtos */
.container { padding: 15px; margin-bottom: 80px; }
.produtos-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 15px; }
.card { background: white; padding: 10px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); text-align: center; }

/* Carrinho Estilo Modal */
#carrinho-modal { position: fixed; bottom: 0; background: white; width: 100%; height: 70%; border-top-left-radius: 20px; border-top-right-radius: 20px; box-shadow: 0 -5px 15px rgba(0,0,0,0.2); z-index: 100; transform: translateY(100%); transition: 0.3s; padding: 20px; box-sizing: border-box; overflow-y: auto; }
#carrinho-modal.aberto { transform: translateY(0); }
.item-carrinho { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #eee; padding: 10px 0; }
.btn-remove { background: #ff5252; width: auto; padding: 5px 10px; font-size: 12px; }

/* Floating Buttons */
.float-btn { position:fixed; width:60px; height:60px; border-radius:50%; font-size:24px; box-shadow: 0 4px 10px rgba(0,0,0,0.3); z-index: 50; }
#btn-carrinho { bottom:20px; right:20px; background: #2ecc71; }
#btn-chat { bottom:90px; right:20px; background: #3498db; }
.badge { position: absolute; top: -5px; right: -5px; background: white; color: black; font-size: 12px; padding: 2px 6px; border-radius: 50%; font-weight: bold; }
</style>
</head>
<body>

<div id="login" class="tela ativa">
  <div style="text-align:center; padding:50px 20px;">
    <h2>🛒 Medela</h2>
    <input id="cpfLogin" placeholder="CPF (admin para painel)">
    <input id="senhaLogin" type="password" placeholder="Senha">
    <button onclick="entrar()">Entrar</button>
  </div>
</div>

<div id="home" class="tela">
  <div class="header">
    <span id="userNome">Olá</span>
    <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
  </div>
  
  <div class="container">
    <h3 style="margin-bottom:5px;">Nossos Produtos</h3>
    <div class="produtos-grid" id="listaProdutos"></div>
  </div>

  <button id="btn-chat" class="float-btn" onclick="alert('Abrindo Chat...')">💬</button>
  <button id="btn-carrinho" class="float-btn" onclick="toggleCarrinho()">
    🛒 <span class="badge" id="carrinhoCount">0</span>
  </button>
</div>

<div id="carrinho-modal">
  <div style="display:flex; justify-content: space-between; align-items: center;">
    <h2>Seu Carrinho</h2>
    <button style="width:auto; background:none; color:black; font-size:24px" onclick="toggleCarrinho()">✕</button>
  </div>
  <div id="itensCarrinho">
    </div>
  <div style="margin-top:20px; border-top: 2px solid #eee; padding-top:10px;">
    <h3>Total: R$ <span id="totalCarrinho">0.00</span></h3>
    <button onclick="alert('Pedido finalizado!')" style="background:#2ecc71">Finalizar Compra</button>
  </div>
</div>

<script>
let produtosLoja = JSON.parse(localStorage.getItem("produtos")) || [
  {id: 1, nome: "Maçã Gala", preco: 5.90},
  {id: 2, nome: "Coca-Cola 2L", preco: 8.50},
  {id: 3, nome: "Pão de Forma", preco: 6.30}
];

let carrinho = [];

function entrar() {
  let cpf = document.getElementById("cpfLogin").value;
  localStorage.setItem("logado", cpf);
  document.getElementById("userNome").innerText = "Olá, " + cpf;
  renderizarProdutos();
  ir("home");
}

function ir(tela) {
  document.querySelectorAll(".tela").forEach(t => t.classList.remove("ativa"));
  document.getElementById(tela).classList.add("ativa");
}

function renderizarProdutos() {
  const grid = document.getElementById("listaProdutos");
  grid.innerHTML = produtosLoja.map(p => `
    <div class="card">
      <div style="font-size:30px">📦</div>
      <strong>${p.nome}</strong><br>
      <span style="color:green">R$ ${p.preco.toFixed(2)}</span>
      <button onclick="addAoCarrinho(${p.id})" style="margin-top:8px; background:#2ecc71">Adicionar</button>
    </div>
  `).join('');
}

/* LÓGICA DO CARRINHO */
function addAoCarrinho(id) {
  const produto = produtosLoja.find(p => p.id === id);
  carrinho.push(produto);
  atualizarCarrinhoUI();
}

function removerDoCarrinho(index) {
  carrinho.splice(index, 1);
  atualizarCarrinhoUI();
}

function atualizarCarrinhoUI() {
  // Atualiza Contador
  document.getElementById("carrinhoCount").innerText = carrinho.length;
  
  // Atualiza Lista
  const lista = document.getElementById("itensCarrinho");
  if(carrinho.length === 0) {
    lista.innerHTML = "<p>Seu carrinho está vazio.</p>";
  } else {
    lista.innerHTML = carrinho.map((item, index) => `
      <div class="item-carrinho">
        <div>
          <strong>${item.nome}</strong><br>
          <small>R$ ${item.preco.toFixed(2)}</small>
        </div>
        <button class="btn-remove" onclick="removerDoCarrinho(${index})">Remover</button>
      </div>
    `).join('');
  }

  // Atualiza Total
  const total = carrinho.reduce((sum, item) => sum + item.preco, 0);
  document.getElementById("totalCarrinho").innerText = total.toFixed(2);
}

function toggleCarrinho() {
  document.getElementById("carrinho-modal").classList.toggle("aberto");
}

function sair() {
  localStorage.removeItem("logado");
  location.reload();
}

// Início
window.onload = () => {
  if(localStorage.getItem("logado")) entrar();
};
</script>
</body>
</html>
