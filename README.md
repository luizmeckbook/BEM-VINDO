<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - App</title>
    <style>
        :root { --primary-green: #00a859; --orange-main: #f37021; --bg-gray: #f4f4f4; --white: #ffffff; }
        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; color: #333; }
        
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; background: white; }
        .active { display: flex; flex-direction: column; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* FORMULÁRIOS */
        .logo-area { text-align: center; margin: 30px 0; }
        .logo-area h1 { color: var(--primary-green); margin: 0; }
        .logo-area span { color: var(--orange-main); }
        .input-group { margin-bottom: 12px; }
        .input-group label { display: block; font-size: 11px; color: #666; margin-bottom: 4px; font-weight: bold; }
        .input-group input, .input-group select { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 8px; background: #f9f9f9; box-sizing: border-box; font-size: 14px; }
        
        /* BOTÕES */
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 16px; border-radius: 8px; font-weight: bold; cursor: pointer; font-size: 15px; }
        .btn-add { background: var(--orange-main); padding: 10px; font-size: 12px; margin-top: 10px; }
        .btn-remove { background: #ff4444; padding: 5px 10px; border-radius: 5px; color: white; border: none; cursor: pointer; font-size: 10px; }

        /* HOME E CARRINHO */
        .header-app { background: linear-gradient(135deg, var(--orange-main), #ff8c42); padding: 40px 20px; color: white; border-radius: 0 0 25px 25px; }
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 20px; padding-bottom: 150px; }
        .item-card { background: white; border: 1px solid #eee; border-radius: 12px; padding: 15px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .item-card img { width: 60px; height: 60px; margin-bottom: 10px; object-fit: contain; }
        
        .cart-container { padding: 20px; margin-bottom: 200px; }
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 12px; border-bottom: 1px solid #eee; background: #fff; margin-bottom: 5px; border-radius: 8px; }
        
        /* PAGAMENTO */
        .payment-box { background: #f9f9f9; padding: 15px; border-radius: 10px; margin-top: 15px; border: 1px dashed #ccc; }
        #troco-box { display: none; margin-top: 10px; }

        /* NAV */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 12px 0; border-top: 1px solid #eee; z-index: 100; }
        .nav-item { font-size: 10px; color: #bbb; text-align: center; cursor: pointer; border: none; background: none; position: relative; }
        .nav-active { color: var(--primary-green); font-weight: bold; }
        .badge { background: red; color: white; border-radius: 50%; padding: 2px 6px; font-size: 10px; position: absolute; top: -5px; right: -10px; }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen active">
        <div class="container">
            <div class="logo-area"><h1>Medela<span> Supermercado</span></h1><p>Acesse sua conta</p></div>
            <div class="input-group"><label>CPF ou E-mail (Gmail)</label><input type="text" id="login-id" placeholder="Digite seu CPF ou Gmail"></div>
            <div class="input-group"><label>Senha</label><input type="password" id="login-pass" placeholder="••••••••"></div>
            <button class="btn-green" onclick="executarLogin()">Entrar</button>
            <p style="text-align:center; color: var(--primary-green); font-weight:bold; cursor:pointer; margin-top:25px;" onclick="irPara('screen-register')">Novo por aqui? Cadastre-se</p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2 style="color:var(--primary-green)">Criar Conta</h2>
            <div class="input-group"><label>* Nome Completo</label><input type="text" id="reg-nome" placeholder="Seu nome"></div>
            <div class="input-group"><label>* CPF</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)" placeholder="000.000.000-00"></div>
            <div class="input-group"><label>* Telefone</label><input type="text" id="reg-tel" placeholder="(00) 00000-0000"></div>
            <div class="input-group"><label>* Gmail</label><input type="email" id="reg-email" placeholder="seuemail@gmail.com"></div>
            <div class="input-group"><label>* Endereço de Entrega</label><input type="text" id="reg-endereco" placeholder="Rua, Número, Bairro"></div>
            <div class="input-group"><label>* Senha</label><input type="password" id="reg-senha" placeholder="Crie uma senha"></div>
            <button class="btn-green" onclick="finalizarCadastro()">Concluir Cadastro</button>
            <p style="text-align:center; cursor:pointer; font-size: 14px; margin-top: 15px;" onclick="irPara('screen-login')">Voltar ao Login</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <header class="header-app"><h2 id="user-display">Olá!</h2><p>O que vamos comprar hoje?</p></header>
        <div class="market-grid">
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png"><span class="item-name">Contra Filé KG</span><strong>R$ 39,90</strong><button class="btn-green btn-add" onclick="addAoCarrinho('Contra Filé', 39.90)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/415/415733.png"><span class="item-name">Banana KG</span><strong>R$ 5,50</strong><button class="btn-green btn-add" onclick="addAoCarrinho('Banana', 5.50)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/3014/3014545.png"><span class="item-name">Pão Francês KG</span><strong>R$ 14,90</strong><button class="btn-green btn-add" onclick="addAoCarrinho('Pão Francês', 14.90)">Adicionar</button></div>
            <div class="item-card"><img src="https://cdn-icons-png.flaticon.com/512/2674/2674486.png"><span class="item-name">Leite 1L</span><strong>R$ 6,20</strong><button class="btn-green btn-add" onclick="addAoCarrinho('Leite', 6.20)">Adicionar</button></div>
        </div>
    </section>

    <section id="screen-cart" class="app-screen">
        <header class="header-app"><h2>Carrinho</h2></header>
        <div class="cart-container">
            <div id="cart-list"></div>
            
            <div class="payment-box">
                <div class="input-group">
                    <label>FORMA DE PAGAMENTO</label>
                    <select id="pay-method" onchange="checkPayMethod()">
                        <option value="Cartão de Crédito">Cartão de Crédito</option>
                        <option value="Cartão de Débito">Cartão de Débito</option>
                        <option value="Pix">Pix</option>
                        <option value="Dinheiro">Dinheiro</option>
                    </select>
                </div>
                <div id="troco-box" class="input-group">
                    <label>TROCO PARA QUANTO?</label>
                    <input type="text" id="troco-valor" placeholder="Ex: 50,00">
                </div>
                <p style="font-size: 13px; color: #444; margin-top: 10px;">Taxa de Entrega: <strong>R$ 5,00</strong></p>
                <p style="font-size: 11px; color: #888;">Entregar em: <br><span id="display-address" style="color:var(--orange-main)"></span></p>
            </div>
        </div>
        
        <div class="container" style="position: fixed; bottom: 70px; background: white; width: 100%; max-width: 450px; border-top: 2px solid var(--primary-green);">
            <p style="font-size: 18px; margin: 10px 0;"><strong>Total: R$ <span id="cart-total">0,00</span></strong></p>
            <button class="btn-green" onclick="enviarPedido()">✅ Finalizar Pedido no WhatsApp</button>
        </div>
    </section>

    <nav class="bottom-nav">
        <button class="nav-item nav-active" onclick="irPara('screen-home')">PRODUTOS</button>
        <button class="nav-item" onclick="irPara('screen-cart')">CARRINHO <span id="cart-count" class="badge">0</span></button>
        <button class="nav-item" onclick="location.reload()">SAIR</button>
    </nav>

    <script>
        let carrinho = [];
        let usuarioLogado = null;
        const TAXA_ENTREGA = 5.00;

        function irPara(id) {
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            if(id === 'screen-cart' && usuarioLogado) {
                document.getElementById('display-address').innerText = usuarioLogado.endereco;
            }
        }

        function mascaraCPF(i) {
            let v = i.value.replace(/\D/g, "");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
            i.value = v;
        }

        function checkPayMethod() {
            const method = document.getElementById('pay-method').value;
            document.getElementById('troco-box').style.display = (method === 'Dinheiro') ? 'block' : 'none';
        }

        function finalizarCadastro() {
            const nome = document.getElementById('reg-nome').value;
            const cpf = document.getElementById('reg-cpf').value;
            const tel = document.getElementById('reg-tel').value;
            const email = document.getElementById('reg-email').value.toLowerCase();
            const endereco = document.getElementById('reg-endereco').value;
            const senha = document.getElementById('reg-senha').value;

            if(!nome || !cpf || !email || !senha) {
                alert("Preencha os campos obrigatórios!");
                return;
            }

            const dados = { nome, cpf, tel, email, endereco, senha };
            
            // Salva por CPF e por Email para permitir login duplo
            localStorage.setItem(cpf, JSON.stringify(dados));
            localStorage.setItem(email, JSON.stringify(dados));
            
            alert("Cadastro concluído com sucesso!");
            irPara('screen-login');
        }

        function executarLogin() {
            const id = document.getElementById('login-id').value.toLowerCase();
            const senha = document.getElementById('login-pass').value;
            const user = JSON.parse(localStorage.getItem(id));

            if (user && user.senha === senha) {
                usuarioLogado = user;
                document.getElementById('user-display').innerText = "Olá, " + user.nome.split(' ')[0];
                irPara('screen-home');
            } else { 
                alert("Dados de acesso incorretos!"); 
            }
        }

        function addAoCarrinho(nome, preco) {
            carrinho.push({ nome, preco });
            atualizarInterfaceCarrinho();
        }

        function removerDoCarrinho(index) {
            carrinho.splice(index, 1);
            atualizarInterfaceCarrinho();
        }

        function atualizarInterfaceCarrinho() {
            const list = document.getElementById('cart-list');
            const totalText = document.getElementById('cart-total');
            const countBadge = document.getElementById('cart-count');
            
            list.innerHTML = "";
            let subtotal = 0;

            carrinho.forEach((item, index) => {
                subtotal += item.preco;
                list.innerHTML += `
                    <div class="cart-item">
                        <span>${item.nome} - R$ ${item.preco.toFixed(2)}</span>
                        <button class="btn-remove" onclick="removerDoCarrinho(${index})">Remover</button>
                    </div>`;
            });

            const totalGeral = carrinho.length > 0 ? subtotal + TAXA_ENTREGA : 0;
            totalText.innerText = totalGeral.toFixed(2);
            countBadge.innerText = carrinho.length;
        }

        function enviarPedido() {
            if (carrinho.length === 0) { alert("Seu carrinho está vazio!"); return; }

            const formaPagamento = document.getElementById('pay-method').value;
            const troco = document.getElementById('troco-valor').value;

            let texto = `*NOVO PEDIDO - MEDELA SUPERMERCADO*\n\n`;
            texto += `*CLIENTE:* ${usuarioLogado.nome}\n`;
            texto += `*TEL:* ${usuarioLogado.tel}\n`;
            texto += `*CPF:* ${usuarioLogado.cpf}\n`;
            texto += `*ENDEREÇO:* ${usuarioLogado.endereco}\n`;
            texto += `----------------------------\n`;
            texto += `*PRODUTOS:*\n`;
            
            carrinho.forEach(item => {
                texto += `• ${item.nome}: R$ ${item.preco.toFixed(2)}\n`;
            });

            texto += `----------------------------\n`;
            texto += `*Taxa de Entrega:* R$ 5,00\n`;
            texto += `*TOTAL DO PEDIDO:* R$ ${document.getElementById('cart-total').innerText}\n`;
            texto += `*FORMA DE PAGAMENTO:* ${formaPagamento}\n`;
            
            if(formaPagamento === 'Dinheiro' && troco) {
                texto += `*Levar troco para:* R$ ${troco}\n`;
            }

            // Seu número: 21977126638
            const url = `https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(texto)}`;
            window.open(url, '_blank');
        }
    </script>
</body>
</html>
