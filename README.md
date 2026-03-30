<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - Sistema Integrado</title>
    <style>
        :root { --primary: #e53935; --admin-sec: #263238; --bg: #f8f9fa; --green: #27ae60; }
        body { margin:0; font-family: 'Segoe UI', sans-serif; background: var(--bg); }
        .tela { display:none; min-height: 100vh; }
        .ativa { display:block; }

        /* Estilos de Form */
        .box { max-width: 400px; margin: 50px auto; padding: 20px; background: white; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); text-align: center; }
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        button { width: 100%; padding: 12px; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; transition: 0.3s; }
        .btn-red { background: var(--primary); color: white; }
        .btn-admin { background: var(--admin-sec); color: white; }

        /* Header */
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top:0; z-index: 100; }
        .header-admin { background: var(--admin-sec); }

        /* Vitrine e Categorias */
        .nav-cats { display: flex; gap: 10px; overflow-x: auto; padding: 15px; background: white; border-bottom: 1px solid #eee; }
        .cat-item { padding: 8px 16px; background: #eee; border-radius: 20px; font-size: 14px; white-space: nowrap; cursor: pointer; }
        .cat-item.active { background: var(--primary); color: white; }
        
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 15px; }
        .produto-card { background: white; padding: 10px; border-radius: 10px; text-align: center; border: 1px solid #eee; }
        .produto-card img { width: 100%; height: 100px; object-fit: contain; }

        /* Tabela Admin */
        .admin-section { padding: 15px; }
        table { width: 100%; border-collapse: collapse; background: white; margin-top: 10px; border-radius: 8px; overflow: hidden; }
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid #eee; font-size: 13px; }
        th { background: #f1f1f1; }
    </style>
</head>
<body>

<div id="login" class="tela ativa">
    <div class="box">
        <h1 style="color:var(--primary)">🛒 Medela</h1>
        <p>Acesse sua conta ou painel admin</p>
        <input id="cpfLogin" placeholder="CPF ou 'admin'">
        <input id="senhaLogin" type="password" placeholder="Senha">
        <button class="btn-red" onclick="autenticar()">Entrar no Sistema</button>
        <p onclick="ir('cadastro')" style="cursor:pointer; color:blue; margin-top:15px;">Novo por aqui? Cadastre-se</p>
    </div>
</div>

<div id="cadastro" class="tela">
    <div class="box">
        <h2>Criar Conta</h2>
        <input id="nomeCad" placeholder="Nome Completo">
        <input id="cpfCad" placeholder="CPF">
        <input id="senhaCad" type="password" placeholder="Crie uma Senha">
        <button class="btn-red" onclick="acaoCadastrar()">Cadastrar Agora</button>
        <button onclick="ir('login')" style="margin-top:10px; background:none;">Voltar para o Login</button>
    </div>
</div>

<div id="home" class="tela">
    <div class="header">
        <span id="nomeUserHeader">Olá, Cliente</span>
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
    </div>
    
    <div class="nav-cats" id="menuCategorias">
        </div>

    <div class="grid" id="listaProdutosLoja">
        </div>
</div>

<div id="admin" class="tela">
    <div class="header header-admin">
        <span>⚙️ Painel Administrativo</span>
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="sair()">Sair</button>
    </div>

    <div class="admin-section">
        <div class="box" style="margin: 10px 0; max-width: 100%;">
            <h3>Cadastrar Produto</h3>
            <input id="admNome" placeholder="Nome do Produto">
            <input id="admPreco" type="number" placeholder="Preço (Ex: 12.90)">
            <select id="admCat">
                <option value="Açougue">Açougue</option>
                <option value="Bebida">Bebida</option>
                <option value="Limpeza">Limpeza</option>
                <option value="Padaria">Padaria</option>
                <option value="Mercearia">Mercearia</option>
            </select>
            <button class="btn-admin" onclick="admAdicionarProduto()">Salvar no Estoque</button>
        </div>

        <h3>Estoque Atual</h3>
        <div id="estoqueLista"></div>

        <h3>Clientes do App</h3>
        <table id="tabelaClientes">
            <thead><tr><th>Nome</th><th>CPF</th></tr></thead>
            <tbody></tbody>
        </table>
    </div>
</div>

<script>
/* DADOS DO SISTEMA */
const CATEGORIAS = ["Todos", "Açougue", "Bebida", "Limpeza", "Padaria", "Mercearia"];
let db_produtos = JSON.parse(localStorage.getItem("m_produtos")) || [];
let cat_atual = "Todos";

/* SISTEMA DE NAVEGAÇÃO E LOGIN */
function ir(tela) {
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));
    document.getElementById(tela).classList.add('ativa');
    if(tela === 'home') renderizarLoja();
    if(tela === 'admin') renderizarAdmin();
}

function autenticar() {
    const cpf = document.getElementById("cpfLogin").value.trim();
    const senha = document.getElementById("senhaLogin").value.trim();

    // Login Admin
    if(cpf === 'admin' && senha === 'admin123') {
        localStorage.setItem("sessao", "ADMIN");
        return ir('admin');
    }

    // Login Cliente
    let users = JSON.parse(localStorage.getItem("m_users")) || {};
    if(users[cpf] && users[cpf].senha === senha) {
        localStorage.setItem("sessao", users[cpf].nome);
        ir('home');
    } else {
        alert("Acesso negado! Verifique CPF e Senha.");
    }
}

function acaoCadastrar() {
    const nome = document.getElementById("nomeCad").value;
    const cpf = document.getElementById("cpfCad").value;
    const senha = document.getElementById("senhaCad").value;

    if(!nome || !cpf) return alert("Preencha os campos!");

    let users = JSON.parse(localStorage.getItem("m_users")) || {};
    if(users[cpf]) return alert("CPF já cadastrado!");

    users[cpf] = { nome, senha };
    localStorage.setItem("m_users", JSON.stringify(users));
    alert("Cadastro com sucesso!");
    ir('login');
}

/* LÓGICA DO ADMINISTRADOR */
function admAdicionarProduto() {
    const nome = document.getElementById("admNome").value;
    const preco = parseFloat(document.getElementById("admPreco").value);
    const cat = document.getElementById("admCat").value;

    if(!nome || isNaN(preco)) return alert("Dados inválidos");

    db_produtos.push({ id: Date.now(), nome, preco, cat });
    localStorage.setItem("m_produtos", JSON.stringify(db_produtos));
    
    document.getElementById("admNome").value = "";
    document.getElementById("admPreco").value = "";
    renderizarAdmin();
}

function admExcluir(id) {
    db_produtos = db_produtos.filter(p => p.id !== id);
    localStorage.setItem("m_produtos", JSON.stringify(db_produtos));
    renderizarAdmin();
}

function renderizarAdmin() {
    // Lista de Estoque
    const container = document.getElementById("estoqueLista");
    container.innerHTML = db_produtos.map(p => `
        <div class="produto-card" style="display:flex; justify-content:space-between; align-items:center; margin-bottom:5px;">
            <span><b>${p.nome}</b> - ${p.cat} (R$ ${p.preco.toFixed(2)})</span>
            <button onclick="admExcluir(${p.id})" style="width:auto; padding:5px 10px; background:red; color:white;">X</button>
        </div>
    `).join('');

    // Lista de Clientes
    const users = JSON.parse(localStorage.getItem("m_users")) || {};
    const tbody = document.querySelector("#tabelaClientes tbody");
    tbody.innerHTML = Object.keys(users).map(key => `
        <tr><td>${users[key].nome}</td><td>${key}</td></tr>
    `).join('');
}

/* LÓGICA DA LOJA (CLIENTE) */
function renderizarLoja() {
    document.getElementById("nomeUserHeader").innerText = "Olá, " + localStorage.getItem("sessao");
    
    // Categorias
    const menu = document.getElementById("menuCategorias");
    menu.innerHTML = CATEGORIAS.map(c => `
        <div class="cat-item ${cat_atual === c ? 'active' : ''}" onclick="filtrar('${c}')">${c}</div>
    `).join('');

    // Produtos
    const grid = document.getElementById("listaProdutosLoja");
    let lista = cat_atual === "Todos" ? db_produtos : db_produtos.filter(p => p.cat === cat_atual);
    
    if(lista.length === 0) {
        grid.innerHTML = "<p style='grid-column: 1/-1; text-align:center'>Nenhum produto nesta categoria.</p>";
        return;
    }

    grid.innerHTML = lista.map(p => `
        <div class="produto-card">
            <div style="font-size:40px">📦</div>
            <strong>${p.nome}</strong><br>
            <span style="color:var(--green); font-weight:bold">R$ ${p.preco.toFixed(2)}</span><br>
            <button class="btn-red" style="margin-top:10px; padding:5px; font-size:12px">Adicionar</button>
        </div>
    `).join('');
}

function filtrar(c) {
    cat_atual = c;
    renderizarLoja();
}

function sair() {
    localStorage.removeItem("sessao");
    location.reload();
}

// Iniciar
window.onload = () => {
    let s = localStorage.getItem("sessao");
    if(s === "ADMIN") ir('admin');
    else if(s) ir('home');
};
</script>

</body>
</html>
