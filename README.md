<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title id="siteTitle">Medela Supermercado</title>
    <style id="dynamicStyles">
        :root { --primary: #e53935; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }
    </style>
    <style>
        body { margin:0; font-family: 'Segoe UI', Arial, sans-serif; background: var(--bg); }
        .tela { display:none; min-height: 100vh; }
        .ativa { display:block; }
        .container { padding: 15px; max-width: 600px; margin: auto; }
        .card { background: white; padding: 15px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); margin-bottom: 15px; }
        
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-size: 16px; }
        button { border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; color: white; width: 100%; transition: 0.3s; }
        .btn-main { background: var(--primary); }
        .btn-support { background: var(--whatsapp); margin-top: 10px; display: flex; align-items: center; justify-content: center; gap: 8px; }
        
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; }
        
        /* Categorias Cliente */
        .cat-nav { display: flex; gap: 10px; overflow-x: auto; padding: 10px 0; margin-bottom: 10px; scrollbar-width: none; }
        .cat-nav::-webkit-scrollbar { display: none; }
        .cat-btn { background: white; color: #333; border: 1px solid #ddd; padding: 8px 15px; border-radius: 20px; white-space: nowrap; width: auto; font-size: 14px; }
        .cat-btn.active { background: var(--primary); color: white; border-color: var(--primary); }

        /* Grid Produtos */
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        .prod-card { background: white; padding: 10px; border-radius: 10px; text-align: center; border: 1px solid #eee; }

        /* Chat */
        .chat-screen { display: flex; flex-direction: column; height: 100vh; background: #e5ddd5; }
        .chat-messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }
        .msg { max-width: 80%; padding: 10px; border-radius: 10px; font-size: 14px; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }
        .msg.sent { align-self: flex-end; background: #dcf8c6; }
        .msg.received { align-self: flex-start; background: white; }
    </style>
</head>
<body>

<div id="login" class="tela ativa">
    <div class="container" style="text-align:center; padding-top:60px;">
        <h1 id="brandName">🛒 Medela</h1>
        <div class="card">
            <input id="lCpf" placeholder="CPF ou 'admin'">
            <input id="lSenha" type="password" placeholder="Senha">
            <button class="btn-main" onclick="entrar()">Entrar</button>
            <button class="btn-support" onclick="abrirSuporteRapido()">💬 Suporte</button>
            <p onclick="ir('cadastro')" style="color:var(--primary); cursor:pointer; margin-top:20px;">Criar Conta</p>
        </div>
    </div>
</div>

<div id="cadastro" class="tela">
    <div class="container">
        <h2>Criar Conta</h2>
        <input id="cNome" placeholder="Nome Completo">
        <input id="cCpf" placeholder="CPF">
        <input id="cSenha" type="password" placeholder="Senha">
        <button class="btn-main" onclick="registrar()">Finalizar Cadastro</button>
        <button onclick="ir('login')" style="background:none; color:gray; margin-top:10px;">Voltar</button>
    </div>
</div>

<div id="home" class="tela">
    <div class="header">
        <span id="welcome">Olá</span>
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div class="cat-nav" id="catNavCliente">
            </div>
        <div class="grid" id="listaLoja">
            </div>
    </div>
    <button onclick="ir('chat_suporte')" style="position:fixed; bottom:20px; right:20px; width:60px; height:60px; border-radius:50%; background:var(--whatsapp); border:none; font-size:25px; color:white; box-shadow: 0 4px 10px rgba(0,0,0,0.3);">💬</button>
</div>

<div id="admin" class="tela">
    <div class="header" style="background:var(--admin-bg)">
        <span>⚙️ Painel Gestor</span>
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="sair()">Sair</button>
    </div>
    <div class="container">
        <div class="card">
            <h3>🎨 Personalizar Site</h3>
            <input id="cfgNome" placeholder="Nome do Supermercado">
            <input type="color" id="cfgCor">
            <button onclick="salvarConfig()" class="btn-main">Aplicar</button>
        </div>

        <div class="card">
            <h3>📦 Novo Produto</h3>
            <input id="pNome" placeholder="Nome do Produto">
            <input id="pPreco" type="number" placeholder="Preço (Ex: 10.50)">
            <label>Categoria:</label>
            <select id="pCat">
                <option value="Açougue">Açougue</option>
                <option value="Bebida">Bebida</option>
                <option value="Limpeza">Limpeza</option>
                <option value="Padaria">Padaria</option>
                <option value="Mercearia">Mercearia</option>
            </select>
            <button onclick="addProduto()" style="background:var(--admin-bg)">Adicionar ao Estoque</button>
        </div>

        <div class="card">
            <h3>👥 Clientes e Senhas Visíveis</h3>
            <div id="lista_usuarios_admin"></div>
        </div>

        <div class="card">
            <h3>💬 Conversas</h3>
            <div id="lista_chats_admin"></div>
        </div>
    </div>
</div>

<div id="chat_suporte" class="tela">
    <div class="chat-screen">
        <div class="header" style="background:#075e54">
            <button onclick="voltarChat()" style="width:auto; background:none; font-size:20px">←</button>
            <span id="chatNomeTopo">Suporte</span>
            <div style="width:30px"></div>
        </div>
        <div class="chat-messages" id="box_msgs"></div>
        <div style="padding:10px; background:#f0f0f0; display:flex; gap:5px;">
            <input id="msg_input" placeholder="Mensagem..." style="margin:0; border-radius:20px;">
            <button onclick="enviarMsg()" style="width:50px; border-radius:50%; background:var(--whatsapp)">➤</button>
        </div>
    </div>
</div>

<script>
/* BANCO DE DADOS */
let usuarios = JSON.parse(localStorage.getItem("med_users")) || {};
let produtos = JSON.parse(localStorage.getItem("med_prod")) || [];
let config = JSON.parse(localStorage.getItem("med_config")) || { nome: "Medela Supermercado", cor: "#e53935" };
let mensagens = JSON.parse(localStorage.getItem("med_chat")) || {};

let usuarioAtivo = ""; 
let catAtual = "Todos";
const CATEGORIAS = ["Todos", "Açougue", "Bebida", "Limpeza", "Padaria", "Mercearia"];

function aplicarConfig() {
    document.getElementById("brandName").innerText = "🛒 " + config.nome;
    document.getElementById("siteTitle").innerText = config.nome;
    document.getElementById("dynamicStyles").innerHTML = `:root { --primary: ${config.cor}; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }`;
}

function ir(tela) {
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));
    document.getElementById(tela).classList.add('ativa');
    if(tela === 'home') renderizarLoja();
    if(tela === 'admin') renderizarAdmin();
}

/* AUTH */
function entrar() {
    let cpf = document.getElementById("lCpf").value;
    let senha = document.getElementById("lSenha").value;
    if(cpf === 'admin' && senha === 'admin123') return ir('admin');
    if(usuarios[cpf] && usuarios[cpf].senha === senha) {
        usuarioAtivo = cpf;
        ir('home');
    } else { alert("Erro de login!"); }
}

function registrar() {
    let nome = document.getElementById("cNome").value;
    let cpf = document.getElementById("cCpf").value;
    let senha = document.getElementById("cSenha").value;
    if(!nome || !cpf || !senha) return alert("Preencha tudo!");
    usuarios[cpf] = { nome, senha };
    localStorage.setItem("med_users", JSON.stringify(usuarios));
    ir('login');
}

/* LOJA E CATEGORIAS */
function renderizarLoja() {
    document.getElementById("welcome").innerText = "Olá, " + (usuarios[usuarioAtivo] ? usuarios[usuarioAtivo].nome : "Visitante");
    
    // Menu de categorias
    document.getElementById("catNavCliente").innerHTML = CATEGORIAS.map(c => `
        <button class="cat-btn ${catAtual === c ? 'active' : ''}" onclick="filtrar('${c}')">${c}</button>
    `).join('');

    // Produtos Filtrados
    let lista = catAtual === "Todos" ? produtos : produtos.filter(p => p.cat === catAtual);
    document.getElementById("listaLoja").innerHTML = lista.map(p => `
        <div class="prod-card">
            <div style="font-size:30px">📦</div>
            <strong>${p.nome}</strong><br>
            <span style="color:green">R$ ${p.preco.toFixed(2)}</span><br>
            <small>${p.cat}</small>
            <button class="btn-main" style="margin-top:8px; font-size:12px">Adicionar</button>
        </div>
    `).join('') || "<p style='grid-column:1/-1'>Nenhum produto nesta categoria.</p>";
}

function filtrar(c) { catAtual = c; renderizarLoja(); }

/* ADMIN */
function addProduto() {
    let nome = document.getElementById("pNome").value;
    let preco = parseFloat(document.getElementById("pPreco").value);
    let cat = document.getElementById("pCat").value;
    if(!nome || isNaN(preco)) return alert("Dados inválidos");
    produtos.push({id: Date.now(), nome, preco, cat});
    localStorage.setItem("med_prod", JSON.stringify(produtos));
    alert("Produto salvo!");
    renderizarAdmin();
}

function renderizarAdmin() {
    // Listar usuários e senhas (como solicitado)
    document.getElementById("lista_usuarios_admin").innerHTML = Object.keys(usuarios).map(cpf => `
        <div style="border-bottom:1px solid #eee; padding:8px 0">
            <b>${usuarios[cpf].nome}</b> | CPF: ${cpf} | Senha: <b style="color:red">${usuarios[cpf].senha}</b>
        </div>
    `).join('');

    // Listar Chats
    document.getElementById("lista_chats_admin").innerHTML = Object.keys(mensagens).map(cpf => `
        <button onclick="abrirChatAdmin('${cpf}')" style="background:#eee; color:#333; margin-bottom:5px">
            Chat com: ${usuarios[cpf] ? usuarios[cpf].nome : cpf}
        </button>
    `).join('') || "Sem conversas.";
}

function salvarConfig() {
    config.nome = document.getElementById("cfgNome").value || config.nome;
    config.cor = document.getElementById("cfgCor").value;
    localStorage.setItem("med_config", JSON.stringify(config));
    location.reload();
}

/* CHAT */
function enviarMsg() {
    let input = document.getElementById("msg_input");
    if(!input.value) return;
    if(!usuarioAtivo) usuarioAtivo = "visitante_" + Math.floor(Math.random()*100);
    if(!mensagens[usuarioAtivo]) mensagens[usuarioAtivo] = [];
    mensagens[usuarioAtivo].push({ texto: input.value, autor: 'cliente' });
    localStorage.setItem("med_chat", JSON.stringify(mensagens));
    input.value = ""; renderizarMsgs();
}

function renderizarMsgs() {
    let msgs = mensagens[usuarioAtivo] || [];
    document.getElementById("box_msgs").innerHTML = msgs.map(m => `
        <div class="msg ${m.autor === 'cliente' ? 'sent' : 'received'}">${m.texto}</div>
    `).join('');
    document.getElementById("box_msgs").scrollTop = 9999;
}

function abrirSuporteRapido() { ir('chat_suporte'); renderizarMsgs(); }
function voltarChat() { ir(usuarioAtivo.includes("visitante") ? 'login' : 'home'); }
function sair() { location.reload(); }

window.onload = aplicarConfig;
</script>
</body>
</html>
