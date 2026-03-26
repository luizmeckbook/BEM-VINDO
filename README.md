<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mercado Medela</title>
    <style>
        /* Reset básico */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
        }

        .container {
            width: 90%;
            max-width: 1200px;
            margin: auto;
        }

        /* Header */
        header {
            background: #0077b6;
            color: #fff;
            padding: 15px 0;
        }

        header .logo {
            height: 50px;
            float: left;
        }

        header nav {
            float: right;
        }

        header nav ul {
            list-style: none;
            display: flex;
        }

        header nav ul li {
            margin-left: 20px;
        }

        header nav ul li a {
            color: #fff;
            text-decoration: none;
            font-weight: bold;
        }

        /* Hero */
        .hero {
            background: #90e0ef;
            color: #03045e;
            text-align: center;
            padding: 100px 0;
        }

        .hero .btn {
            padding: 10px 20px;
            background: #0077b6;
            color: #fff;
            border: none;
            cursor: pointer;
            text-decoration: none;
            font-size: 1rem;
            border-radius: 5px;
        }

        /* Produtos */
        .produtos-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin: 40px 0;
        }

        .produto-card {
            border: 1px solid #ddd;
            padding: 20px;
            text-align: center;
            border-radius: 10px;
        }

        .produto-card img {
            width: 100%;
            height: auto;
            border-radius: 5px;
        }

        .produto-card button {
            margin-top: 10px;
            padding: 10px 20px;
            background: #0077b6;
            color: #fff;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }

        /* Contato */
        #contato form {
            display: flex;
            flex-direction: column;
            gap: 10px;
            max-width: 500px;
            margin: 20px auto;
        }

        #contato input, #contato textarea {
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
        }

        #contato button {
            padding: 10px;
            background: #0077b6;
            color: #fff;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }

        /* Footer */
        footer {
            background: #023e8a;
            color: #fff;
            text-align: center;
            padding: 20px 0;
        }

        /* Clear floats */
        .clearfix::after {
            content: "";
            display: table;
            clear: both;
        }
    </style>
</head>
<body>

    <!-- Header / Menu -->
    <header class="clearfix">
        <div class="container">
            <img src="img/logo.png" alt="Mercado Medela Logo" class="logo">
            <nav>
                <ul>
                    <li><a href="#home">Home</a></li>
                    <li><a href="#produtos">Produtos</a></li>
                    <li><a href="#sobre">Sobre</a></li>
                    <li><a href="#contato">Contato</a></li>
                </ul>
            </nav>
        </div>
    </header>

    <!-- Home Section -->
    <section id="home" class="hero">
        <div class="container">
            <h1>Bem-vindo ao Mercado Medela</h1>
            <p>O melhor lugar para encontrar produtos de qualidade.</p>
            <a href="#produtos" class="btn">Ver Produtos</a>
        </div>
    </section>

    <!-- Produtos Section -->
    <section id="produtos">
        <div class="container">
            <h2>Nossos Produtos</h2>
            <div class="produtos-grid">
                <div class="produto-card">
                    <img src="img/produto1.jpg" alt="Produto 1">
                    <h3>Produto 1</h3>
                    <p>Descrição do produto 1.</p>
                    <button>Comprar</button>
                </div>
                <div class="produto-card">
                    <img src="img/produto2.jpg" alt="Produto 2">
                    <h3>Produto 2</h3>
                    <p>Descrição do produto 2.</p>
                    <button>Comprar</button>
                </div>
                <div class="produto-card">
                    <img src="img/produto3.jpg" alt="Produto 3">
                    <h3>Produto 3</h3>
                    <p>Descrição do produto 3.</p>
                    <button>Comprar</button>
                </div>
            </div>
        </div>
    </section>

    <!-- Sobre Section -->
    <section id="sobre">
        <div class="container">
            <h2>Sobre Nós</h2>
            <p>Somos o Mercado Medela, com anos de experiência em oferecer produtos de qualidade e atendimento impecável aos nossos clientes.</p>
        </div>
    </section>

    <!-- Contato Section -->
    <section id="contato">
        <div class="container">
            <h2>Contato</h2>
            <form id="form-contato">
                <input type="text" placeholder="Nome" required>
                <input type="email" placeholder="Email" required>
                <textarea placeholder="Mensagem" required></textarea>
                <button type="submit">Enviar</button>
            </form>
        </div>
    </section>

    <!-- Footer -->
    <footer>
        <div class="container">
            <p>&copy; 2026 Mercado Medela. Todos os direitos reservados.</p>
        </div>
    </footer>

    <script>
        // Formulário de contato
        document.getElementById('form-contato').addEventListener('submit', function(e) {
            e.preventDefault();
            alert('Mensagem enviada com sucesso!');
        });
    </script>
</body>
</html>
