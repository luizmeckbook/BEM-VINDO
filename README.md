MERCADO MEDELA
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - V14 PRO</title>
    <style>
        :root { --green: #00a859; --orange: #f37021; --gray: #f4f4f4; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--gray); margin: 0; padding: 0; overflow-x: hidden; }
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; box-sizing: border-box; }
        .header { background: var(--orange); padding: 15px; color: white; text-align: center; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; }
        
        /* Câmera e Foto */
        .camera-box { width: 100%; background: #000; border-radius: 12px; overflow: hidden; margin-bottom: 10px; position: relative; height: 220px; border: 2px solid #ddd; }
        #video { width: 100%; height: 100%; object-fit: cover; transform: scaleX(-1); }
        #canvas { display: none; }
        .photo-preview { width: 100%; border-radius: 12px; border: 3px solid var(--green); margin-bottom: 10px; display: none; }
        .user-img-adm { width: 60px; height: 60px; border-radius: 50%; object-fit: cover; margin-right: 12px; border: 2px solid var(--orange); }

        /* Categorias e Grid */
        .cat-bar { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; scrollbar-width: none; }
        .cat-item { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 11px; font-weight: bold; cursor: pointer; }
        .cat-item.active-cat { background: var(--green); color: white; }
        .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 120px; }
        .card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); display: flex; flex-direction: column; justify-content: space-between; }
        .card img { width: 100%; height: 80px; object-fit: contain; margin-bottom: 5px; }

        /* Inputs e Botões */
        .btn { background: var(--green); color: white; border: none; padding: 12px; border-radius: 10px; font-weight: bold; width: 100%; cursor: pointer; margin-top: 5px; }
        .input-group { margin-bottom: 10px; width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-size: 14px; }
        
        .nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; left: 50%; transform: translateX(-50%); z-index: 1000; }
        .nav-btn { font-size: 11px; color: #aaa; border: none; background: none; font-weight: bold; cursor: pointer; }
        .admin-item { background: white; padding: 12px; margin-bottom: 8px; border-radius: 10px; display: flex; align-items: center; border-left: 5px solid var(--orange); box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
    </style>
</head>
<body>

<section id="scr-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--green); margin:0;">MEDELA</h1>
            <p style="color:var(--orange); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <input type="text" id="l-cpf" placeholder="CPF" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="l-pass" placeholder="Senha" class="input-group">
        <button class="btn" onclick="login()">ENTRAR AGORA</button>
        <p style="text-align:center; font-size:12px;">Novo por aqui? <span style="color:var(--orange); cursor:pointer; font-weight:bold;" onclick="go('scr-reg')">Criar minha Conta</span></p>
        <p style="text-align:center; font-size:10px; color:#ccc; margin-top:60px;" onclick="askAdmin()">PAINEL DO MESTRE</p>
    </div>
</section>

<section id="scr-reg" class="app-screen">
    <div class="header"><h2>Novo Cadastro</h2></div>
    <div class="container">
        <p style="font-size:12px; text-align:center; color:var(--orange); margin-bottom:10px;"><b>SEGURANÇA:</b> Tire uma selfie para validar seu rosto.</p>
        
        <div class="camera-box" id="cam-area">
            <video id="video" autoplay playsinline></video>
        </div>
        <img id="photo-preview" class="photo-preview">
        <canvas id="canvas"></canvas>
        
        <button id="btn-snap" class="btn" style="background:#444; margin-bottom:15px;" onclick="takeSnap()">TIRAR FOTO DO ROSTO</button>
        <button id="btn-retake" class="btn" style="background:#888; margin-bottom:15px; display:none;" onclick="startCam()">TIRAR OUTRA FOTO</button>

        <input type="text" id="r-nome" placeholder="Nome Completo" class="input-group">
        <input type="text" id="r-cpf" placeholder="CPF (Seu Login)" class="input-group" oninput="maskCPF(this)" maxlength="14">
        <input type="password" id="r-pass" placeholder="Crie uma Senha" class="input-group">
        <input type="text" id="r-tel" placeholder="Celular (WhatsApp)" class="input-group" oninput="maskTel(this)" maxlength="15">
        <label style="font-size: 11px; color: #666; margin-left: 5px;">Data de Nascimento:</label>
        <input type="date" id="r-nasc" class="input-group">
        <input type="text" id="r-end" placeholder="Endereço Completo para Entrega" class="input-group">
        
        <button class="btn" onclick="register()">FINALIZAR CADASTRO</button>
        <button class="btn" style="background:#888; margin-top:10px;" onclick="go('scr-login')">CANCELAR</button>
    </div>
</section>

<section id="scr-home" class="app-screen">
    <div class="header">
        <h3 id="u-name" style="margin:0">Olá!</h3>
        <input type="text" placeholder="🔍 Buscar produtos..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="cat-bar" id="cat-loja"></div>
    <div id="p-grid" class="grid"></div>
</section>

<section id="scr-cart" class="app-screen">
    <div class="header"><h2>Cesta de Compras</h2></div>
    <div class="container" id="c-list" style="padding-bottom: 220px;"></div>
    <div id="c-foot" class="container" style="position:fixed; bottom:60px; background:white; width:100%; max-width:450px; left:50%; transform:translateX(-50%); border-top:1px solid #eee; display:none;">
        <h3 style="color:var(--green)">Total: R$ <span id="t-val">0,00</span></h3>
        <button class="btn" style="background:#25d366" onclick="sendZap()">PEDIR NO WHATSAPP</button>
    </div>
</section>

<section id="scr-admin" class="app-screen">
    <div class="header"><h2>Gestão Medela</h2></div>
    <div class="cat-bar">
        <div class="cat-item active-cat" id="tab-p" onclick="abaAdmin('produtos')">Estoque</div>
        <div class="cat-item" id="tab-u" onclick="abaAdmin('usuarios')">Nuvem Usuários</div>
    </div>
    <div class="container" id="adm-content"></div>
</section>

<nav class="nav" id="bot-nav" style="display:none;">
    <button class="nav-btn" onclick="go('scr-home')">LOJA</button>
    <button class="nav-btn" onclick="go('scr-cart')">CESTA (<span id="count">0</span>)</button>
    <button class="nav-btn" onclick="logout()">SAIR</button>
</nav>

<script>
    const CATS = ["Mercearia", "Açougue", "Hortifrúti", "Bebidas", "Limpeza", "Padaria", "Fiambreria"];
    let db = JSON.parse(localStorage.getItem('m_db')) || [];
    let user = JSON.parse(localStorage.getItem('m_user'));
    let isAdmin = localStorage.getItem('m_is_admin') === 'true';
    let cart = [];
    let curCat = "Mercearia";
    let stream = null;
    let fotoData = null;

    window.onload = () => {
        if (isAdmin) { go('scr-admin'); abaAdmin('produtos'); }
        else if (user) { document.getElementById('u-name').innerText = `Olá, ${user.nome.split(' ')[0]}`; go('scr-home'); }
        else { go('scr-login'); }
    };

    function go(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('bot-nav').style.display = (id === 'scr-home' || id === 'scr-cart') ? 'flex' : 'none';
        
        if(id === 'scr-reg') startCam();
        else if(stream) { stream.getTracks().forEach(t => t.stop()); }

        if(id === 'scr-home') renderHome();
        if(id === 'scr-cart') renderCart();
    }

    // --- SISTEMA DE CÂMERA ---
    async function startCam() {
        fotoData = null;
        document.getElementById('cam-area').style.display = 'block';
        document.getElementById('photo-preview').style.display = 'none';
        document.getElementById('btn-snap').style.display = 'block';
        document.getElementById('btn-retake').style.display = 'none';
        try {
            stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "user" } });
            document.getElementById('video').srcObject = stream;
        } catch (e) { alert("Câmera não disponível."); }
    }

    function takeSnap() {
        const video = document.getElementById('video');
        const canvas = document.getElementById('canvas');
        canvas.width = video.videoWidth;
        canvas.height = video.videoHeight;
        canvas.getContext('2d').drawImage(video, 0, 0);
        fotoData = canvas.toDataURL('image/jpeg');
        
        document.getElementById('photo-preview').src = fotoData;
        document.getElementById('photo-preview').style.display = 'block';
        document.getElementById('cam-area').style.display = 'none';
        document.getElementById('btn-snap').style.display = 'none';
        document.getElementById('btn-retake').style.display = 'block';
        stream.getTracks().forEach(t => t.stop());
    }

    // --- LOJA E CARRINHO ---
    function setCatLoja(c) { curCat = c; document.getElementById('cat-loja').innerHTML = CATS.map(cat => `<div class="cat-item ${cat===curCat?'active-cat':''}" onclick="setCatLoja('${cat}')">${cat}</div>`).join(''); renderHome(); }
    
    function renderHome(s = "") {
        if(!document.getElementById('cat-loja').innerHTML) setCatLoja(curCat);
        const filtered = db.filter(p => p.cat === curCat && p.nome.toLowerCase().includes(s.toLowerCase()));
        document.getElementById('p-grid').innerHTML = filtered.map(p => `
            <div class="card">
                <img src="${p.foto || 'https://via.placeholder.com/80'}" onerror="this.src='https://via.placeholder.com/80'">
                <b style="font-size:11px;">${p.nome}</b>
                <span style="color:var(--green); font-size:13px;">R$ ${p.preco.toFixed(2)} / ${p.tipo}</span>
                <div style="display:flex; gap:3px;">
                    <input type="${p.tipo==='UN'?'number':'text'}" id="qty-${p.id}" value="1" style="width:45px; text-align:center; border:1px solid #ddd; border-radius:5px;">
                    <button class="btn" style="margin:0; padding:8px; font-size:10px;" onclick="addToCart(${p.id})">ADD</button>
                </div>
            </div>
        `).join('');
    }

    function addToCart(id) {
        const p = db.find(x => x.id === id);
        let val = document.getElementById(`qty-${id}`).value.replace(',', '.');
        let q = parseFloat(val);
        if (p.tipo === 'UN' && !Number.isInteger(q)) return alert("Este item só aceita quantidades inteiras.");
        if(isNaN(q) || q <= 0) return;
        cart.push({...p, qty: q, total: p.preco * q});
        document.getElementById('count').innerText = cart.length;
    }

    function renderCart() {
        const cL = document.getElementById('c-list');
        if(cart.length===0) { cL.innerHTML="<p style='text-align:center;'>Cesta vazia</p>"; document.getElementById('c-foot').style.display="none"; return; }
        cL.innerHTML = cart.map((p,i) => `<div class="admin-item"><span>${p.nome}<br><small>${p.qty} ${p.tipo}</small></span><b>R$ ${p.total.toFixed(2)} <span onclick="remCart(${i})" style="color:red; cursor:pointer; margin-left:10px">X</span></b></div>`).join('');
        document.getElementById('t-val').innerText = (cart.reduce((a,b)=>a+b.total,0)+7).toFixed(2);
        document.getElementById('c-foot').style.display="block";
    }

    function remCart(i) { cart.splice(i,1); renderCart(); document.getElementById('count').innerText = cart.length; }

    // --- ADMIN ---
    function abaAdmin(tipo) {
        const cont = document.getElementById('adm-content');
        document.getElementById('tab-p').classList.toggle('active-cat', tipo === 'produtos');
        document.getElementById('tab-u').classList.toggle('active-cat', tipo === 'usuarios');
        
        if(tipo === 'produtos') {
            cont.innerHTML = `<div style="background:white; padding:15px; border-radius:10px;">
                <input type="text" id="a-nome" placeholder="Nome" class="input-group">
                <input type="text" id="a-foto" placeholder="Link da Foto" class="input-group">
                <input type="number" id="a-preco" placeholder="Preço" class="input-group">
                <select id="a-cat" class="input-group">${CATS.map(c => `<option>${c}</option>`).join('')}</select>
                <select id="a-tipo" class="input-group"><option value="UN">UNIDADE</option><option value="KG">QUILO</option></select>
                <button class="btn" onclick="addProd()">SALVAR NO ESTOQUE</button>
            </div><div id="a-list" style="margin-top:15px;"></div>
            <button class="btn" style="background:#333; margin-top:20px;" onclick="sairAdmin()">SAIR DO ADM</button>`;
            renderAdmin();
        } else {
            cont.innerHTML = `<h4>Nuvem de Usuários (Senhas e Rostos)</h4><div id="adm-users-list"></div><button class="btn" style="background:#333; margin-top:20px;" onclick="sairAdmin()">VOLTAR</button>`;
            renderUsuariosAdmin();
        }
    }

    function renderUsuariosAdmin() {
        const list = document.getElementById('adm-users-list');
        let html = "";
        for (let i = 0; i < localStorage.length; i++){
            let k = localStorage.key(i);
            if(k.startsWith("u_")) {
                let u = JSON.parse(localStorage.getItem(k));
                html += `<div class="admin-item">
                    <img src="${u.rosto || 'https://via.placeholder.com/60'}" class="user-img-adm">
                    <div style="flex:1; font-size:12px;">
                        <b>${u.nome}</b><br>
                        <span>CPF: ${u.cpf} | <b style="color:var(--orange)">Senha: ${u.senha}</b></span><br>
                        <small>Celular: ${u.tel}</small>
                    </div>
                </div>`;
            }
        }
        list.innerHTML = html || "Sem usuários.";
    }

    function addProd() {
        const n = document.getElementById('a-nome').value, p = parseFloat(document.getElementById('a-preco').value), c = document.getElementById('a-cat').value, t = document.getElementById('a-tipo').value;
        if(!n || !p) return alert("Erro");
        db.push({id: Date.now(), nome: n, foto: document.getElementById('a-foto').value, preco: p, cat: c, tipo: t});
        localStorage.setItem('m_db', JSON.stringify(db));
        abaAdmin('produtos');
    }

    function renderAdmin() { document.getElementById('a-list').innerHTML = db.map(p=>`<div class="admin-item">${p.nome} (${p.tipo}) <button onclick="remProd(${p.id})" style="background:red; color:white; border:none; padding:5px; border-radius:5px;">X</button></div>`).join(''); }
    function remProd(id) { db = db.filter(p=>p.id!==id); localStorage.setItem('m_db', JSON.stringify(db)); renderAdmin(); }

    // --- AUTENTICAÇÃO ---
    function login() {
        const cpf = document.getElementById('l-cpf').value, pass = document.getElementById('l-pass').value;
        const s = localStorage.getItem(`u_${cpf}`);
        if(s) {
            const u = JSON.parse(s);
            if(u.senha === pass) { user = u; localStorage.setItem('m_user', JSON.stringify(u)); location.reload(); }
            else alert("Senha incorreta!");
        } else alert("CPF não cadastrado!");
    }

    function register() {
        const n = document.getElementById('r-nome').value, c = document.getElementById('r-cpf').value, p = document.getElementById('r-pass').value, t = document.getElementById('r-tel').value, d = document.getElementById('r-nasc').value, e = document.getElementById('r-end').value;
        if(!fotoData) return alert("Tire sua selfie primeiro!");
        if(!n || c.length < 14 || !p || !t) return alert("Preencha todos os dados!");
        
        const dados = {nome:n, cpf:c, senha:p, rosto:fotoData, tel:t, nascimento:d, end:e};
        localStorage.setItem(`u_${c}`, JSON.stringify(dados));
        localStorage.setItem('m_user', JSON.stringify(dados));
        location.reload();
    }

    function askAdmin() { if(prompt("Senha Mestra:")==="123") { localStorage.setItem('m_is_admin','true'); location.reload(); } }
    function logout() { localStorage.removeItem('m_user'); location.reload(); }
    function sairAdmin() { localStorage.removeItem('m_is_admin'); location.reload(); }
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
