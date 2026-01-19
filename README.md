<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Controle de Qualidade</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #f4f6f8;
}
.hidden { display: none; }

.login {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}
.login-box {
    background: #fff;
    padding: 25px;
    border-radius: 8px;
    width: 280px;
    box-shadow: 0 0 10px rgba(0,0,0,.1);
}
.login-box h2 {
    text-align: center;
}
.login-box input {
    width: 100%;
    padding: 8px;
    margin: 8px 0;
}
.login-box button {
    width: 100%;
    padding: 10px;
    background: #2ecc71;
    border: none;
    color: #fff;
    font-weight: bold;
    cursor: pointer;
}

/* Dashboard */
.header {
    background: #c0392b;
    color: #fff;
    padding: 12px;
    text-align: center;
    font-size: 18px;
}
.container {
    padding: 15px;
}
.card {
    background: #fff;
    padding: 15px;
    border-radius: 8px;
    margin-bottom: 15px;
}
.card h3 {
    margin-top: 0;
}
input, button {
    padding: 8px;
    width: 100%;
    margin-top: 5px;
}
button {
    background: #3498db;
    color: #fff;
    border: none;
    cursor: pointer;
}
.alert {
    background: #f1c40f;
    padding: 10px;
    border-radius: 6px;
    margin-bottom: 10px;
}
.danger {
    background: #e74c3c;
    color: #fff;
}
table {
    width: 100%;
    border-collapse: collapse;
}
th, td {
    border-bottom: 1px solid #ccc;
    padding: 6px;
    text-align: center;
}
small {
    color: #555;
}
</style>
</head>
<body>

<!-- LOGIN -->
<div class="login" id="login">
    <div class="login-box">
        <h2>Login</h2>
        <input id="user" placeholder="Usuário">
        <input id="pass" type="password" placeholder="Senha">
        <button onclick="login()">Entrar</button>
    </div>
</div>

<!-- SISTEMA -->
<div id="app" class="hidden">
    <div class="header">Controle de Qualidade</div>

    <div class="container">

        <div class="card">
            <h3>Adicionar Produto</h3>
            <input id="nome" placeholder="Nome do Produto">
            <input id="lote" placeholder="Lote">
            <small>Data de Fabricação</small>
            <input id="fab" type="date">
            <small>Data de Validade</small>
            <input id="val" type="date">
            <button onclick="addProduto()">Salvar Produto</button>
        </div>

        <div class="card">
            <h3>Alertas (7 dias antes)</h3>
            <div id="alertas"></div>
        </div>

        <div class="card">
            <h3>Produtos Cadastrados</h3>
            <table>
                <thead>
                    <tr>
                        <th>Produto</th>
                        <th>Lote</th>
                        <th>Fabricação</th>
                        <th>Validade</th>
                    </tr>
                </thead>
                <tbody id="lista"></tbody>
            </table>
        </div>

    </div>
</div>

<script>
const USER = "admin";
const PASS = "admin";
let produtos = JSON.parse(localStorage.getItem("produtos")) || [];

function login() {
    if (user.value === USER && pass.value === PASS) {
        loginDiv = document.getElementById("login");
        appDiv = document.getElementById("app");
        loginDiv.classList.add("hidden");
        appDiv.classList.remove("hidden");
        render();
    } else {
        alert("Usuário ou senha incorretos");
    }
}

function addProduto() {
    if (!nome.value || !lote.value || !fab.value || !val.value) {
        alert("Preencha todos os campos");
        return;
    }
    produtos.push({
        nome: nome.value,
        lote: lote.value,
        fab: fab.value,
        val: val.value
    });
    localStorage.setItem("produtos", JSON.stringify(produtos));
    nome.value = lote.value = fab.value = val.value = "";
    render();
}

function render() {
    lista.innerHTML = "";
    alertas.innerHTML = "";
    const hoje = new Date();

    produtos.forEach(p => {
        const validade = new Date(p.val);
        const diff = Math.ceil((validade - hoje) / (1000 * 60 * 60 * 24));

        const tr = document.createElement("tr");
        tr.innerHTML = `
            <td>${p.nome}</td>
            <td>${p.lote}</td>
            <td>${p.fab}</td>
            <td>${p.val}</td>
        `;
        lista.appendChild(tr);

        if (diff <= 7 && diff >= 0) {
            alertas.innerHTML += `
                <div class="alert">
                    ⚠ ${p.nome} (Lote ${p.lote}) vence em ${diff} dia(s)
                </div>
            `;
        }
        if (diff < 0) {
            alertas.innerHTML += `
                <div class="alert danger">
                    ❌ ${p.nome} (Lote ${p.lote}) VENCIDO
                </div>
            `;
        }
    });
}
</script>

</body>
</html>
