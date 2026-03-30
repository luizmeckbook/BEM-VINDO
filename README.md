<!DOCTYPE html>
<html lang="pt-br">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Medela Supermercado - Seguro</title>
<style>
    :root { --primary: #e53935; --bg: #f5f5f5; --white: #ffffff; --green: #2ecc71; }
    body { margin:0; font-family: 'Segoe UI', Arial; background: var(--bg); }
    .tela { display:none; min-height: 100vh; }
    .ativa { display:block; }

    /* Forms */
    .login-container { text-align:center; padding:50px 20px; max-width: 400px; margin: auto; }
    input { width:100%; padding:12px; margin:10px 0; border-radius:8px; border:1px solid #ddd; box-sizing: border-box; }
    button { background: var(--primary); color:white; border:none; padding:12px; border-radius:8px; width:100%; cursor:pointer; font-weight:bold; }
    .link { color: #e53935; text-decoration: underline; cursor: pointer; display: block; margin-top: 15px; }

    /* Header e Loja */
    .header { background: var(--primary); color:white; padding:15px; display:flex; justify-content: space-between; align-items: center; }
    .container { padding: 15px; margin-bottom: 80px; }
    .produtos-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
    .card { background: white; padding: 10px; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); text-align: center; }

    /* Carrinho */
    #carrinho-modal { position: fixed; bottom: 0; background: white; width: 100%; height: 70%; border-top-left-radius: 20px; border-top-right-radius: 20px; box-shadow: 0 -5px 15px rgba(0,0,0,0.2); z-index: 100; transform: translateY(100%); transition: 0.3s; padding: 20px; box-sizing: border-box; overflow-y: auto; }
    #carrinho-modal.aberto { transform: translateY(0); }
    .item-carrinho { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid #eee; padding: 10px 0; }
    
    .badge { background: white; color: var(--primary); padding: 2px 6px; border-radius: 50%; font-size: 12px; margin-left: 5px; }
</style>
</head>
<body>

<div id="login" class="tela ativa">
    <div class="login-container">
        <h2>🛒 Medela Login</h2>
        <input id="cpfLogin" placeholder="Seu CPF">
        <input id="senhaLogin" type="password" placeholder="Sua Senha">
        <button onclick="entrar()">Entrar</button>
        <span class="link" onclick="ir('cadastro')">Não tem conta? Cadastre-se</span>
    </div>
</div>

<div id="cadastro" class="tela">
    <div class="login-container">
        <h2>Criar Conta Única</h2>
        <p>Use seu CPF como identificador único.</p>
        <input id="cpfCad" placeholder="Digite seu CPF">
        <input id="senhaCad" type="password" placeholder="Crie uma senha forte">
        <button onclick="cadastrar()">Finalizar Cadastro</button>
        <span class="link" onclick="ir('login')">Já tenho conta</span>
    </div>
</div>

<div id="home" class="tela">
    <div class="header">
        <span id="userNome">Olá</span>
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <h3>Produtos</h3>
        <div class="produtos-grid" id="listaProdutos"></div>
    </div>
    
    <button onclick="toggleCarrinho()" style="position:fixed; bottom:20px; right:20px; width:70px; height:70px; border-radius:50%; background:var(--green); font-size:24px; box-shadow: 0 4px 10px rgba(0,0,0,0.3);">
        🛒<span id="cartCount" class="badge">0</span>
    </button>
</div>

<div id="carrinho-modal">
    <div style="display:flex; justify-content: space-between; align-items: center;">
        <h2>Carrinho</h2>
        <button style="width:auto; background:none; color:black; font-size:24px" onclick="toggleCarrinho()">✕</button>
    </div>
    <div id="itensCarrinho"></div>
    <div style="margin-top:20px; border-top: 2px solid #eee; padding-top:10px;">
        <h3>Total: R$ <span id="totalCarrinho">0.00</span></h3>
        <button onclick="finalizarCompra()" style="background:var(--green)">Enviar para WhatsApp</button>
    </div>
</div>

<script>
// --- BANCO DE DADOS LOCAL ---
let produtos = [
    {id: 1, nome: "Arroz 5kg", preco: 25.90},
    {id: 2, nome: "Feijão Preto", preco: 7.50},
    {id: 3, nome: "Óleo de Soja", preco: 6.80},
    {id: 4, nome: "Leite Integral", preco: 4.90}
];

let carrinho = [];

// --- FUNÇÕES DE USUÁRIO (A SENHA ÚNICA) ---
function cadastrar() {
    let cpf = document.getElementById("cpfCad").value.trim();
    let senha = document.getElementById("senhaCad").value.trim();

    if(cpf.length < 3 || senha.length < 3) {
        alert("Preencha os campos corretamente!");
        return;
    }

    let usuarios = JSON.parse(localStorage.getItem("usuarios")) || {};

    // VERIFICA SE O USUÁRIO JÁ EXISTE
    if(usuarios[cpf]) {
        alert("Erro: Este CPF já está cadastrado no sistema.");
        return;
    }

    // SALVA O NOVO USUÁRIO
    usuarios[cpf] = senha;
    localStorage.setItem("usuarios", JSON.stringify(usuarios));

    alert("Cadastro realizado com sucesso! Agora faça login.");
    ir('login');
}

function entrar() {
    let cpf = document.getElementById("cpfLogin").value.trim();
    let senha = document.getElementById("senhaLogin").value.trim();
    
    let usuarios = JSON.parse(localStorage.getItem("usuarios")) || {};

    // VALIDAÇÃO DA SENHA ÚNICA
    if(usuarios[cpf] && usuarios[cpf] === senha) {
        localStorage.setItem("usuarioLogado", cpf);
        document.getElementById("userNome").innerText = "Olá, " + cpf;
        renderizarProdutos();
        ir('home');
    } else {
        alert("CPF ou Senha incorretos!");
    }
}

// --- FUNÇÕES DO CARRINHO ---
function addAoCarrinho(id) {
    let p = produtos.find(item => item.id === id);
    carrinho.push(p);
    atualizarUI();
}

function atualizarUI() {
    document.getElementById("cartCount").innerText = carrinho.length;
    let lista = document.getElementById("itensCarrinho");
    let total = 0;
    
    lista.innerHTML = carrinho.map((item, index) => {
        total += item.preco;
        return `<div class="item-carrinho">
            <span>${item.nome} - R$ ${item.preco.toFixed(2)}</span>
            <button onclick="removerItem(${index})" style="width:auto; padding:5px; background:red">X</button>
        </div>`;
    }).join('');
    
    document.getElementById("totalCarrinho").innerText = total.toFixed(2);
}

function removerItem(index) {
    carrinho.splice(index, 1);
    atualizarUI();
}

function finalizarCompra() {
    if(carrinho.length === 0) return alert("Carrinho vazio!");
    let texto = "Olá, gostaria de pedir: \n" + carrinho.map(i => "- " + i.nome).join("\n");
    window.open(`https://wa.me/5521999999999?text=${encodeURIComponent(texto)}`);
}

// --- UTILITÁRIOS ---
function ir(tela) {
    document.querySelectorAll(".tela").forEach(t => t.classList.remove("ativa"));
    document.getElementById(tela).classList.add("ativa");
}

function toggleCarrinho() {
    document.getElementById("carrinho-modal").classList.toggle("aberto");
}

function renderizarProdutos() {
    document.getElementById("listaProdutos").innerHTML = produtos.map(p => `
        <div class="card">
            <strong>${p.nome}</strong><br>
            R$ ${p.preco.toFixed(2)}<br>
            <button onclick="addAoCarrinho(${p.id})" style="margin-top:10px; background:var(--green)">Adicionar</button>
        </div>
    `).join('');
}

function sair() {
    localStorage.removeItem("usuarioLogado");
    location.reload();
}

window.onload = () => {
    let logado = localStorage.getItem("usuarioLogado");
    if(logado) {
        document.getElementById("userNome").innerText = "Olá, " + logado;
        renderizarProdutos();
        ir('home');
    }
};
</script>
</body>
</html>
