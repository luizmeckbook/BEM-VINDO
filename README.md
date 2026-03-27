MEDELA SUPERMERCADO
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MEDELA SUPERMERCADO - PRO V3</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg-gray); margin: 0; color: var(--text-dark); }
        
        .app-screen { display: none; min-height: 100vh; flex-direction: column; width: 100%; }
        .active { display: flex !important; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        .header-orange { background: var(--orange-header); padding: 15px 20px; color: white; border-radius: 0 0 20px 20px; position: sticky; top: 0; z-index: 100; text-align: center;}
        .bottom-nav { position: fixed; bottom: 0; width: 100%; max-width: 450px; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 1000; left: 50%; transform: translateX(-50%); }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; border: none; background: none; font-weight: bold; cursor: pointer; }
        .nav-active { color: var(--primary-green); }

        .cat-container { display: flex; overflow-x: auto; padding: 10px; gap: 8px; background: white; white-space: nowrap; }
        .cat-pill { padding: 8px 16px; background: #f0f0f0; border-radius: 20px; font-size: 12px; font-weight: bold; cursor: pointer; border: 1px solid #ddd; }
        .cat-pill.active-cat { background: var(--primary-green); color: white; border-color: var(--primary-green); }

        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; padding: 15px; padding-bottom: 150px; }
        .item-card { background: white; border-radius: 12px; padding: 10px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        
        .input-group { margin-bottom: 12px; }
        .input-group label { display: block; font-size: 11px; font-weight: bold; margin-bottom: 4px; }
        .input-group input, .input-group select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; width: 100%; }
        
        .list-item { background: white; padding: 12px; margin-bottom: 8px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; border-left: 5px solid var(--orange-header); }
    </style>
</head>
<body>

<section id="screen-login" class="app-screen">
    <div class="container">
        <div style="text-align: center; margin: 40px 0;">
            <h1 style="color: var(--primary-green); margin:0; font-size: 40px;">MEDELA</h1>
            <p style="color:var(--orange-header); font-weight:bold;">SUPERMERCADO</p>
        </div>
        <div class="input-group">
            <label>CPF</label>
            <input type="text" id="login-cpf" placeholder="000.000.000-00" oninput="mascaraCPF(this)" maxlength="14">
        </div>
        <button class="btn-green" onclick="fazerLogin()">ENTRAR</button>
        <p style="text-align:center; margin-top:20px;">Novo por aqui? <b style="color:var(--orange-header); cursor:pointer" onclick="irPara('screen-register')">Cadastre-se</b></p>
        <p style="text-align:center; margin-top:50px; font-size:11px; color:#ccc; cursor:pointer;" onclick="acessoAdmin()">⚙️ ADMINISTRAÇÃO</p>
    </div>
</section>

<section id="screen-register" class="app-screen">
    <div class="header-orange"><h2>Criar Conta</h2></div>
    <div class="container">
        <div class="input-group"><label>Nome</label><input type="text" id="reg-nome"></div>
        <div class="input-group"><label>CPF</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)" maxlength="14"></div>
        <div class="input-group"><label>Endereço</label><input type="text" id="reg-end"></div>
        <button class="btn-green" onclick="salvarCadastro()">CADASTRAR</button>
        <button class="btn-green" style="background:#888; margin-top:10px;" onclick="irPara('screen-login')">VOLTAR</button>
    </div>
</section>

<section id="screen-home" class="app-screen">
    <div class="header-orange">
        <h3 id="user-display" style="margin:0">Olá!</h3>
        <input type="text" placeholder="Pesquisar produto..." style="width:100%; margin-top:10px; padding:10px; border-radius:8px; border:none;" oninput="renderHome(this.value)">
    </div>
    <div class="cat-container" id="cat-list"></div>
    <div class="market-grid" id="product-grid"></div>
</section>

<section id="screen-cart" class="app-screen">
    <div class="header-orange"><h2>Carrinho</h2></div>
    <div class="container" style="padding-bottom: 200px;" id="cart-items-list"></div>
    <div id="cart-footer" class="container" style="position:fixed; bottom:60px; background:white; width:100%; border-top:2px solid #eee; display:none; left:50%; transform:translateX(-50%);">
        <h3 style="color:var(--primary-green)">TOTAL: <span id="total-val" style="float:right">0,00</span></h3>
        <button class="btn-green" onclick="enviarWhatsApp()">FINALIZAR NO WHATSAPP</button>
    </div>
</section>

