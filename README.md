<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meu Terê - App</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; background-color: #fff; margin: 0; padding: 0; color: var(--text-dark); }
        
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; }
        .active { display: flex; flex-direction: column; }
        .container { padding: 25px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* LOGIN & CADASTRO (Estilo Imagem) */
        .logo-main { text-align: center; margin: 40px 0 20px 0; }
        .logo-main img { width: 220px; }
        .welcome-text { text-align: center; color: #666; font-size: 15px; margin-bottom: 30px; }
        
        .input-group { margin-bottom: 18px; position: relative; }
        .input-group label { display: block; font-size: 13px; color: #444; margin-bottom: 6px; }
        .input-group label span { color: red; }
        .input-group input, .input-group select { width: 100%; padding: 16px; border: 1px solid #eee; border-radius: 12px; background: #f5f5f5; box-sizing: border-box; font-size: 15px; outline: none; }
        
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 16px; border-radius: 10px; font-weight: bold; cursor: pointer; font-size: 16px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .btn-outline { background: white; color: var(--primary-green); border: 1px solid #ddd; margin-top: 15px; padding: 12px; border-radius: 8px; font-weight: 500; cursor: pointer; width: 100%; }

        /* HEADER LARANJA (Estilo Imagem 1000785235) */
        .header-orange { background: var(--orange-header); padding: 45px 25px 35px 25px; color: white; border-radius: 0 0 35px 35px; }
        .header-orange h2 { margin: 0; font-size: 24px; }
        .header-orange p { margin: 8px 0 0 0; opacity: 0.9; font-size: 14px; }

        /* GRID DE PRODUTOS */
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; padding: 20px; padding-bottom: 120px; }
        .item-card { background: white; border-radius: 15px; padding: 15px; text-align: center; box-shadow: 0 4px 12px rgba(0,0,0,0.06); border: 1px solid #f0f0f0; }
        .item-card img { width: 80px; height: 80px; margin-bottom: 12px; object-fit: contain; }
        .item-card .name { font-size: 14px; font-weight: 600; height: 35px; display: block; margin-bottom: 5px; }
        .item-card .price { font-size: 16px; color: #000; font-weight: bold; display: block; margin-bottom: 10px; }
        .btn-add-item { background: var(--orange-header); color: white; border: none; width: 100%; padding: 10px; border-radius: 8px; cursor: pointer; font-weight: bold; text-transform: lowercase; }

        /* CARRINHO */
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 15px; border-bottom: 1px solid #eee; margin-bottom: 10px; background: #fff; border-radius: 12px; }
        .footer-cart { position: fixed; bottom: 70px; background: white; width: 100%; max-width: 450px; border-top: 1px solid #eee; padding: 20px; box-sizing: border-box; }

        /* NAVEGAÇÃO INFERIOR */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 10px 0; border-top: 1px solid #eee; z-index: 1000; }
        .nav-item { font-size: 11px; color: #aaa; text-align: center; cursor: pointer; border: none; background: none; font-weight: 500; text-transform: uppercase; width: 33%; }
        .nav-active { color: var(--primary-green); }
        .cart-badge { background: #e31b1b; color: white; border-radius: 50%; padding: 2px 6px; font-size: 10px; margin-left: 3px; }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen active">
        <div class="container">
            <div class="logo-main">
                <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR_X3GvC6R2p7zE0yLp-7X8v7p9C7N9n0W1w&s" alt="Meu Terê">
            </div>
            <p class="welcome-text">Preencha com seus dados para entrar.</p>
            
            <div class="input-group">
                <label><span>*</span>CPF/CNPJ</label>
                <input type="text" id="login-id" placeholder="000.000.000-00">
            </div>
            <div class="input-group">
                <label><span>*</span>Senha</label>
                <input type="password" id="login-pass" placeholder="••••••••">
            </div>
            
            <button class="btn-green" onclick="executarLogin()">Entrar</button>
            <button class="btn-outline">Esqueceu sua senha?</button>
            
            <p style="text-align:center; margin-top:30px; font-size:14px; color:var(--primary-green); font-weight:600; cursor:pointer;" onclick="irPara('screen-register')">Cadastre-se</p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2 style="color:var(--primary-green); margin-top:0;">Cadastrar</h2>
            <div class="input-group"><label><span>*</span>CPF/CNPJ</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)"></div>
            <div class="input-group"><label><span>*</span>Nome Completo</label><input type="text" id="reg-nome"></div>
            <div class="input-group"><label><span>*</span>Telefone</label><input type="text" id="reg-tel" placeholder="(00) 00000-0000"></div>
            <div class="input-group"><label><span>*</span>E-mail</label><input type="email" id="reg-email"></div>
            <div class="input-group"><label><span>*</span>Endereço</label><input type="text" id="reg-endereco"></div>
            <div class="input-group"><label><span>*</span>Senha</label><input type="password" id="reg-senha"></div>
            
            <button class="btn-green" style="margin-top:10px;" onclick="finalizarCadastro()">Próximo</button>
            <p style="text-align:center; margin-top:20px; cursor:pointer; color:#666;" onclick="irPara('screen-login')">Voltar para o Login</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <div class="header-orange">
            <h2 id="user-display">Olá!</h2>
            <p>Escolha seus produtos abaixo:</p>
        </div>
        
        <div class="market-grid">
            <div class="item-card">
                <img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png">
                <span class="name">Contra File KG</span>
                <span class="price">R$ 39,90</span>
                <button class="btn-add-item" onclick="addAoCarrinho('Contra File', 39.90)">adicionar</button>
            </div>
            <div class="item-card">
                <img src="https://cdn-icons-png.flaticon.com/512/415/415733.png">
                <span class="name">Banana Prata KG</span>
                <span class="price">R$ 5,50</span>
                <button class="btn-add-item" onclick="addAoCarrinho('Banana Prata', 5.50)">adicionar</button>
            </div>
            <div class="item-card">
                <img src="https://cdn-icons-png.flaticon.com/512/3014/3014545.png">
                <span class="name">Pão Francês KG</span>
                <span class="price">R$ 14,90</span>
                <button class="btn-add-item" onclick="addAoCarrinho('Pão Francês', 14.90)">adicionar</button>
            </div>
            <div class="item-card">
                <img src="https://cdn-icons-png.flaticon.com/512/2674/2674486.png">
                <span class="name">Leite Integral 1L</span>
                <span class="price">R$ 6,20</span>
                <button class="btn-add-item" onclick="addAoCarrinho('Leite Integral', 6.20)">adicionar</button>
            </div>
        </div>
    </section>

    <section id="screen-cart" class="app-screen">
        <div class="header-orange">
            <h2>Carrinho</h2>
            <p>Finalize sua compra</p>
        </div>
        <div class="container" id="cart-list" style="padding-bottom: 180px;">
            <p style="text-align:center; color:#999; margin-top:50px;">Seu carrinho está vazio.</p>
        </div>
        
        <div class="footer-cart" id="cart-footer" style="display:none;">
            <div style="margin-bottom:15px;">
                <label style="font-size:12px; font-weight:bold;">FORMA DE PAGAMENTO</label>
                <select id="pay-method" style="width:100%; padding:10px; margin-top:5px; border-radius:8px; border:1px solid #ddd;">
                    <option>Cartão de Crédito</option>
                    <option>Cartão de Débito</option>
                    <option>Pix</option>
                    <option>Dinheiro</option>
                </select>
            </div>
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
                <span style="font-weight:bold;">Total:</span>
                <span style="font-size:20px; font-weight:bold; color:var(--primary-green);">R$ <span id="cart-total">0,00</span></span>
            </div>
            <button class="btn-green" onclick="enviarPedido()">Finalizar no WhatsApp</button>
        </div>
    </section>

    <nav class="bottom-nav">
        <button class="nav-item nav-active" id="nav-login-btn" onclick="irPara('screen-login')">LOGIN</button>
        <button class="nav-item" id="nav-cart-btn" onclick="irPara('screen-cart')">CARRINHO<span id="cart-count" class="cart-badge">0</span></button>
        <button class="nav-item" onclick="location.reload()">SAIR</button>
    </nav>

    <script>
        let carrinho = [];
        let usuarioLogado = null;

        function irPara(id) {
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('nav-active'));
            
            document.getElementById(id).classList.add('active');
            
            // Gerencia os nomes da barra de navegação
            if(id === 'screen-home' || id === 'screen-login') {
                document.getElementById('nav-login-btn').classList.add('nav-active');
            } else if(id === 'screen-cart') {
                document.getElementById('nav-cart-btn').classList.add('nav-active');
                renderizarCarrinho();
            }
        }

        function executarLogin() {
            const id = document.getElementById('login-id').value;
            const user = JSON.parse(localStorage.getItem(id));
            
            // Para teste, se o campo não estiver vazio, deixa passar
            if(id.length > 3) {
                usuarioLogado = user || { nome: "Cliente", endereco: "Não informado", tel: "2199999999" };
                document.getElementById('user-display').innerText = "Olá, " + usuarioLogado.nome.split(' ')[0] + "!";
                document.getElementById('nav-login-btn').innerText = "PRODUTOS";
                irPara('screen-home');
            } else {
                alert("Por favor, insira seus dados.");
            }
        }

        function finalizarCadastro() {
            const cpf = document.getElementById('reg-cpf').value;
            const nome = document.getElementById('reg-nome').value;
            if(!cpf || !nome) return alert("Preencha os campos!");
            
            const dados = {
                nome: nome,
                cpf: cpf,
                tel: document.getElementById('reg-tel').value,
                endereco: document.getElementById('reg-endereco').value
            };
            localStorage.setItem(cpf, JSON.stringify(dados));
            alert("Cadastro realizado!");
            irPara('screen-login');
        }

        function mascaraCPF(i) {
            let v = i.value.replace(/\D/g, "");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
            i.value = v;
        }

        function addAoCarrinho(nome, preco) {
            if(!usuarioLogado) {
                alert("Faça login para adicionar produtos!");
                irPara('screen-login');
                return;
            }
            carrinho.push({ nome, preco });
            document.getElementById('cart-count').innerText = carrinho.length;
        }

        function renderizarCarrinho() {
            const list = document.getElementById('cart-list');
            const footer = document.getElementById('cart-footer');
            
            if(carrinho.length === 0) {
                list.innerHTML = '<p style="text-align:center; color:#999; margin-top:50px;">Seu carrinho está vazio.</p>';
                footer.style.display = "none";
                return;
            }

            list.innerHTML = "";
            let total = 0;
            carrinho.forEach((item, index) => {
                total += item.preco;
                list.innerHTML += `
                    <div class="cart-item">
                        <div><strong>${item.nome}</strong><br><small>R$ ${item.preco.toFixed(2)}</small></div>
                        <button onclick="removerItem(${index})" style="background:none; border:none; color:red; font-weight:bold;">Remover</button>
                    </div>`;
            });

            document.getElementById('cart-total').innerText = (total + 5).toFixed(2); // +5 de taxa exemplo
            footer.style.display = "block";
        }

        function removerItem(index) {
            carrinho.splice(index, 1);
            document.getElementById('cart-count').innerText = carrinho.length;
            renderizarCarrinho();
        }

        function enviarPedido() {
            let msg = `*NOVO PEDIDO - MEU TERÊ*\n\nCliente: ${usuarioLogado.nome}\nEndereço: ${usuarioLogado.endereco}\n\n*ITENS:*\n`;
            carrinho.forEach(i => msg += `- ${i.nome}: R$ ${i.preco.toFixed(2)}\n`);
            msg += `\n*TOTAL: R$ ${document.getElementById('cart-total').innerText}*`;
            
            window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
        }
    </script>
</body>
</html>
