# Calculadora-DL50
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de DL50</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            margin: 20px;
            background-color: #f0f8ff;
        }
        h1 {
            color: #333;
        }
        table {
            border-collapse: collapse;
            width: 100%;
            margin-bottom: 20px;
        }
        table, th, td {
            border: 1px solid #ccc;
        }
        th, td {
            padding: 8px;
            text-align: center;
        }
        input[type="number"] {
            width: 80px;
        }
        button {
            padding: 8px 16px;
            background-color: #007bff;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 4px;
        }
        button:hover {
            background-color: #0056b3;
        }
        .result {
            font-weight: bold;
            color: green;
        }
    </style>
</head>
<body>

<h1>Calculadora de DL50</h1>

<p>Informe as doses e o número de afetados/total por dose:</p>

<table id="doseTable">
    <tr>
        <th>Dose (mg/kg)</th>
        <th>Afetados</th>
        <th>Total</th>
        <th>% Afetados</th>
    </tr>
    <tr>
        <td><input type="number" step="any"></td>
        <td><input type="number"></td>
        <td><input type="number"></td>
        <td>—</td>
    </tr>
    <tr>
        <td><input type="number" step="any"></td>
        <td><input type="number"></td>
        <td><input type="number"></td>
        <td>—</td>
    </tr>
    <tr>
        <td><input type="number" step="any"></td>
        <td><input type="number"></td>
        <td><input type="number"></td>
        <td>—</td>
    </tr>
    <tr>
        <td><input type="number" step="any"></td>
        <td><input type="number"></td>
        <td><input type="number"></td>
        <td>—</td>
    </tr>
</table>

<button onclick="calcularDL50()">Calcular DL50</button>

<p class="result" id="resultado"></p>

<script>
function calcularDL50() {
    const tabela = document.getElementById('doseTable');
    const linhas = tabela.rows;
    let dados = [];

    for (let i = 1; i < linhas.length; i++) {
        const dose = parseFloat(linhas[i].cells[0].children[0].value);
        const afetados = parseInt(linhas[i].cells[1].children[0].value);
        const total = parseInt(linhas[i].cells[2].children[0].value);

        if (!isNaN(dose) && !isNaN(afetados) && !isNaN(total) && total > 0) {
            const perc = (afetados / total) * 100;
            linhas[i].cells[3].innerText = perc.toFixed(1) + '%';
            dados.push({ dose, perc });
        }
    }

    if (dados.length < 2) {
        document.getElementById('resultado').innerText = 'Por favor, preencha pelo menos dois pontos válidos.';
        return;
    }

    dados.sort((a, b) => a.dose - b.dose);

    let dl50 = null;

    for (let i = 0; i < dados.length - 1; i++) {
        if (dados[i].perc <= 50 && dados[i+1].perc >= 50) {
            // Interpolação linear
            const x1 = dados[i].dose;
            const x2 = dados[i+1].dose;
            const y1 = dados[i].perc;
            const y2 = dados[i+1].perc;

            dl50 = x1 + ((50 - y1) * (x2 - x1)) / (y2 - y1);
            break;
        }
    }

    if (dl50 !== null) {
        document.getElementById('resultado').innerText = `DL50 estimada ≈ ${dl50.toFixed(2)} mg/kg`;
    } else {
        document.getElementById('resultado').innerText = 'Não foi possível estimar a DL50. Verifique se os dados abrangem a faixa próxima de 50% de efeito.';
    }
}
</script>

</body>
</html>