<section id="screen-admin" class="app-screen">
    <div class="header-orange"><h2>Painel Admin</h2></div>
    <div class="container" style="padding-bottom: 50px;">
        <div style="background:white; padding:15px; border-radius:10px; border:1px solid #ddd; margin-bottom:20px;">
            <div class="input-group"><input type="text" id="adm-nome" placeholder="Nome do Produto"></div>
            <div class="input-group">
                <select id="adm-cat-select"></select>
            </div>
            <div class="input-group">
                <select id="adm-tipo">
                    <option value="un">Unidade (UN)</option>
                    <option value="kg">Quilo (KG)</option>
                </select>
            </div>
            <div class="input-group"><input type="number" id="adm-preco" placeholder="Preço (Ex: 5.99)"></div>
            <button class="btn-green" onclick="admAdicionar()">SALVAR PRODUTO</button>
        </div>
        <input type="text" placeholder="🔍 Buscar para excluir..." oninput="renderAdmin(this.value)" style="width:100%; padding:12px; margin-bottom:15px; border-radius:8px; border:1px solid var(--orange-header);">
        <div id="adm-lista-geral"></div>
        <button class="btn-green" style="background:#333; margin-top:20px" onclick="sairAdmin()">SAIR E VOLTAR AO LOGIN</button>
    </div>
</section>

<nav class="bottom-nav" id="main-nav" style="display:none;">
    <button class="nav-item" id="n-h" onclick="irPara('screen-home')">LOJA</button>
    <button class="nav-item" id="n-c" onclick="irPara('screen-cart')">CARRINHO (<span id="count">0</span>)</button>
    <button class="nav-item" onclick="logout()">SAIR</button>
</nav>

