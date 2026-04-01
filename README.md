<html lang="pt-br">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title id="siteTitle">Medela Supermercado</title>  
    <style id="dynamicStyles">  
        :root { --primary: #e53935; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }  
    </style>  
    <style>  
        body { margin:0; font-family: 'Segoe UI', Arial, sans-serif; background: var(--bg); overflow-x: hidden; }  
        .tela { display:none; min-height: 100vh; width: 100%; }  
        .ativa { display:block; }  
        .container { padding: 15px; max-width: 600px; margin: auto; }  
        .card { background: white; padding: 15px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); margin-bottom: 15px; }  
        input, select { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }  
        button { border: none; padding: 12px; border-radius: 8px; font-weight: bold; cursor: pointer; color: white; width: 100%; transition: 0.2s; }  
        .btn-main { background: var(--primary); }  
        .btn-del { background: #ff5252; padding: 5px 10px; width: auto; font-size: 12px; }
        .btn-support { background: var(--whatsapp); margin-top: 10px; display: flex; align-items: center; justify-content: center; gap: 8px; }  
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; }  
        
        /* Chat & Carrinho */  
        .chat-screen { display: flex; flex-direction: column; height: 100vh; background: #e5ddd5; }  
        .chat-messages { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 8px; }  
        .msg { max-width: 80%; padding: 10px; border-radius: 10px; font-size: 14px; box-shadow: 0 1px 1px rgba(0,0,0,0.1); }  
        .msg.sent { align-self: flex-end; background: #dcf8c6; }  
        .msg.received { align-self: flex-start; background: white; }  
        .chat-footer { padding: 10px; background: #f0f0f0; display: flex; gap: 5px; }  
        
        .cat-nav { display: flex; gap: 10px; overflow-x: auto; padding: 10px 0; }  
        .cat-btn { background: white; border: 1px solid #ddd; padding: 8px 15px; border-radius: 20px; white-space: nowrap; width: auto; color: #333; }  
        .cat-btn.active { background: var(--primary); color: white; }  
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }  
        .carrinho-flutuante { position: fixed; bottom: 20px; left: 20px; background: #333; color: white; padding: 15px; border-radius: 30px; display: flex; align-items: center; gap: 10px; box-shadow: 0 4px 15px rgba(0,0,0,0.3); z-index: 99; cursor: pointer;}
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
            <button class="btn-support" onclick="abrirSuporteVisitante()">💬 Suporte</button>  
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
        <button onclick="ir('login')" style="background:#888; margin-top:10px;">Voltar</button>  
    </div>  
</div>  

<div id="home" class="tela">  
    <div class="header" id="headerCliente">  
        <span id="welcome">Olá</span>  
        <button style="width:auto; background:rgba(0,0,0,0.2)" onclick="voltarParaLogin()">Sair</button>  
    </div>  
    <div class="container" style="padding-bottom: 100px;">  
        <input type="text" id="buscaProduto" placeholder="🔍 Buscar produto..." oninput="renderizarFiltrado(ultimaCat)">
        <div class="cat-nav" id="catNav"></div>  
        <div class="grid" id="listaLoja"></div>  
    </div>  
    <div class="carrinho-flutuante" id="btnCarrinho" onclick="ir('checkout')">
        🛒 <span id="cartCount">0</span> itens | <b id="cartTotal">R$ 0,00</b>
    </div>
    <button onclick="ir('chat')" style="position:fixed; bottom:20px; right:20px; width:60px; height:60px; border-radius:50%; background:var(--whatsapp); border:none; font-size:25px; color:white; box-shadow: 0 4px 10px rgba(0,0,0,0.3);">💬</button>  
</div>  

<div id="checkout" class="tela">
    <div class="header">
        <button onclick="ir('home')" style="width:auto; background:none;">← Voltar</button>
        <span>Meu Carrinho</span>
        <button onclick="limparCarrinho()" style="width:auto; background:none; font-size:12px;">Limpar</button>
    </div>
    <div class="container">
        <div id="itensCarrinho"></div>
        <div class="card" id="resumoFinal" style="display:none;">
            <h4>Total: <span id="totalFinal">R$ 0,00</span></h4>
            <button class="btn-support" onclick="finalizarPedido()">✅ Pedir pelo WhatsApp</button>
        </div>
    </div>
</div>

<div id="admin" class="tela">  
    <div class="header" id="headerAdmin" style="background:var(--admin-bg)">  
        <span>⚙️ Painel Gestor</span>  
        <button style="width:auto; background:rgba(255,255,255,0.2)" onclick="voltarParaLogin()">Sair</button>  
    </div>  
    <div class="container">  
        <div class="card">  
            <h3>🎨 Modelagem do Site</h3>  
            <input id="cfgNome" placeholder="Nome do Mercado">  
            <input type="color" id="cfgCor">  
            <button onclick="salvarConfigSemRefresh()" class="btn-main">Aplicar Agora</button>  
        </div>  
        <div class="card">  
            <h3>💬 Conversas Ativas</h3>  
            <div id="lista_chats_admin"></div>  
        </div>  
        <div class="card">  
            <h3>📦 Gerenciar Produtos</h3>  
            <input id="pNome" placeholder="Nome do Produto">  
            <input id="pPreco" type="number" placeholder="Preço">  
            <select id="pCat">  
                <option>Açougue</option><option>Bebida</option><option>Limpeza</option><option>Padaria</option><option>Mercearia</option>  
            </select>  
            <button onclick="addProduto()" style="background:var(--admin-bg)">Cadastrar</button>
            <hr>
            <div id="admin_lista_produtos" style="margin-top:15px; max-height: 200px; overflow-y: auto;"></div>
        </div>  
        <div class="card">  
            <h3>👥 Clientes e Senhas</h3>  
            <div id="lista_usuarios_admin"></div>  
        </div>  
    </div>
</div>  

<div id="chat" class="tela">  
    <div class="chat-screen">  
        <div class="header" style="background:#075e54">  
            <button onclick="voltarDoChat()" style="width:auto; background:none; font-size:20px">←</button>  
            <span id="chatTitulo">Chat Suporte</span>  
            <div style="width:30px"></div>  
        </div>  
        <div class="chat-messages" id="box_msgs"></div>  
        <div class="chat-footer">  
            <input id="msg_input" placeholder="Digite uma mensagem...">  
            <button onclick="enviarMensagem()" style="width:50px; border-radius:50%; background:var(--whatsapp)">➤</button>  
        </div>  
    </div>  
</div>  

<script>  
let usuarios = JSON.parse(localStorage.getItem("m_users")) || {};  
let produtos = JSON.parse(localStorage.getItem("m_prod")) || [];  
let config = JSON.parse(localStorage.getItem("m_cfg")) || { nome: "Medela Supermercado", cor: "#e53935" };  
let mensagens = JSON.parse(localStorage.getItem("m_chat")) || {};  
let carrinho = [];

let sessaoAtiva = "";   
let clienteNoChat = "";   
let modoAdminChat = false;  
let ultimaCat = "Todos";

function aplicarEstilos() {  
    document.getElementById("brandName").innerText = "🛒 " + config.nome;  
    document.getElementById("siteTitle").innerText = config.nome;  
    document.getElementById("dynamicStyles").innerHTML = `:root { --primary: ${config.cor}; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }`;  
    document.getElementById("headerCliente").style.backgroundColor = config.cor;  
}  
  
function ir(tela) {  
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));  
    document.getElementById(tela).classList.add('ativa');  
    if(tela === 'home') renderizarLoja();  
    if(tela === 'admin') renderizarAdmin();  
    if(tela === 'checkout') renderizarCarrinho();
}  
  
function entrar() {  
    let cpf = document.getElementById("lCpf").value;  
    let senha = document.getElementById("lSenha").value;  
    if(cpf === 'SUPER-MEDELA#' && senha === 'MEDELA1053@') { sessaoAtiva = "admin"; ir('admin'); return; }  
    if(usuarios[cpf] && usuarios[cpf].senha === senha) { sessaoAtiva = cpf; ir('home'); }  
    else { alert("Dados incorretos!"); }  
}  

function registrar() {  
    let n = document.getElementById("cNome").value;  
    let c = document.getElementById("cCpf").value;  
    let s = document.getElementById("cSenha").value;  
    if(!n || !c || !s) return alert("Preencha tudo");  
    usuarios[c] = { nome: n, senha: s };  
    localStorage.setItem("m_users", JSON.stringify(usuarios));  
    ir('login');  
}  

/* LOJA E CARRINHO */
function renderizarLoja() {  
    document.getElementById("welcome").innerText = "Olá, " + (usuarios[sessaoAtiva]?.nome || "Cliente");  
    const cats = ["Todos", "Açougue", "Bebida", "Limpeza", "Padaria", "Mercearia"];  
    document.getElementById("catNav").innerHTML = cats.map(c => `<button class="cat-btn ${ultimaCat === c ? 'active' : ''}" onclick="renderizarFiltrado('${c}')">${c}</button>`).join('');  
    renderizarFiltrado(ultimaCat);  
    atualizarBotaoCarrinho();
}  

function renderizarFiltrado(cat) {  
    ultimaCat = cat;
    let busca = document.getElementById("buscaProduto").value.toLowerCase();
    let lista = cat === "Todos" ? produtos : produtos.filter(p => p.cat === cat);  
    
    if(busca) {
        lista = lista.filter(p => p.nome.toLowerCase().includes(busca));
    }

    document.getElementById("listaLoja").innerHTML = lista.map((p, index) => `  
        <div class="card" style="text-align:center">  
            <strong>${p.nome}</strong><br><span style="color:green">R$ ${p.fixedPreco || p.preco.toFixed(2)}</span><br>
            <button class="btn-main" style="margin-top:8px; padding:5px; font-size:12px;" onclick="addCarrinho('${p.nome}', ${p.preco})">+ Carrinho</button>
        </div>`).join('') || "Nenhum produto encontrado";  
}  

function addCarrinho(nome, preco) {
    carrinho.push({nome, preco});
    atualizarBotaoCarrinho();
}

function atualizarBotaoCarrinho() {
    let total = carrinho.reduce((sum, item) => sum + item.preco, 0);
    document.getElementById("cartCount").innerText = carrinho.length;
    document.getElementById("cartTotal").innerText = "R$ " + total.toFixed(2);
    document.getElementById("btnCarrinho").style.display = carrinho.length > 0 ? "flex" : "none";
}

function renderizarCarrinho() {
    let container = document.getElementById("itensCarrinho");
    if(carrinho.length === 0) {
        container.innerHTML = "<p style='text-align:center; margin-top:50px;'>Seu carrinho está vazio.</p>";
        document.getElementById("resumoFinal").style.display = "none";
        return;
    }
    document.getElementById("resumoFinal").style.display = "block";
    let total = carrinho.reduce((sum, item) => sum + item.preco, 0);
    document.getElementById("totalFinal").innerText = "R$ " + total.toFixed(2);
    
    container.innerHTML = carrinho.map((item, i) => `
        <div class="card" style="display:flex; justify-content:space-between; align-items:center;">
            <span>${item.nome} - <b>R$ ${item.preco.toFixed(2)}</b></span>
            <button class="btn-del" onclick="removerCarrinho(${i})">Remover</button>
        </div>
    `).join('');
}

function removerCarrinho(i) {
    carrinho.splice(i, 1);
    renderizarCarrinho();
    atualizarBotaoCarrinho();
}

function limparCarrinho() {
    carrinho = [];
    ir('home');
}

function finalizarPedido() {
    let texto = `*Novo Pedido - ${config.nome}*\n\n`;
    carrinho.forEach(item => texto += `- ${item.nome}: R$ ${item.preco.toFixed(2)}\n`);
    let total = carrinho.reduce((sum, item) => sum + item.preco, 0);
    texto += `\n*Total: R$ ${total.toFixed(2)}*`;
    window.open(`https://wa.me/55?text=${encodeURIComponent(texto)}`);
}

/* ADMIN SYSTEM */  
function renderizarAdmin() {  
    document.getElementById("lista_usuarios_admin").innerHTML = Object.keys(usuarios).map(c => `  
        <div style="border-bottom:1px solid #eee; padding:5px"><b>${usuarios[c].nome}</b> | Senha: <b style="color:red">${usuarios[c].senha}</b></div>  
    `).join('');  
    
    document.getElementById("lista_chats_admin").innerHTML = Object.keys(mensagens).map(c => `  
        <button onclick="abrirChatAdmin('${c}')" style="background:#eee; color:#333; margin-bottom:5px">Responder: ${usuarios[c]?.nome || c}</button>  
    `).join('') || "Sem mensagens.";

    document.getElementById("admin_lista_produtos").innerHTML = produtos.map((p, i) => `
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:5px; font-size:13px; border-bottom: 1px solid #eee;">
            <span>${p.nome} (R$ ${p.preco.toFixed(2)})</span>
            <button class="btn-del" onclick="excluirProduto(${i})">X</button>
        </div>
    `).join('');
}  
  
function addProduto() {  
    let n = document.getElementById("pNome").value;  
    let p = document.getElementById("pPreco").value;  
    let c = document.getElementById("pCat").value;  
    if(!n || !p) return alert("Preencha o nome e o preço");
    produtos.push({nome: n, preco: parseFloat(p), cat: c});  
    localStorage.setItem("m_prod", JSON.stringify(produtos));  
    alert("Produto Adicionado!");  
    renderizarAdmin();  
}  

function excluirProduto(index) {
    if(confirm("Deseja realmente excluir este produto?")) {
        produtos.splice(index, 1);
        localStorage.setItem("m_prod", JSON.stringify(produtos));
        renderizarAdmin();
    }
}
  
function salvarConfigSemRefresh() {  
    config.nome = document.getElementById("cfgNome").value || config.nome;  
    config.cor = document.getElementById("cfgCor").value;  
    localStorage.setItem("m_cfg", JSON.stringify(config));  
    aplicarEstilos();  
    alert("Configurações aplicadas com sucesso!");  
}  

/* CHAT SYSTEM */  
function abrirSuporteVisitante() {  
    sessaoAtiva = "visitante_" + Math.floor(Math.random()*999);  
    clienteNoChat = sessaoAtiva;  
    modoAdminChat = false;  
    document.getElementById("chatTitulo").innerText = "Suporte Online";  
    ir('chat');  
}  
  
function abrirChatAdmin(cpf) {  
    clienteNoChat = cpf;  
    modoAdminChat = true;  
    document.getElementById("chatTitulo").innerText = "Chat: " + (usuarios[cpf]?.nome || cpf);  
    ir('chat');  
}  
  
function enviarMensagem() {  
    let input = document.getElementById("msg_input");  
    if(!input.value) return;  
    let idChat = modoAdminChat ? clienteNoChat : sessaoAtiva;  
    if(!mensagens[idChat]) mensagens[idChat] = [];  
    mensagens[idChat].push({ texto: input.value, autor: modoAdminChat ? 'admin' : 'cliente' });  
    localStorage.setItem("m_chat", JSON.stringify(mensagens));  
    input.value = "";  
    renderizarMensagens();  
}  
  
function renderizarMensagens() {  
    let idChat = modoAdminChat ? clienteNoChat : sessaoAtiva;  
    let msgs = mensagens[idChat] || [];  
    document.getElementById("box_msgs").innerHTML = msgs.map(m => `  
        <div class="msg ${m.autor === (modoAdminChat ? 'admin' : 'cliente') ? 'sent' : 'received'}">${m.texto}</div>  
    `).join('');  
    document.getElementById("box_msgs").scrollTop = 9999;  
}  
  
function voltarDoChat() {  
    if(modoAdminChat) ir('admin');  
    else if(sessaoAtiva.includes("visitante")) ir('login');  
    else ir('home');  
}  
  
function voltarParaLogin() {  
    sessaoAtiva = "";  
    carrinho = [];
    document.getElementById("lCpf").value = "";  
    document.getElementById("lSenha").value = "";  
    ir('login');  
}  
  
window.onload = aplicarEstilos;  
</script>  
</body>  
</html>
