<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Sistema de Retirada de Lanterna</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; background: #f0f2f5; }
    h1 { text-align: center; color: #333; }
    input, select, button { padding: 8px; font-size: 16px; margin-top: 5px; }
    button { cursor: pointer; background-color: #007bff; color: white; border: none; border-radius: 5px; }
    button:hover { background-color: #0056b3; }
    .lanterna { border: 1px solid #ccc; padding: 15px; margin: 10px 0; border-radius: 8px; background: white; display: flex; justify-content: space-between; align-items: center; }
    .disponivel { color: green; font-weight: bold; }
    .indisponivel { color: red; font-weight: bold; }
    .lanterna-info { flex: 1; }
    .lanterna-actions button { background-color: #28a745; }
    .lanterna-actions button:hover { background-color: #218838; }
    @media(max-width:600px){
      .lanterna { flex-direction: column; align-items: flex-start; }
      .lanterna-actions { margin-top: 10px; }
    }
  </style>
</head>
<body>
  <h1>Sistema de Retirada de Lanterna</h1>

  <label>Nome do Funcionário:</label><br>
  <input type="text" id="funcionario" placeholder="Digite seu nome"><br><br>

  <label>Escolha uma lanterna disponível:</label><br>
  <select id="lanternaSelect"></select><br><br>

  <button onclick="retirarLanterna()">Retirar Lanterna</button>

  <h2>Lanternas</h2>
  <div id="listaLanternas"></div>

  <script>
    let lanternas = [
      { id: 1, nome: "Lanterna 1", disponivel: true, usuario: "" },
      { id: 2, nome: "Lanterna 2", disponivel: true, usuario: "" },
      { id: 3, nome: "Lanterna 3", disponivel: true, usuario: "" }
    ];

    // Carregar dados do localStorage, se existirem
    const dadosSalvos = localStorage.getItem("lanternas");
    if (dadosSalvos) lanternas = JSON.parse(dadosSalvos);

    function salvarLocal() {
      localStorage.setItem("lanternas", JSON.stringify(lanternas));
    }

    function atualizarInterface() {
      const lista = document.getElementById("listaLanternas");
      lista.innerHTML = "";

      lanternas.forEach(l => {
        const div = document.createElement("div");
        div.className = "lanterna";
        div.innerHTML = `
          <div class="lanterna-info">
            <strong>${l.nome}</strong><br>
            <span class="${l.disponivel ? 'disponivel' : 'indisponivel'}">
              ${l.disponivel ? "Disponível" : "Em uso por: " + l.usuario}
            </span>
          </div>
          <div class="lanterna-actions">
            ${!l.disponivel ? `<button onclick="devolverLanterna(${l.id})">Devolver</button>` : ""}
          </div>
        `;
        lista.appendChild(div);
      });

      const select = document.getElementById("lanternaSelect");
      select.innerHTML = "";
      lanternas.filter(l => l.disponivel).forEach(l => {
        const option = document.createElement("option");
        option.value = l.id;
        option.textContent = l.nome;
        select.appendChild(option);
      });
    }

    function retirarLanterna() {
      const funcionario = document.getElementById("funcionario").value.trim();
      const id = parseInt(document.getElementById("lanternaSelect").value);

      if (!funcionario || isNaN(id)) {
        alert("Preencha o nome e selecione uma lanterna!");
        return;
      }

      lanternas = lanternas.map(l =>
        l.id === id && l.disponivel
          ? { ...l, disponivel: false, usuario: funcionario }
          : l
      );
      salvarLocal();
      atualizarInterface();
      alert(`${funcionario} retirou a lanterna!`);
    }

    function devolverLanterna(id) {
      lanternas = lanternas.map(l =>
        l.id === id ? { ...l, disponivel: true, usuario: "" } : l
      );
      salvarLocal();
      atualizarInterface();
      alert(`Lanterna devolvida!`);
    }

    atualizarInterface();
  </script>
</body>
</html>
