<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Medela Supermercado - App</title>
    <style>
        :root {
            --primary-green: #00a859;
            --orange-main: #f37021;
            --bg-gray: #f2f2f2;
            --white: #ffffff;
            --text-dark: #333;
        }

        body { font-family: 'Segoe UI', sans-serif; background-color: var(--bg-gray); margin: 0; padding: 0; }

        /* ESTILO DOS CONTAINERS PRINCIPAIS */
        .app-screen { display: none; min-height: 100vh; padding: 20px; box-sizing: border-box; background: white; text-align: center; }
        .screen-active { display: flex; flex-direction: column; align-items: center; justify-content: center; }

        .logo-box h1 { color: var(--primary-green); margin: 0; }
        .logo-box span { color: var(--orange-main); }
        .card-form { width: 100%; max-width: 400px; margin-top: 20px; }
        .input-group { text-align: left; margin-bottom: 15px; width: 100%; }
        .input-group label { display: block; font-size: 13px; color: #666; margin-bottom: 5px; }
        .input-group input { width: 100%; padding: 15px; border: 1px solid #ddd; border-radius: 10px; background: #f9f9f9; box-sizing: border-box; }
        .btn-main { background: var(--primary-green); color: white; border: none; width: 100%; padding: 15px; border-radius: 10px; font-weight: bold; cursor: pointer; margin-top: 10px; }
        .link-text { color: var(--primary-green); font-size: 14px; margin-top: 15px; cursor: pointer; text-decoration: underline; }

        /* AREA LOGADA E MENUS */
        #main-logged-in { text-align: left; background: var(--bg-gray); padding: 0; flex-direction: column; align-items: stretch; justify-content: flex-start; }
        .app-content { padding: 0 0 80px 0; display: none; } /* Area de conteúdo principal */
        .content-active { display: block; }

        .header-app { background: linear-gradient(135deg, var(--orange-main), #ff8c42); padding: 40px 20px; color: white; border-radius: 0 0 25px 25px; width: 100%; box-sizing: border-box;}
        .section-header { padding: 20px; font-weight: bold; font-size: 18px; }

        /* GRID DE PRODUTOS */
        .product-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; padding: 0 20px; }
        .product-item { background: white; border-radius: 12px; padding: 15px; text-align: center; }
        .product-item img { width: 70px; }

        /* BARRA DE NAVEGAÇÃO INFERIOR */
        .bottom-nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; justify-content: space-around; padding: 15px 0; border-top: 1px solid #eee; z-index: 100; box-shadow: 0 -2px 10px rgba(0,0,0,0.05); }
        .nav-item { display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 11px; color: #aaa; cursor: pointer; border: none; background: none; padding: 0;}
        .nav-active { color: var(--primary-green); font-weight: bold; }
        .nav-item-icon { font-size: 20px; margin-bottom: 3px;}

    </style>
</head>
<body>

    <section id="login-screen" class="app-screen screen-active">
        <div class="logo-box">
            <h1>Medela<span> Supermercado</span></h1>
            <p>Login ou Cadastre-se</p>
        </div>
        <div class="card-form">
            <div class="input-group">
                <label>CPF</label>
                <input type="text" id="login-cpf" placeholder="000.000.000-00">
            </div>
            <div class="input-group">
                <label>Senha</label>
                <input type="password" id="login-senha" placeholder="••••••••">
            </div>
            <button class="btn-main" onclick="tentarLogin()">Entrar</button>
            <p class="link-text" onclick="mudarTelaInicial('register-screen')">Não tem conta? Cadastre-se</p>
        </div>
    </section>

    <section id="register-screen" class="app-screen">
        <div class="logo-box">
            <h1>Criar<span> Conta</span></h1>
            <p>Cadastre-se no Medela Supermercado</p>
        </div>
        <div class="card-form">
            <div class="input-group">
                <label>Nome Completo</label>
                <input type="text" id="reg-nome" placeholder="Seu nome">
            </div>
            <div class="input-group">
                <label>CPF</label>
                <input type="text" id="reg-cpf" placeholder="000.000.000-00">
            </div>
            <div class="input-group">
                <label>Crie uma Senha</label>
                <input type="password" id="reg-senha" placeholder="Mínimo 6 dígitos">
            </div>
            <button class="btn-main" onclick="salvarCadastro()">Finalizar Cadastro</button>
            <p class="link-text" onclick="mudarTelaInicial('login-screen')">Já tenho conta. Voltar</p>
        </div>
    </section>

    <main id="main-logged-in" class="app-screen">
        <header class="header-app">
            <h2 id="boas-vindas" style="margin:0">Olá!</h2>
            <p>Bem-vindo ao Medela</p>
        </header>

        <section id="content-desconto" class="app-content content-active">
            <div class="section-header">Ofertas Ativadas</div>
            <div class="product-grid">
                <div class="product-item">
                    <img src="https://cdn-icons-png.flaticon.com/512/1134/1134444.png">
                    <p>Acém kg</p>
                    <strong>R$ 29,98</strong>
                </div>
                <div class="product-item">
                    <img src="https://cdn-icons-png.flaticon.com/512/1041/1041653.png">
                    <p>Frango kg</p>
                    <strong>R$ 11,98</strong>
                </div>
            </div>
        </section>

        <section id="content-jornal" class="app-content">
            <div class="section-header">Jornal de ofertas</div>
            <div style="text-align: center; color: #888; padding-top: 50px;">
                <p>Nenhum jornal publicado!</p>
                <p style="font-size: 12px;">Em breve novo jornal de ofertas, volte mais tarde.</p>
            </div>
        </section>

        <section id="content-pontos" class="app-content">
            <div class="section-header">Troca Pontos</div>
            <div style="text-align: center; color: #888; padding-top: 50px;">
                <p>Você não tem pontos suficientes.</p>
            </div>
        </section>

        <section id="content-conta" class="app-content">
            <div class="section-header">Minha conta</div>
            <div style="padding: 20px; text-align: center;">
                <p>Perfil e Configurações</p>
                <button class="btn-main" style="background: red;" onclick="location.reload()">Sair do App</button>
            </div>
        </section>

        <nav class="bottom-nav">
            <button class="nav-item nav-active" id="btn-desconto" onclick="mudarConteudo('content-desconto', 'btn-desconto')">
                <div class="nav-item-icon">%</div>
                <div>Desconto</div>
            </button>
            <button class="nav-item" id="btn-jornal" onclick="mudarConteudo('content-jornal', 'btn-jornal')">
                <div class="nav-item-icon">📄</div>
                <div>Jornal</div>
            </button>
            <button class="nav-item" id="btn-pontos" onclick="mudarConteudo('content-pontos', 'btn-pontos')">
                <div class="nav-item-icon">🎟️</div>
                <div>Pontos</div>
            </button>
            <button class="nav-item" id="btn-conta" onclick="mudarConteudo('content-conta', 'btn-conta')">
                <div class="nav-item-icon">👤</div>
                <div>Conta</div>
            </button>
        </nav>
    </main>

    <script>
        // --- LÓGICA DE TROCA DE TELAS (Login/Cadastro) ---
        function mudarTelaInicial(id) {
            document.querySelectorAll('.app-screen').forEach(s => s.classList.remove('screen-active'));
            document.getElementById(id).classList.add('screen-active');
        }

        // --- LÓGICA DE CADASTRO E LOGIN (Verificação de Senha) ---
        function salvarCadastro() {
            const nome = document.getElementById('reg-nome').value;
            const cpf = document.getElementById('reg-cpf').value;
            const senha = document.getElementById('reg-senha').value;

            if(!nome || !cpf || !senha) { alert("Preencha tudo!"); return; }
            
