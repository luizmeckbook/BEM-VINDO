<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meu Terê - App</title>
    <style>
        :root { --primary-green: #00a859; --orange-header: #f37021; --bg-gray: #f4f4f4; --text-dark: #333; }
        body { font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; color: var(--text-dark); }
        
        .app-screen { display: none; min-height: 100vh; width: 100%; box-sizing: border-box; }
        .active { display: flex; flex-direction: column; }
        .container { padding: 20px; max-width: 450px; margin: 0 auto; width: 100%; box-sizing: border-box; }
        
        /* LOGIN & CADASTRO */
        .logo-main { text-align: center; margin: 30px 0; }
        .logo-main img { width: 180px; }
        .welcome-text { text-align: center; color: #666; font-size: 14px; margin-bottom: 25px; }
        .input-group { margin-bottom: 15px; }
        .input-group label { display: block; font-size: 12px; color: #444; margin-bottom: 5px; }
        .input-group input { width: 100%; padding: 14px; border: 1px solid #ddd; border-radius: 10px; background: #fff; box-sizing: border-box; }
        .btn-green { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; font-size: 16px; }

        /* HEADER */
        .header-orange { background: var(--orange-header); padding: 30px 20px; color: white; border-radius: 0 0 25px 25px; position: sticky; top: 0; z-index: 100; }
        .search-box { margin-top: 15px; width: 100%; padding: 10px; border-radius: 8px; border: none; outline: none; }

        /* CATEGORIAS */
        .category-scroll { display: flex; overflow-x: auto; padding: 10px 20px; gap: 10px; background: #fff; white-space: nowrap; -webkit-overflow-scrolling: touch; }
        .cat-pill { padding: 8px 15px; background: #eee; border-radius: 20px; font-size: 13px; cursor: pointer; border: 1px solid #ddd; }
        .cat-active { background: var(--primary-green); color: white; border-color: var(--primary-green); }

        /* GRID DE PRODUTOS */
        .market-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 15px; padding-bottom: 120px; }
        .item-card { background: white; border-radius: 12px; padding: 12px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .item-card .name { font-size: 13px; font-weight: 600; display: block; height: 32px; margin: 8px 0; overflow: hidden; }
        .item-card .price { font-size: 15px; color: var(--primary-green); font-weight: bold; display: block; margin-bottom: 8px; }
        .btn-add-item { background: var(--orange-header); color: white; border: none; width: 100%; padding: 8px; border-radius: 6px; cursor: pointer; font-weight: bold; font-size: 12px; }

        /* CARRINHO */
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 12px; background: #fff; border-radius: 10px; margin-bottom: 8px; }
        .footer-cart { position: fixed; bottom: 65px; background: white; width: 100%; max-width: 450px; border-top: 1px solid #eee; padding: 15px; box-sizing: border-box; box-shadow: 0 -2px 10px rgba(0,0,0,0.05); }

        /* NAVEGAÇÃO */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 10px 0; border-top: 1px solid #eee; z-index: 1000; }
        .nav-item { font-size: 10px; color: #aaa; text-align: center; cursor: pointer; border: none; background: none; font-weight: bold; width: 33%; }
        .nav-active { color: var(--primary-green); }
        .cart-badge { background: #e31b1b; color: white; border-radius: 50%; padding: 2px 5px; font-size: 9px; margin-left: 3px; }
    </style>
</head>
<body>

    <section id="screen-login" class="app-screen active">
        <div class="container">
            <div class="logo-main"><img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR_X3GvC6R2p7zE0yLp-7X8v7p9C7N9n0W1w&s"></div>
            <p class="welcome-text">Entre para ver nossos +500 produtos.</p>
            <div class="input-group"><label>CPF/CNPJ</label><input type="text" id="login-id" placeholder="000.000.000-00"></div>
            <div class="input-group"><label>Senha</label><input type="password" id="login-pass" placeholder="••••••••"></div>
            <button class="btn-green" onclick="executarLogin()">Entrar</button>
            <p style="text-align:center; margin-top:20px; font-size:14px; color:var(--primary-green); cursor:pointer;" onclick="irPara('screen-register')">Criar nova conta</p>
        </div>
    </section>

    <section id="screen-register" class="app-screen">
        <div class="container">
            <h2 style="color:var(--primary-green);">Cadastrar</h2>
            <div class="input-group"><label>CPF/CNPJ</label><input type="text" id="reg-cpf" oninput="mascaraCPF(this)"></div>
            <div class="input-group"><label>Nome Completo</label><input type="text" id="reg-nome"></div>
            <div class="input-group"><label>Endereço de Entrega</label><input type="text" id="reg-endereco" placeholder="Rua, número, bairro"></div>
            <div class="input-group"><label>Senha</label><input type="password" id="reg-senha"></div>
            <button class="btn-green" onclick="finalizarCadastro()">Cadastrar</button>
            <p style="text-align:center; margin-top:20px; color:#666;" onclick="irPara('screen-login')">Voltar</p>
        </div>
    </section>

    <section id="screen-home" class="app-screen">
        <div class="header-orange">
            <h2 id="user-display">Olá!</h2>
            <input type="text" class="search-box" id="search-input" placeholder="O que você procura hoje?" oninput="filtrarProdutos()">
        </div>
        
        <div class="category-scroll" id="cat-list">
            </div>
        
        <div class="market-grid" id="product-grid">
            </div>
    </section>

    <section id="screen-cart" class="app-screen">
        <div class="header-orange"><h2>Meu Carrinho</h2></div>
        <div class="container" id="cart-list" style="padding-bottom: 180px;"></div>
        
        <div class="footer-cart" id="cart-footer" style="display:none;">
            <div style="display:flex; justify-content:space-between; margin-bottom:10px;">
                <span style="font-weight:bold;">Total:</span>
                <span style="font-size:18px; font-weight:bold; color:var(--primary-green);">R$ <span id="cart-total">0,00</span></span>
            </div>
            <button class="btn-green" onclick="enviarPedido()">Pedir no WhatsApp</button>
        </div>
    </section>

    <nav class="bottom-nav">
        <button class="nav-item nav-active" id="nav-main-btn" onclick="botaoPrincipalMenu()">PRODUTOS</button>
        <button class="nav-item" id="nav-cart-btn" onclick="irPara('screen-cart')">CARRINHO<span id="cart-count" class="cart-badge">0</span></button>
        <button class="nav-item" onclick="location.reload()">SAIR</button>
    </nav>

    <script>
        // BANCO DE DADOS DE PRODUTOS
        const produtosMaster = [
            { cat: "Mercearia Seca", itens: ["Arroz Agulhinha", "Arroz Integral", "Arroz Parbolizado", "Feijão Carioca", "Feijão Preto", "Feijão Fradinho", "Lentilha", "Grão-de-bico", "Farinha de Trigo", "Farinha de Milho", "Farinha de Mandioca", "Fubá", "Aveia", "Espaguete", "Macarrão Parafuso", "Macarrão Penne", "Lasanha Massa", "Nhoque", "Macarrão Instantâneo", "Talharim", "Óleo de Soja", "Óleo de Canola", "Óleo de Girassol", "Azeite Extra-virgem", "Vinagre Branco", "Milho Enlatado", "Ervilha", "Palmito", "Extrato de Tomate", "Atum em Lata", "Sardinha em Lata", "Azeitona", "Ketchup", "Mostarda", "Maionese", "Sal Refinado", "Sal Grosso", "Açúcar Cristal", "Açúcar Mascavo", "Café em Pó", "Café Solúvel", "Cápsulas Café", "Chá Preto", "Mate", "Biscoito Cream-cracker", "Rosquinhas", "Granola", "Leite Condensado", "Creme de Leite", "Achocolatado", "Geleia"] },
            { cat: "Hortifrúti", itens: ["Banana", "Laranja", "Mamão", "Melão", "Maçã", "Uva", "Abacaxi", "Kiwi", "Morango", "Abacate", "Tomate", "Cebola", "Alho", "Batata", "Cenoura", "Mandioca", "Abobrinha", "Berinjela", "Pimentão", "Pepino", "Alface Crespa", "Couve", "Rúcula", "Brócolis", "Repolho", "Salsinha", "Cebolinha", "Coentro", "Manjericão"] },
            { cat: "Açougue", itens: ["Picanha KG", "Alcatra KG", "Contrafilé KG", "Coxão Mole KG", "Patinho KG", "Carne Moída", "Lombo Suíno", "Pernil Suíno", "Costela Suína", "Linguiça Toscana", "Frango Inteiro", "Peito de Frango", "Coxa de Frango", "Asas de Frango", "Ovos Brancos 30un", "Tilápia", "Camarão", "Bacalhau"] },
            { cat: "Laticínios", itens: ["Leite Integral", "Leite Desnatado", "Leite sem Lactose", "Iogurte Natural", "Iogurte Frutas", "Queijo Muçarela", "Queijo Prato", "Queijo Minas", "Parmesão Ralado", "Requeijão", "Manteiga", "Margarina"] },
            { cat: "Bebidas", itens: ["Água s/ Gás", "Água c/ Gás", "Coca-Cola 2L", "Guaraná 2L", "Suco Concentrado", "Néctar Uva", "Água de Coco", "Cerveja Lata", "Vinho Tinto", "Vinho Branco", "Espumante", "Cachaça", "Vodca", "Energético"] },
            { cat: "Limpeza", itens: ["Sabão em Pó", "Sabão Líquido", "Amaciante", "Alvejante", "Detergente", "Esponja", "Desinfetante", "Água Sanitária", "Multiuso", "Saco de Lixo", "Papel Higiênico", "Papel Toalha"] },
            { cat: "Higiene", itens: ["Sabonete Barra", "Sabonete Líquido", "Xampu", "Condicionador", "Pasta de Dente", "Escova Dental", "Desodorante", "Absorvente", "Lâmina Barbear"] },
            { cat: "Congelados", itens: ["Batata Palito", "Pizza Congelada", "Lasanha Congelada", "Hambúrguer", "Nuggets", "Pão de Queijo", "Sorvete Pote", "Açaí"] },
            { cat: "Padaria", itens: ["Pão Francês", "Pão de Forma", "Torrada", "Bolo Pronto", "Mel", "Geleia Morango"] },
            { cat: "Outros", itens: ["Ração Cão", "Ração Gato", "Areia Higiênica", "Pilhas AA", "Vela", "Fósforo"] }
        ];

        let carrinho = [];
        let usuarioLogado = null;
        let categoriaAtual = "Mercearia Seca";

        function botaoPrincipalMenu() {
            if (usuarioLogado) irPara('screen-home');
            else irPara('screen-login');
        }

        function irPara(id) {
            if (usuarioLogado && (id === 'screen-login' || id === 'screen-register')) id = 'screen-home';
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('nav-active'));
            document.getElementById(id).classList.add('active');
            if(id === 'screen-home') {
                document.getElementById('nav-main-btn').classList.add('nav-active');
                renderizarCategorias();
                renderizarProdutos();
            } else if(id === 'screen-cart') {
                document.getElementById('nav-cart-btn').classList.add('nav-active');
                renderizarCarrinho();
            }
            window.scrollTo(0,0);
        }

        function renderizarCategorias() {
            const list = document.getElementById('cat-list');
            list.innerHTML = produtosMaster.map(c => `
                <div class="cat-pill ${categoriaAtual === c.cat ? 'cat-active' : ''}" 
                     onclick="setCat('${c.cat}')">${c.cat}</div>
            `).join('');
        }

        function setCat(cat) {
            categoriaAtual = cat;
            renderizarCategorias();
            renderizarProdutos();
        }

        function renderizarProdutos(filtro = "") {
            const grid = document.getElementById('product-grid');
            grid.innerHTML = "";
            
            produtosMaster.forEach(c => {
                if (filtro === "" && c.cat !== categoriaAtual) return;
                
                c.itens.forEach(item => {
                    if (filtro !== "" && !item.toLowerCase().includes(filtro.toLowerCase())) return;
                    
                    // Preço aleatório simulado entre 5 e 40 para visualização
                    const precoFake = (Math.random() * (40 - 5) + 5).toFixed(2);
                    
                    grid.innerHTML += `
                        <div class="item-card">
                            <span class="name">${item}</span>
                            <span class="price">R$ ${precoFake}</span>
                            <button class="btn-add-item" onclick="addAoCarrinho('${item}', ${precoFake})">ADICIONAR</button>
                        </div>
                    `;
                });
            });
        }

        function filtrarProdutos() {
            const termo = document.getElementById('search-input').value;
            renderizarProdutos(termo);
        }

        function executarLogin() {
            const id = document.getElementById('login-id').value;
            if(id.length > 3) {
                const salvo = JSON.parse(localStorage.getItem(id));
                usuarioLogado = salvo || { nome: "Cliente", endereco: "Não informado" };
                document.getElementById('user-display').innerText = "Olá, " + usuarioLogado.nome.split(' ')[0] + "!";
                irPara('screen-home');
            }
        }

        function finalizarCadastro() {
            const cpf = document.getElementById('reg-cpf').value;
            const nome = document.getElementById('reg-nome').value;
            if(cpf && nome) {
                localStorage.setItem(cpf, JSON.stringify({nome, cpf, endereco: document.getElementById('reg-endereco').value}));
                alert("Cadastro OK!"); irPara('screen-login');
            }
        }

        function addAoCarrinho(nome, preco) {
            if(!usuarioLogado) return irPara('screen-login');
            carrinho.push({ nome, preco });
            document.getElementById('cart-count').innerText = carrinho.length;
        }

        function renderizarCarrinho() {
            const list = document.getElementById('cart-list');
            const footer = document.getElementById('cart-footer');
            if(carrinho.length === 0) {
                list.innerHTML = '<p style="text-align:center; color:#999; margin-top:50px;">Carrinho vazio.</p>';
                footer.style.display = "none";
                return;
            }
            let total = 0;
            list.innerHTML = carrinho.map((item, index) => {
                total += item.preco;
                return `
                    <div class="cart-item">
                        <div><strong>${item.nome}</strong><br><small>R$ ${item.preco.toFixed(2)}</small></div>
                        <button onclick="removerItem(${index})" style="color:red; border:none; background:none;">Remover</button>
                    </div>`;
            }).join('');
            document.getElementById('cart-total').innerText = total.toFixed(2);
            footer.style.display = "block";
        }

        function removerItem(index) {
            carrinho.splice(index, 1);
            document.getElementById('cart-count').innerText = carrinho.length;
            renderizarCarrinho();
        }

        function enviarPedido() {
            let msg = `*NOVO PEDIDO - MEU TERÊ*\n\n*Cliente:* ${usuarioLogado.nome}\n*Endereço:* ${usuarioLogado.endereco}\n\n`;
            carrinho.forEach(i => msg += `- ${i.nome}: R$ ${i.preco.toFixed(2)}\n`);
            msg += `\n*TOTAL: R$ ${document.getElementById('cart-total').innerText}*`;
            window.open(`https://api.whatsapp.com/send?phone=5521977126638&text=${encodeURIComponent(msg)}`);
        }

        function mascaraCPF(i) {
            let v = i.value.replace(/\D/g, "");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d)/, "$1.$2");
            v = v.replace(/(\d{3})(\d{1,2})$/, "$1-$2");
            i.value = v;
        }
    </script>
</body>
</html>