<script>
    const SENHA_MESTRA = "Medela7690";
    const CATEGORIAS = ["Mercearia", "Açougue", "Hortifrúti", "Bebida s", "Limpeza", "Padaria", "Fiambreria"];
    
    let estoque = JSON.parse(localStorage.getItem('medela_estoque_v3')) || [];
    let carrinho = [];
    let userAtivo = JSON.parse(localStorage.getItem('medela_user_logado'));
    let isAdmin = localStorage.getItem('admin_session') === 'true'; // Verifica se o ADM está logado
    let categoriaSelecionada = CATEGORIAS[0];

    window.onload = () => {
        popularSelectCategorias();
        // Lógica de Redirecionamento ao atualizar
        if (isAdmin) {
            irPara('screen-admin');
        } else if (userAtivo) {
            logarInterface();
            irPara('screen-home');
        } else {
            irPara('screen-login');
        }
    };

    function popularSelectCategorias() {
        const sel = document.getElementById('adm-cat-select');
        sel.innerHTML = CATEGORIAS.map(c => `<option value="${c}">${c}</option>`).join('');
    }

    function irPara(id) {
        document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
        document.getElementById('main-nav').style.display = (id === 'screen-home' || id === 'screen-cart') ? 'flex' : 'none';
        
        if(id === 'screen-home') { renderCategorias(); renderHome(); }
        if(id === 'screen-cart') renderCart();
        if(id === 'screen-admin') renderAdmin();
    }

    // --- CADASTRO / LOGIN ---
    function salvarCadastro() {
        const n = document.getElementById('reg-nome').value;
        const c = document.getElementById('reg-cpf').value;
        const e = document.getElementById('reg-end').value;
        if(!n || c.length < 14) return alert("Dados incompletos!");
        localStorage.setItem(`cliente_${c}`, JSON.stringify({nome: n, cpf: c, end: e}));
        alert("Cadastrado! Faça o login.");
        irPara('screen-login');
    }

    function fazerLogin() {
        const c = document.getElementById('login-cpf').value;
        const dados = localStorage.getItem(`cliente_${c}`);
        if(dados) {
            userAtivo = JSON.parse(dados);
            localStorage.setItem('medela_user_logado', JSON.stringify(userAtivo));
            logarInterface();
            irPara('screen-home');
        } else { alert("CPF não encontrado!"); }
    }

    function logarInterface() { document.getElementById('user-display').innerText = `Olá, ${userAtivo.nome.split(' ')[0]}`; }

    // --- LOJA ---
    function renderCategorias() {
        const container = document.getElementById('cat-list');
        container.innerHTML = CATEGORIAS.map(c => `
            <div class="cat-pill ${categoriaSelecionada === c ? 'active-cat' : ''}" onclick="setCat('${c}')">${c}</div>
        `).join('');
    }

    function setCat(c) {
        categoriaSelecionada = c;
        renderCategorias();
        renderHome();
    }

    function renderHome(busca = "") {
        const grid = document.getElementById('product-grid');
        const filtrados = estoque.filter(p => 
            p.cat === categoriaSelecionada && 
            p.n.toLowerCase().includes(busca.toLowerCase())
        );

        grid.innerHTML = filtrados.map(p => `
            <div class="item-card">
                <b style="font-size:14px; display:block; height:35px">${p.n}</b>
                <span style="color:var(--primary-green); font-weight:bold">R$ ${p.p.toFixed(2)}</span>
                <div style="display:flex; align-items:center; justify-content:center; gap:5px; margin:8px 0">
                    <input type="number" id="qty-${p.id}" value="1" step="${p.tipo==='kg'?'0.1':'1'}" style="width:50px; text-align:center">
                    <small>${p.tipo}</small>
                </div>
                <button onclick="addCart(${p.id})" style="background:var(--orange-header); color:white; border:none; padding:8px; border-radius:5px; width:100%; cursor:pointer">ADICIONAR</button>
            </div>
        `).join('');
    }

    function addCart(id) {
        const p = estoque.find(x => x.id === id);
        const q = parseFloat(document.getElementById(`qty-${id}`).value);
        if(q <= 0) return;
        carrinho.push({...p, qtd: q, sub: p.p * q});
        document.getElementById('count').innerText = carrinho.length;
        alert("Adicionado!");
    }

    function renderCart() {
        const container = document.getElementById('cart-items-list');
        if(carrinho.length === 0) {
            container.innerHTML = "<center style='margin-top:50px'>Carrinho vazio</center>";
            document.getElementById('cart-footer').style.display = "none";
            return;
        }
        let total = 0;
        container.innerHTML = carrinho.map((it, i) => {
            total += it.sub;
            return `
                <div class="list-item">
                    <div><b>${it.n}</b><br><small>${it.qtd}${it.tipo} x R$ ${it.p.toFixed(2)}</small></div>
                    <div style="text-align:right"><b>R$ ${it.sub.toFixed(2)}</b><br><span onclick="remCart(${i})" style="color:red; font-size:11px">Remover</span></div>
                </div>
            `;
        }).join('');
        document.getElementById('total-val').innerText = "R$ " + (total + 7).toFixed(2);
        document.getElementById('cart-footer').style.display = "block";
    }

    function remCart(i) {
        carrinho.splice(i,1);
        document.getElementById('count').innerText = carrinho.length;
        renderCart();
    }

    // --- ADMIN (CORRIGIDO) ---
    function acessoAdmin() {
        if(prompt("Senha:") === SENHA_MESTRA) {
            localStorage.setItem('admin_session', 'true'); // Cria a sessão
            isAdmin = true;
            irPara('screen-admin');
        }
        else alert("Senha incorreta");
    }

    function sairAdmin() {
        localStorage.removeItem('admin_session'); // Deleta a sessão
        isAdmin = false;
        location.reload(); // Recarrega para voltar ao login limpo
    }

    function renderAdmin(busca = "") {
        const lista = document.getElementById('adm-lista-geral');
        const filtrados = estoque.filter(p => p.n.toLowerCase().includes(busca.toLowerCase()));
        lista.innerHTML = filtrados.map(p => `
            <div class="list-item">
                <span><b>${p.n}</b> (${p.cat})<br>R$ ${p.p.toFixed(2)} / ${p.tipo}</span>
                <button onclick="admRemover(${p.id})" style="background:red; color:white; border:none; padding:8px; border-radius:5px">EXCLUIR</button>
            </div>
        `).join('');
    }

    function admAdicionar() {
        const n = document.getElementById('adm-nome').value;
        const c = document.getElementById('adm-cat-select').value;
        const t = document.getElementById('adm-tipo').value;
        const p = parseFloat(document.getElementById('adm-preco').value);
        if(!n || !p) return alert("Preencha tudo!");
        estoque.push({id: Date.now(), n, cat: c, tipo: t, p});
        localStorage.setItem('medela_estoque_v3', JSON.stringify(estoque));
        renderAdmin();
        alert("Salvo!");
    }

    function admRemover(id) {
        if(confirm("Excluir?")) {
            estoque = estoque.filter(p => p.id !== id);
            localStorage.setItem('medela_estoque_v3', JSON.stringify(estoque));
            renderAdmin();
        }
    }

    // --- UTIL ---
    function mascaraCPF(i) {
        let v = i.value.replace(/\D/g, "");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d)/, "$1.$2");
        v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
        i.value = v;
    }

    function logout() { localStorage.removeItem('medela_user_logado'); location.reload(); }

    function enviarWhatsApp() {
        let msg = `*PEDIDO - MEDELA*\nCliente: ${userAtivo.nome}\nEndereço: ${userAtivo.end}\n\n`;
        carrinho.forEach(i => msg += `- ${i.n} (${i.qtd}${i.tipo}): R$ ${i.sub.toFixed(2)}\n`);
        msg += `\n*TOTAL: ${document.getElementById('total-val').innerText}*`;
        window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
    }
</script>
</body>
</html>
