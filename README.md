<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V15 VIP</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        /* Câmera e Chat */
        .camera-box { width: 100%; background: #000; border-radius: 12px; height: 200px; overflow: hidden; margin-bottom: 10px; }
        #video { width: 100%; height: 100%; object-fit: cover; transform: scaleX(-1); }
        .chat-area { flex: 1; overflow-y: auto; padding: 15px; background: #e5ddd5; display: flex; flex-direction: column; gap: 8px; margin-bottom: 70px; }
        .msg { padding: 8px 12px; border-radius: 10px; max-width: 80%; font-size: 14px; position: relative; }
        .msg-u { background: #dcf8c6; align-self: flex-end; }
        .msg-a { background: #fff; align-self: flex-start; }
        .msg img { max-width: 100%; border-radius: 5px; margin-top: 5px; }

        /* Interface Loja */
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 100px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .card img { width: 100%; height: 80px; object-fit: contain; }
        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        
        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        .admin-item { background: white; padding: 10px; margin-bottom: 8px; border-radius: 10px; display: flex; align-items: center; border-left: 5px solid var(--orange); }
        .user-img-adm { width: 50px; height: 50px; border-radius: 50%; object-fit: cover; margin-right: 10px; }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 30px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <input type="text" id="l-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="l-pass" placeholder="Senha" class="input-group">
        <button class="btn" onclick="login()">ENTRAR</button>
        <p style="text-align:center; font-size:12px;">Esqueceu ou é novo? <span style="color:var(--orange); cursor:pointer" onclick="go('scr-reg')">Criar Conta</span></p>
        <button class="btn" style="background:#444; margin-top:10px;" onclick="abrirSuporte('visitante')">SUPORTE / AJUDA</button>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:40px;" onclick="askAdmin()">PAINEL ADM</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Cadastro Seguro</h2></div>
    <div class="container">
        <div class="camera-box" id="cam-area"><video id="video" autoplay playsinline></video></div>
        <img id="photo-preview" style="width:100%; border-radius:12px; display:none; margin-bottom:10px;">
        <button id="btn-snap" class="btn" style="background:#444; margin-bottom:10px;" onclick="takeSnap()">CAPTURAR ROSTO</button>
        
        <input type="text" id="r-nome" placeholder="Nome Completo" class="input-group">
        <input type="text" id="r-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="r-pass" placeholder="Crie uma Senha" class="input-group">
        <input type="text" id="r-tel" placeholder="Celular" class="input-group" oninput="maskTel(this)" maxlength="15">
        <input type="date" id="r-nasc" class="input-group">
        <input type="text" id="r-end" placeholder="Endereço" class="input-group">
        <button class="btn" onclick="register()">FINALIZAR</button>
        <button class="btn" style="background:#888; margin-top:5px;" onclick="go('scr-login')">VOLTAR</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 Buscar..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div id="cat-loja" class="cat-bar"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-suporte" class="app-screen">
    <div class="header"><h2 id="sup-title">Suporte Medela</h2></div>
    <div id="chat-box" class="chat-area"></div>
    <div class="container" style="position:fixed; bottom:60px; background:#f0f0f0; width:100%; max-width:450px; left:50%; transform:translateX(-50%); display:flex; padding:10px; gap:5px;">
        <label style="background:var(--orange); color:white; padding:10px; border-radius:50%; cursor:pointer;">
            📷 <input type="file" accept="image/*" style="display:none" onchange="enviarMsg(true, this)">
        </label>
        <input type="text" id="msg-input" placeholder="Digite aqui..." style="flex:1; border-radius:20px; border:1px solid #ccc; padding:10px;">
        <button onclick="enviarMsg(false)" style="background:var(--green); color:white; border:none; border-radius:50%; width:40px;">➔</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Painel Mestre</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('produtos')">Estoque</div>
        <div class="cat-item" id="tab-u" onclick="abaAdmin('usuarios')">Nuvem Usuários</div>
        <div class="cat-item" id="tab-s" onclick="abaAdmin('suporte')">Chats</div>
    </div>
    <div class="container" id="adm-content"></div>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button class="nav-btn" onclick="go('scr-home')">LOJA</button>
    <button class="nav-btn" onclick="go('scr-cart')">CESTA (<span id="count">0</span>)</button>
    <button class="nav-btn" onclick="abrirSuporte()">SUPORTE</button>
    <button class="nav-btn" onclick="logout()">SAIR</button>
</nav>

<canvas id="canvas" style="display:none;"></canvas>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    let db = JSON.parse(localStorage.getItem('m_db')) || [];
    let chats = JSON.parse(localStorage.getItem('m_chats')) || {};
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_is_admin') === 'true';
    let curChatID = "";
    let stream = null;
    let fotoRosto = "";

    window.onload = () => {
        if (isAdmin) { go('scr-admin'); abaAdmin('produtos'); }
        else if (user) { go('scr-home'); }
        else { go('scr-login'); }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id === 'scr-home' || id === 'scr-suporte') ? 'flex' : 'none';
        if(id === 'scr-reg') startCam();
        if(id === 'scr-home') { renderHome(); document.getElementById('u-name').innerText = "Olá, " + user.nome.split(' ')[0]; }
    }

    // --- CÂMERA ---
    async function startCam() {
        try { stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } }); document.getElementById('video').srcObject = stream; } 
        catch (e) { alert("Câmera não permitida."); }
    }
    function takeSnap() {
        const v = document.getElementById('video'); const c = document.getElementById('canvas');
        c.width = v.videoWidth; c.height = v.videoHeight;
        c.getContext('2d').drawImage(v, 0, 0);
        fotoRosto = c.toDataURL('image/jpeg');
        document.getElementById('photo-preview').src = fotoRosto;
        document.getElementById('photo-preview').style.display = 'block';
        document.getElementById('cam-area').style.display = 'none';
        stream.getTracks().forEach(t => t.stop());
    }

    // --- SUPORTE ---
    function abrirSuporte(tipo) {
        curChatID = tipo === 'visitante' ? 'visitante_'+Date.now() : user.cpf;
        document.getElementById('sup-title').innerText = "Suporte Medela";
        go('scr-suporte');
        renderChat();
    }

    function enviarMsg(isFoto, input) {
        const txt = document.getElementById('msg-input');
        if(!chats[curChatID]) chats[curChatID] = [];
        
        if(isFoto) {
            const reader = new FileReader();
            reader.onload = (e) => {
                chats[curChatID].push({ from: 'user', img: e.target.result });
                salvarChat();
            };
            reader.readAsDataURL(input.files[0]);
        } else if(txt.value) {
            chats[curChatID].push({ from: 'user', text: txt.value });
            txt.value = "";
            salvarChat();
        }
    }

    function salvarChat() { localStorage.setItem('m_chats', JSON.stringify(chats)); renderChat(); }

    function renderChat() {
        const box = document.getElementById('chat-box');
        const msgs = chats[curChatID] || [];
        box.innerHTML = msgs.map(m => `
            <div class="msg ${m.from==='user'?'msg-u':'msg-a'}">
                ${m.text ? m.text : ''}
                ${m.img ? `<img src="${m.img}">` : ''}
            </div>
        `).join('');
        box.scrollTop = box.scrollHeight;
    }

    // --- ADMIN ---
    function abaAdmin(tipo) {
        const cont = document.getElementById('adm-content');
        if(tipo === 'usuarios') {
            cont.innerHTML = `
                <input type="text" placeholder="🔍 Buscar Nome ou CPF..." class="input-group" oninput="renderUsersAdm(this.value)">
                <div id="list-u"></div>`;
            renderUsersAdm();
        } else if(tipo === 'suporte') {
            cont.innerHTML = `<h4>Conversas Ativas</h4><div id="list-s"></div>`;
            renderChatsAdm();
        } else {
            cont.innerHTML = `<h4>Estoque</h4><button class="btn" onclick="alert('Cadastre no código V14')">Novo Item</button><div id="list-p"></div>`;
            renderProdsAdm();
        }
    }

    function renderUsersAdm(filtro = "") {
        const list = document.getElementById('list-u');
        let html = "";
        for (let i = 0; i < localStorage.length; i++) {
            let k = localStorage.key(i);
            if(k.startsWith("u_")) {
                let u = JSON.parse(localStorage.getItem(k));
                if(u.nome.toLowerCase().includes(filtro.toLowerCase()) || u.cpf.includes(filtro)) {
                    html += `<div class="admin-item">
                        <img src="${u.rosto}" class="user-img-adm">
                        <div style="font-size:12px;"><b>${u.nome}</b><br>CPF: ${u.cpf} | Senha: <b>${u.senha}</b></div>
                    </div>`;
                }
            }
        }
        list.innerHTML = html || "Nenhum usuário encontrado.";
    }

    function renderChatsAdm() {
        document.getElementById('list-s').innerHTML = Object.keys(chats).map(id => `
            <div class="admin-item" onclick="responderChat('${id}')" style="cursor:pointer">
                Chat: ${id} ➔
            </div>
        `).join('') || "Sem mensagens.";
    }

    function responderChat(id) {
        const msg = prompt("Sua resposta (pode ser texto):");
        if(msg) {
            chats[id].push({ from: 'admin', text: msg });
            localStorage.setItem('m_chats', JSON.stringify(chats));
            renderChatsAdm();
        }
    }

    // --- BASE ---
    function login() {
        const c = document.getElementById('l-cpf').value, p = document.getElementById('l-pass').value;
        const s = localStorage.getItem(`u_${c}`);
        if(s && JSON.parse(s).senha === p) { user = JSON.parse(s); localStorage.setItem('m_user', s); location.reload(); }
        else alert("Dados inválidos.");
    }

    function register() {
        const c = document.getElementById('r-cpf').value, n = document.getElementById('r-nome').value, p = document.getElementById('r-pass').value;
        if(!fotoRosto || !c || !p) return alert("Preencha tudo e tire a foto!");
        const d = {nome:n, cpf:c, senha:p, rosto:fotoRosto, tel:document.getElementById('r-tel').value};
        localStorage.setItem(`u_${c}`, JSON.stringify(d));
        user = d; localStorage.setItem('m_user', JSON.stringify(d)); location.reload();
    }

    function maskCPF(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d)/,"$1.$2").replace(/(\d{3})(\d{1,2})$/,"$1-$2"); i.value = v; }
    function maskTel(i) { let v = i.value.replace(/\D/g,""); v = v.replace(/^(\d{2})(\d)/g,"($1) $2").replace(/(\d)(\d{4})$/,"$1-$2"); i.value = v; }
    function askAdmin() { if(prompt("Senha:")==="123") { localStorage.setItem('m_is_admin','true'); location.reload(); } }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function sairAdmin() { localStorage.removeItem('m_is_admin'); location.reload(); }
</script>
</body>
</html>
