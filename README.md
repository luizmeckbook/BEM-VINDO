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
        .btn-support { background: var(--whatsapp); margin-top: 10px; display: flex; align-items: center; justify-content: center; gap: 8px; }  
        .header { background: var(--primary); color: white; padding: 15px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; }  
        
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

        .cart-bar { position: fixed; bottom: 0; left: 0; right: 0; background: #333; color: white; padding: 20px; display: none; justify-content: space-between; align-items: center; z-index: 200; border-radius: 20px 20px 0 0; cursor: pointer; box-shadow: 0 -4px 10px rgba(0,0,0,0.2); }
        .remove-btn { background: #ff5252; padding: 5px 10px; width: auto; font-size: 11px; }
        .profit-badge { background: #e8f5e9; color: #2e7d32; padding: 2px 6px; border-radius: 4px; font-size: 10px; font-weight: bold; }
        
        /* Estilos Relatório */
        .report-table { width: 100%; border-collapse: collapse; font-size: 12px; margin-top: 10px; }
        .report-table th, .report-table td { border: 1px solid #eee; padding: 8px; text-align: left; }
        .report-table th { background: #f8f8f8; }
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
        <input type="text" id="pesquisaLoja" placeholder="Pesquisar produto..." oninput="renderizarLoja()">
        <div class="cat-nav" id="catNav"></div>  
        <div class="grid" id="listaLoja"></div>  
    </div>  
    <div class="cart-bar" id="cartBar" onclick="ir('carrinho')">
        <span>🛒 <b id="cartCount">0</b> itens</span>
        <b id="cartTotal">R$ 0,00</b>
        <span style="background:var(--whatsapp); padding:5px 10px; border-radius:8px">ABRIR</span>
    </div>
    <button onclick="ir('chat')" style="position:fixed; bottom:90px; right:20px; width:60px; height:60px; border-radius:50%; background:var(--whatsapp); border:none; font-size:25px; color:white; box-shadow: 0 4px 10px rgba(0,0,0,0.3); z-index: 150;">💬</button>  
</div>  

<div id="carrinho" class="tela">
    <div class="header">
        <button onclick="ir('home')" style="width:auto; background:none; font-size:20px">←</button>
        <span>Meu Carrinho</span>
        <div style="width:30px"></div>
    </div>
    <div class="container">
        <div id="listaItensCarrinho"></div>
        <div class="card" id="cardPagamento" style="display:none;">
            <h3>Pagamento</h3>
            <select id="metodoPagamento" onchange="toggleTroco()">
                <option value="">Selecione a forma...</option>
                <option value="Pix">Pix</option>
                <option value="Cartão de Crédito">Cartão de Crédito</option>
                <option value="Cartão de Débito">Cartão de Débito</option>
                <option value="Dinheiro">Dinheiro</option>
            </select>
            <div id="divTroco" style="display:none;">
                <input type="number" id="valorTroco" placeholder="Troco para quanto?">
            </div>
            <button class="btn-main" onclick="finalizarPedido()" style="margin-top:15px; background:var(--whatsapp)">Finalizar via WhatsApp</button>
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
            <h3>📊 Relatório de Vendas (Por Data)</h3>
            <div id="relatorioVendas"></div>
            <button onclick="limparRelatorio()" style="background:#888; font-size:10px; margin-top:10px; width:auto; padding:5px">Limpar Histórico</button>
        </div>

        <div class="card">  
            <h3>🎨 Modelagem do Site</h3>  
            <input id="cfgNome" placeholder="Nome do Mercado">  
            <input type="color" id="cfgCor">  
            <button onclick="salvarConfigSemRefresh()" class="btn-main">Aplicar Agora</button>  
        </div>  
        <div class="card">  
            <h3>📦 Novo Produto (Balanceamento)</h3>  
            <input id="pNome" placeholder="Nome do Produto">  
            <div style="display:flex; gap:10px">
                <input id="pBruto" type="number" placeholder="Bruto (Custo)">  
                <input id="pLiquido" type="number" placeholder="Líquido (Venda)">
            </div>
            <select id="pCat">  
                <option>Açougue</option><option>Bebida</option><option>Limpeza</option><option>Padaria</option><option>Mercearia</option>  
            </select>  
            <button onclick="addProduto()" style="background:var(--admin-bg)">Cadastrar e Salvar</button>  
            <hr>
            <h3>🔍 Gerenciar Produtos</h3>
            <input type="text" id="pesquisaAdmin" placeholder="Procurar produto cadastrado..." oninput="renderizarAdmin()">
            <div id="admin_lista_excluir" style="margin-top:10px; max-height: 250px; overflow-y: auto;"></div>
        </div>  
        <div class="card">  
            <h3>💬 Conversas Ativas</h3>  
            <div id="lista_chats_admin"></div>  
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
let vendas = JSON.parse(localStorage.getItem("m_vendas")) || []; // [{data, bruto, liquido}]
let carrinho = [];

let sessaoAtiva = "";   
let clienteNoChat = "";   
let modoAdminChat = false;  
let categoriaAtual = "Todos";

function aplicarEstilos() {  
    document.getElementById("brandName").innerText = "🛒 " + config.nome;  
    document.getElementById("siteTitle").innerText = config.nome;  
    document.getElementById("dynamicStyles").innerHTML = `:root { --primary: ${config.cor}; --admin-bg: #2c3e50; --bg: #f4f4f4; --whatsapp: #25d366; }`;  
    document.getElementById("headerCliente").style.backgroundColor = config.cor;  
}  
  
function ir(tela) {  
    document.querySelectorAll('.tela').forEach(t => t.classList.remove('ativa'));  
    document.getElementById(tela).classList.add('ativa');  
    if(tela === 'home') { renderizarLoja(); atualizarBarraCarrinho(); }
    if(tela === 'admin') renderizarAdmin();  
    if(tela === 'carrinho') renderizarCarrinho();
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

/* CARRINHO E VENDAS */
function addCarrinho(idUnico) {
    let p = produtos.find(item => item.id === idUnico);
    carrinho.push(p);
    atualizarBarraCarrinho();
}

function removerCarrinho(index) {
    carrinho.splice(index, 1);
    renderizarCarrinho();
    atualizarBarraCarrinho();
}

function atualizarBarraCarrinho() {
    let total = carrinho.reduce((a, b) => a + b.liquido, 0);
    let bar = document.getElementById("cartBar");
    if(carrinho.length > 0) {
        bar.style.display = "flex";
        document.getElementById("cartCount").innerText = carrinho.length;
        document.getElementById("cartTotal").innerText = "R$ " + total.toFixed(2);
    } else {
        bar.style.display = "none";
    }
}

function renderizarCarrinho() {
    let html = "";
    carrinho.forEach((p, i) => {
        html += `<div class="card" style="display:flex; justify-content:space-between; align-items:center;">
            <div><b>${p.nome}</b><br>R$ ${p.liquido.toFixed(2)}</div>
            <button class="remove-btn" onclick="removerCarrinho(${i})">Remover</button>
        </div>`;
    });
    document.getElementById("listaItensCarrinho").innerHTML = html || "<p style='text-align:center; padding:20px;'>Carrinho vazio</p>";
    document.getElementById("cardPagamento").style.display = carrinho.length > 0 ? "block" : "none";
}

function toggleTroco() {
    let m = document.getElementById("metodoPagamento").value;
    document.getElementById("divTroco").style.display = m === "Dinheiro" ? "block" : "none";
}

function finalizarPedido() {
    let metodo = document.getElementById("metodoPagamento").value;
    if(!metodo) return alert("Escolha a forma de pagamento");
    
    // Registrar Venda Localmente para o ADM
    let totalLiquido = carrinho.reduce((a, b) => a + b.liquido, 0);
    let totalBruto = carrinho.reduce((a, b) => a + b.bruto, 0);
    let hoje = new Date().toLocaleDateString();
    
    vendas.push({ data: hoje, bruto: totalBruto, liquido: totalLiquido });
    localStorage.setItem("m_vendas", JSON.stringify(vendas));

    // Preparar WhatsApp
    let msg = `*Novo Pedido - ${config.nome}*\n\n`;
    carrinho.forEach(p => msg += `- ${p.nome}: R$ ${p.liquido.toFixed(2)}\n`);
    msg += `\n*Total:* R$ ${totalLiquido.toFixed(2)}\n*Pagamento:* ${metodo}`;
    if(metodo === "Dinheiro") {
        let troco = document.getElementById("valorTroco").value;
        msg += troco ? `\n*Troco para:* R$ ${troco}` : `\n*Sem troco*`;
    }
    
    window.open(`https://wa.me/55?text=${encodeURIComponent(msg)}`);
}

/* LOJA CLIENTE */
function renderizarLoja() {  
    document.getElementById("welcome").innerText = "Olá, " + (usuarios[sessaoAtiva]?.nome || "Cliente");  
    const cats = ["Todos", "Açougue", "Bebida", "Limpeza", "Padaria", "Mercearia"];  
    document.getElementById("catNav").innerHTML = cats.map(c => `<button class="cat-btn ${categoriaAtual === c ? 'active' : ''}" onclick="filtrar('${c}')">${c}</button>`).join('');  
    let pesquisa = document.getElementById("pesquisaLoja").value.toLowerCase();
    let filtrados = categoriaAtual === "Todos" ? produtos : produtos.filter(p => p.cat === categoriaAtual);
    if(pesquisa) filtrados = filtrados.filter(p => p.nome.toLowerCase().includes(pesquisa));

    document.getElementById("listaLoja").innerHTML = filtrados.map((p) => `
        <div class="card" style="text-align:center">  
            <strong>${p.nome}</strong><br><span style="color:green">R$ ${p.liquido.toFixed(2)}</span>
            <button class="btn-main" onclick="addCarrinho(${p.id})" style="margin-top:10px; padding:5px; font-size:12px;">+ Adicionar</button>
        </div>`).join('') || "Nenhum produto.";  
}  
function filtrar(c) { categoriaAtual = c; renderizarLoja(); }

/* ADMIN SYSTEM */  
function renderizarAdmin() {  
    // Relatório de Vendas
    const resumoVendas = {};
    vendas.forEach(v => {
        if(!resumoVendas[v.data]) resumoVendas[v.data] = { bruto: 0, liquido: 0 };
        resumoVendas[v.data].bruto += v.bruto;
        resumoVendas[v.data].liquido += v.liquido;
    });

    let htmlRelatorio = `<table class="report-table"><tr><th>Data</th><th>Bruto (Custo)</th><th>Líquido (Venda)</th><th>Lucro</th></tr>`;
    for(let data in resumoVendas) {
        let b = resumoVendas[data].bruto;
        let l = resumoVendas[data].liquido;
        htmlRelatorio += `<tr><td>${data}</td><td>R$ ${b.toFixed(2)}</td><td>R$ ${l.toFixed(2)}</td><td style="color:green">R$ ${(l-b).toFixed(2)}</td></tr>`;
    }
    htmlRelatorio += `</table>`;
    document.getElementById("relatorioVendas").innerHTML = Object.keys(resumoVendas).length ? htmlRelatorio : "Nenhuma venda registrada hoje.";

    // Lista de Usuários
    document.getElementById("lista_usuarios_admin").innerHTML = Object.keys(usuarios).map(c => `  
        <div style="border-bottom:1px solid #eee; padding:5px"><b>${usuarios[c].nome}</b> | Senha: <b style="color:red">${usuarios[c].senha}</b></div>  
    `).join('');  
    
    // Lista de Produtos com Busca
    let busca = document.getElementById("pesquisaAdmin").value.toLowerCase();
    let listaFiltrada = busca ? produtos.filter(p => p.nome.toLowerCase().includes(busca)) : produtos;

    document.getElementById("admin_lista_excluir").innerHTML = listaFiltrada.map((p) => {
        let lucro = p.liquido - p.bruto;
        let indexOriginal = produtos.findIndex(item => item.id === p.id);
        return `
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:8px; font-size:12px; border-bottom:1px solid #eee; padding-bottom:5px">
            <div>
                <b>${p.nome}</b><br>
                B: R$ ${p.bruto.toFixed(2)} | L: R$ ${p.liquido.toFixed(2)} 
                <span class="profit-badge">Lucro: R$ ${lucro.toFixed(2)}</span>
            </div>
            <button onclick="excluirProd(${indexOriginal})" style="background:red; padding:4px 8px; width:auto">Excluir</button>
        </div>`;
    }).join('') || "<p style='font-size:12px; color:#666'>Nenhum produto.</p>";
}  

function addProduto() {  
    let n = document.getElementById("pNome").value;  
    let b = document.getElementById("pBruto").value;  
    let l = document.getElementById("pLiquido").value;  
    let c = document.getElementById("pCat").value;  
    if(!n || !b || !l) return alert("Preencha Nome, Bruto e Líquido");
    produtos.push({ id: Date.now() + Math.random(), nome: n, bruto: parseFloat(b), liquido: parseFloat(l), cat: c });  
    localStorage.setItem("m_prod", JSON.stringify(produtos));  
    document.getElementById("pNome").value = "";
    document.getElementById("pBruto").value = "";
    document.getElementById("pLiquido").value = "";
    renderizarAdmin();  
}  

function excluirProd(i) {
    if(confirm("Excluir este produto?")) {
        produtos.splice(i, 1);
        localStorage.setItem("m_prod", JSON.stringify(produtos));
        renderizarAdmin();
    }
}

function limparRelatorio() {
    if(confirm("Deseja apagar todo o histórico de vendas?")) {
        vendas = [];
        localStorage.setItem("m_vendas", JSON.stringify(vendas));
        renderizarAdmin();
    }
}

function salvarConfigSemRefresh() {  
    config.nome = document.getElementById("cfgNome").value || config.nome;  
    config.cor = document.getElementById("cfgCor").value;  
    localStorage.setItem("m_cfg", JSON.stringify(config));  
    aplicarEstilos(); 
    alert("Configurações aplicadas!");  
}  
  
function voltarParaLogin() { sessaoAtiva = ""; carrinho = []; ir('login'); }  

/* CHAT */
function abrirSuporteVisitante() { sessaoAtiva = "visitante_" + Math.floor(Math.random()*999); clienteNoChat = sessaoAtiva; modoAdminChat = false; document.getElementById("chatTitulo").innerText = "Suporte Online"; ir('chat'); }  
function abrirChatAdmin(cpf) { clienteNoChat = cpf; modoAdminChat = true; document.getElementById("chatTitulo").innerText = "Chat: " + (usuarios[cpf]?.nome || cpf); ir('chat'); }  
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
