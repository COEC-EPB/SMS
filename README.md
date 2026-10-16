<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Teste de SMS</title>

    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f3f5f7;
            margin: 0;
            padding: 30px;
        }

        .card {
            max-width: 450px;
            margin: 50px auto;
            background: white;
            padding: 30px;
            border-radius: 14px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.1);
        }

        h2 {
            margin-top: 0;
        }

        label {
            font-weight: bold;
            display: block;
            margin-top: 15px;
        }

        input,
        textarea {
            width: 100%;
            padding: 12px;
            margin-top: 6px;
            border: 1px solid #ccc;
            border-radius: 7px;
            box-sizing: border-box;
        }

        textarea {
            height: 120px;
            resize: vertical;
        }

        button {
            width: 100%;
            padding: 14px;
            margin-top: 20px;
            border: none;
            border-radius: 7px;
            background: #0066cc;
            color: white;
            font-size: 16px;
            cursor: pointer;
        }

        button:disabled {
            background: #999;
        }

        #resultado {
            margin-top: 20px;
            padding: 12px;
            border-radius: 7px;
            display: none;
        }

        .sucesso {
            display: block !important;
            color: #155724;
            background: #d4edda;
        }

        .erro {
            display: block !important;
            color: #721c24;
            background: #f8d7da;
        }
    </style>
</head>

<body>

<div class="card">

    <h2>📱 Teste de SMS</h2>

    <label>Telefone</label>

    <input
        id="telefone"
        type="tel"
        value="+5583991105959"
        placeholder="+5583999999999"
    >

    <label>Mensagem</label>

    <textarea
        id="mensagem"
        placeholder="Digite sua mensagem..."
    >Teste de SMS enviado pelo Cloudflare Worker.</textarea>

    <button id="btnEnviar" onclick="enviarSMS()">
        Enviar SMS
    </button>

    <div id="resultado"></div>

</div>


<script>

const WORKER_URL = "COLOQUE_AQUI_A_URL_DO_SEU_WORKER";

async function enviarSMS() {

    const telefone = document
        .getElementById("telefone")
        .value
        .trim();

    const mensagem = document
        .getElementById("mensagem")
        .value
        .trim();

    const resultado = document.getElementById("resultado");

    const botao = document.getElementById("btnEnviar");


    if (!telefone) {

        resultado.className = "erro";
        resultado.innerHTML = "Informe o telefone.";

        return;
    }


    if (!mensagem) {

        resultado.className = "erro";
        resultado.innerHTML = "Digite uma mensagem.";

        return;
    }


    try {

        botao.disabled = true;

        botao.innerHTML = "Enviando...";


        const response = await fetch(
            WORKER_URL + "/api/enviar-sms",
            {
                method: "POST",

                headers: {
                    "Content-Type": "application/json"
                },

                body: JSON.stringify({
                    telefone: telefone,
                    mensagem: mensagem
                })
            }
        );


        const dados = await response.json();


        if (!response.ok) {

            console.error(dados);

            throw new Error(
                dados.erro?.message ||
                dados.erro ||
                "Erro ao enviar SMS"
            );
        }


        resultado.className = "sucesso";

        resultado.innerHTML =
            "✅ SMS enviado com sucesso!<br><br>" +
            "SID: " + dados.sid;


    } catch (erro) {

        console.error(erro);

        resultado.className = "erro";

        resultado.innerHTML =
            "❌ Erro ao enviar SMS:<br>" +
            erro.message;

    } finally {

        botao.disabled = false;

        botao.innerHTML = "Enviar SMS";

    }
}

</script>

</body>
</html>
