MERCADO MEDELA
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V12</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        .cat-bar { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-item { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 11px; font-weight: bold; cursor: pointer; }
        .cat-item.active-cat { background: var(--green); color: white; }

        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 120px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); display: flex; flex-direction: column; justify-content: space-between;}
        .card img { width: 100%; height: 80px; object-fit: contain; margin-bottom: 5px; }

        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; margin-top: 5px; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }

        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        
        .admin-item { background: white; padding: 10px; margin-bottom: 5px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange); }
        .chat-box { flex: 1; overflow-y: auto; padding: 15px; display: flex; flex-direction: column; gap: 10px; background: #e5ddd5; margin-bottom: 80px; }
        .msg { padding: 10px; border-radius: 10px; max-width: 80%; font-size: 14px; }
        .msg-client { background: #dcf8c6; align-self: flex-end; }
        .msg-admin { background: #fff; align-self: flex-start; }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <input type="text" id="l-cpf" placeholder="CPF (Acesso)" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <button class="btn" onclick="login()">ENTRAR</button>
        <p style="text-align:center; font-size:12px;">Novo por aqui? <span style="color:var(--orange); cursor:pointer" onclick="go('scr-reg')">Criar Conta</span></p>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:60px;" onclick="askAdmin()">PAINEL ADMINISTRATIVO</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Novo Cadastro</h2></div>
    <div class="container">
        <input type="text" id="r-nome" placeholder="Nome Completo" class="input-group">
        <input type="text" id="r-cpf" placeholder="CPF (Será seu login)" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="text" id="r-tel" placeholder="Celular (WhatsApp)" class="input-group" oninput="maskTel(this)" maxlength="15">
        
        <label style="font-size: 11px; color: #666; margin-left: 5px;">Data de Nascimento:</label>
        <input type="date" id="r-nasc" class="input-group">
        
        <input type="text" id="r-end" placeholder="Endereço Completo" class="input-group">
        <button class="btn" onclick="register()">CADASTRAR E ENTRAR</button>
        <button class="btn" style="background:#888" onclick="go('scr-login')">VOLTAR</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 Buscar no mercado..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="cat-bar" id="cat-loja"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-cart" class="app-screen">
    <div class="header"><h2>Cesta de Compras</h2></div>
    <div class="container" id="c-list" style="padding-bottom: 220px;"></div>
    <div id="c-foot" class="container" style="position:fixed; bottom:60px; background:white; width:100%; max-width:450px; left:50%; transform:translateX(-50%); border-top:1px solid #eee; display:none;">
        <h3 style="color:var(--green)">Total: R$ <span id="t-val">0,00</span></h3>
        <button class="btn" style="background:#25d366" onclick="sendZap()">FINALIZAR NO WHATSAPP</button>
    </div>
</section>

<section id="scr-suporte" class="app-screen">
    <div class="header"><h2>Suporte Online</h2></div>
    <div id="chat-cliente" class="chat-box"></div>
    <div class="container" style="position:fixed; bottom:60px; background:#f0f0f0; width:100%; max-width:450px; left:50%; transform:translateX(-50%); display:flex; padding:10px; gap:5px;">
        <input type="text" id="msg-input" placeholder="Dúvida ou problema..." style="flex:1; border-radius:20px; border:1px solid #ccc; padding:10px;">
        <button onclick="enviarMensagem()" style="background:var(--green); color:white; border:none; border-radius:50%; width:40px;">➔</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Gestão Medela</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('produtos')">Estoque</div>
        <div class="cat-item" id="tab-m" onclick="abaAdmin('mensagens')">Mensagens</div>
        <div class="cat-item" id="tab-u" onclick="abaAdmin('usuarios')">Nuvem Usuários</div>
    </div>
    <div class="container" id="adm-content"></div>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button class="nav-btn" onclick="go('scr-home')">MERCADO</button>
    <button class="nav-btn" onclick="go('scr-cart')">CESTA (<span id="count">0</span>)</button>
    <button class="nav-btn" onclick="go('scr-suporte')">SUPORTE</button>
    <button class="nav-btn" onclick="logout()">SAIR</button>
</nav>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    let db = JSON.parse(localStorage.getItem('m_db')) || [];
    let chats = JSON.parse(localStorage.getItem('m_chats')) || {};
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_is_admin') === 'true';
    let cart = [];
    let curCat = "Mercearia";

    window.onload = () => {
        if (isAdmin) { go('scr-admin'); abaAdmin('produtos'); }
        else if (user) { document.getElementById('u-name').innerText = `Olá, ${user.nome.split(' ')[0]}`; go('scr-home'); }
        else { go('scr-login'); }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id !== 'scr-login' && id !== 'scr-reg' && !isAdmin) ? 'flex' : 'none';
        if(id === 'scr-home') renderHome();
        if(id === 'scr-cart') renderCart();
        if(id === 'scr-suporte') renderChatCliente();
    }

    // --- ADMIN ABAS ---
    function abaAdmin(tipo) {
        const cont = document.getElementById('adm-content');
        document.getElementById('tab-p').classList.toggle('active-cat', tipo === 'produtos');
        document.getElementById('tab-m').classList.toggle('active-cat', tipo === 'mensagens');
        document.getElementById('tab-u').classList.toggle('active-cat', tipo === 'usuarios');
        
        if(tipo === 'produtos') {
            cont.innerHTML = `<div style="background:white; padding:15px; border-radius:10px;">
                <input type="text" id="a-nome" placeholder="Nome" class="input-group">
                <input type="text" id="a-foto" placeholder="URL Foto" class="input-group">
                <input type="number" id="a-preco" placeholder="Preço" class="input-group">
                <select id="a-cat" class="input-group">${CATS.map(c => `<option>${c}</option>`).join('')}</select>
                <select id="a-tipo" class="input-group"><option value="UN">UNIDADE</option><option value="KG">QUILO</option></select>
                <button class="btn" onclick="addProd()">ADICIONAR</button>
            </div><div id="a-list" style="margin-top:15px;"></div>
            <button class="btn" style="background:#333; margin-top:20px;" onclick="sairAdmin()">SAIR</button>`;
            renderAdmin();
        } else if(tipo === 'mensagens') {
            cont.innerHTML = `<h4>Conversas</h4><div id="adm-chats-list"></div><button class="btn" style="background:#333; margin-top:20px;" onclick="sairAdmin()">SAIR</button>`;
            renderListaChatsAdmin();
        } else {
            // ABA NUVEM DE USUÁRIOS
            cont.innerHTML = `<h4>Usuários Cadastrados</h4><div id="adm-users-list"></div><button class="btn" style="background:#333; margin-top:20px;" onclick="sairAdmin()">SAIR</button>`;
            renderUsuariosAdmin();
        }
    }

    function renderUsuariosAdmin() {
        const list = document.getElementById('adm-users-list');
        let html = "";
        for (let i = 0; i < localStorage.length; i++){
            let key = localStorage.key(i);
            if(key.startsWith("u_")) {
                let u = JSON.parse(localStorage.getItem(key));
                html += `<div class="admin-item" style="flex-direction:column; align-items:flex-start;">
                    <b>${u.nome}</b>
                    <small>Login (CPF): ${u.cpf}</small>
                    <small>Celular: ${u.tel}</small>
                    <small>Nasc: ${u.nascimento}</small>
                </div>`;
            }
        }
        list.innerHTML = html || "Nenhum usuário no banco.";
    }

    // --- LOJA E CARRINHO ---
    function setCatLoja(c) { curCat = c; document.getElementById('cat-loja').innerHTML = CATS.map(cat => `<div class="cat-item ${cat===curCat?'active-cat':''}" onclick="setCatLoja('${cat}')">${cat}</div>`).join(''); renderHome(); }
    function renderHome(s = "") {
        if(!document.getElementById('cat-loja').innerHTML) setCatLoja(curCat);
        const filtered = db.filter(p => p.cat === curCat && p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('p-grid').innerHTML = filtered.map(p => `<div class="card"><img src="${p.foto || 'https://via.placeholder.com/80'}"><b>${p.nome}</b><br><span style="color:var(--green)">R$ ${p.preco.toFixed(2)} / ${p.tipo}</span><div style="display:flex; gap:2px;"><input type="number" id="qty-${p.id}" value="1" step="${p.tipo==='KG'?'0.1':'1'}" style="width:40px;"><button class="btn" style="margin:0; padding:5px; font-size:10px;" onclick="addToCart(${p.id})">OK</button></div></div>`).join('');
    }
    function addToCart(id) { const p = db.find(x => x.id === id); const q = parseFloat(document.getElementById(`qty-${id}`).value); cart.push({...p, qty: q, total: p.preco * q}); document.getElementById('count').innerText = cart.length; alert("Adicionado!"); }
    function renderCart() {
        const cL = document.getElementById('c-list');
        if(cart.length===0) { cL.innerHTML="Vazio"; document.getElementById('c-foot').style.display="none"; return; }
        cL.innerHTML = cart.map((p,i) => `<div class="admin-item"><span>${p.nome}<br><small>${p.qty}${p.tipo}</small></span><b>R$ ${p.total.toFixed(2)} <span onclick="remCart(${i})" style="color:red; cursor:pointer">X</span></b></div>`).join('');
        document.getElementById('t-val').innerText = (cart.reduce((a,b)=>a+b.total,0)+7).toFixed(2);
        document.getElementById('c-foot').style.display="block";
    }
    function remCart(i) { cart.splice(i,1); renderCart(); document.getElementById('count').innerText = cart.length; }

    // --- SUPORTE ---
    function renderChatCliente() { const box = document.getElementById('chat-cliente'); const conv = chats[user.cpf] || []; box.innerHTML = conv.map(m => `<div class="msg ${m.from === 'user' ? 'msg-client' : 'msg-admin'}">${m.text}</div>`).join(''); box.scrollTop = box.scrollHeight; }
    function enviarMensagem() { const inp = document.getElementById('msg-input'); if(!inp.value) return; if(!chats[user.cpf]) chats[user.cpf] = []; chats[user.cpf].push({ from: 'user', text: inp.value }); localStorage.setItem('m_chats', JSON.stringify(chats)); inp.value = ""; renderChatCliente(); }
    function renderListaChatsAdmin() { document.getElementById('adm-chats-list').innerHTML = Object.keys(chats).map(cpf => `<div class="admin-item" onclick="abrirChatAdmin('${cpf}')" style="cursor:pointer"><b>CPF: ${cpf}</b> ➔</div>`).join('') || "Sem mensagens."; }
    function abrirChatAdmin(cpf) { const resp = prompt(`Histórico:\n${chats[cpf].map(m => (m.from==='user'?'CLI: ':'EU: ')+m.text).join('\n')}\n\nResposta:`); if(resp) { chats[cpf].push({ from: 'admin', text: resp }); localStorage.setItem('m_chats', JSON.stringify(chats)); renderListaChatsAdmin(); } }

    // --- FUNÇÕES GERAIS ---
    function addProd() { const n = document.getElementById('a-nome').value, p = parseFloat(document.getElementById('a-preco').value), c = document.getElementById('a-cat').value, t = document.getElementById('a-tipo').value; if(!n || !p) return; db.push({id: Date.now(), nome: n, foto: document.getElementById('a-foto').value, preco: p, cat: c, tipo: t}); localStorage.setItem('m_db', JSON.stringify(db)); abaAdmin('produtos'); }
    function renderAdmin(s="") { document.getElementById('a-list').innerHTML = db.filter(p=>p.nome.toLowerCase().includes(s.toLowerCase())).map(p=>`<div class="admin-item">${p.nome} <button onclick="remProd(${p.id})">X</button></div>`).join(''); }
    function remProd(id) { db = db.filter(p=>p.id!==id); localStorage.setItem('m_db', JSON.stringify(db)); renderAdmin(); }
    function askAdmin() { if(prompt("Senha ADM:")==="123") { localStorage.setItem('m_is_admin','true'); location.reload(); } }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function sairAdmin() { localStorage.removeItem('m_is_admin'); location.reload(); }
    function login() { const cpf = document.getElementById('l-cpf').value, s = localStorage.getItem(`u_${cpf}`); if(s) { user = JSON.parse(s); localStorage.setItem('m_user', JSON.stringify(user)); location.reload(); } else alert("CPF não encontrado."); }
    function register() { 
        const n = document.getElementById('r-nome').value, c = document.getElementById('r-cpf').value, t = document.getElementById('r-tel').value, d = document.getElementById('r-nasc').value, e = document.getElementById('r-end').value;
        if(!n || c.length < 14 || !t || !d) return alert("Preencha todos os campos!"); 
        const dados = {nome:n, cpf:c, tel:t, nascimento:d, end:e};
        localStorage.setItem(`u_${c}`, JSON.stringify(dados)); localStorage.setItem('m_user', JSON.stringify(dados)); location.reload(); 
    }
    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
    function maskTel(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/^(\d{2})(\d)/g,"($1) $2").replace(/(\d)(\d{4})$/,"$1-$2"); i.value = v; }
    function sendZap() { 
        let t = document.getElementById('t-val').innerText;
        let itens = cart.map(p => `- ${p.nome} (${p.qty}${p.tipo}): R$ ${p.total.toFixed(2)}`).join('\n');
        let msg = `*PEDIDO MEDELA*\n\n*Cliente:* ${user.nome}\n*CPF:* ${user.cpf}\n*Celular:* ${user.tel}\n*Endereço:* ${user.end}\n\n*Produtos:*\n${itens}\n\n*TOTAL: R$ ${t}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`); 
    }
</script>
</body>
</html>
