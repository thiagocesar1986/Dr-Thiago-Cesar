c<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Doses do Thiagão</title>
  <style>
    body { font-family: sans-serif; padding: 20px; max-width: 600px; margin: auto; }
    label { display: block; margin-top: 10px; }
    input, select, button { width: 100%; padding: 8px; margin-top: 5px; }
    .resultado { margin-top: 20px; font-weight: bold; }
  </style>
</head>
<body>
  <h2>Doses do Thiagão</h2>

  <label for="condicao">Condição clínica:</label>
  <select id="condicao" onchange="preencherCampos()"></select>

  <label for="peso">Peso do paciente (kg):</label>
  <input type="number" id="peso" step="0.1">

  <label for="dose">Dose recomendada (mg/kg):</label>
  <input type="number" id="dose">

  <label for="concentracao_mg">Quantidade de medicamento (mg):</label>
  <input type="number" id="concentracao_mg">

  <label for="volume_ml">Volume correspondente (mL):</label>
  <input type="number" id="volume_ml">

  <button onclick="calcularDose()">Calcular Dose</button>

  <div class="resultado" id="resultado"></div>

  <script>
    const protocolos = {
      "Pneumonia": {
        dose: 50, 
        concentracao_mg: 250, 
        volume_ml: 5,
        medicamentos: [
          { nome: "Amoxicilina", dose_ataque: "500 mg a cada 8 horas por 3 dias", dose_manutencao: "250 mg a cada 12 horas" },
          { nome: "Azitromicina", dose_ataque: "500 mg/dia por 5 dias", dose_manutencao: "Não aplicável" }
        ]
      },
      "Bronquiolite viral aguda": {
        dose: 0, 
        concentracao_mg: 0.9, 
        volume_ml: 5,
        medicamentos: [
          { nome: "Soro fisiológico", dose_ataque: "Nebulização conforme necessidade", dose_manutencao: "Não aplicável" }
        ]
      },
      "Dengue": {
        dose: 15, 
        concentracao_mg: 500, 
        volume_ml: 5,
        medicamentos: [
          { nome: "Dipirona", dose_ataque: "15 mg/kg a cada 6 horas", dose_manutencao: "Não aplicável" }
        ]
      },
      "Hipertensão": {
        dose: 10, 
        concentracao_mg: 50, 
        volume_ml: 2,
        medicamentos: [
          { nome: "Captopril", dose_ataque: "12,5 mg a cada 12 horas", dose_manutencao: "25 mg/dia" },
          { nome: "Losartana", dose_ataque: "50 mg/dia", dose_manutencao: "100 mg/dia" }
        ]
      },
      "Eclâmpsia": {
        dose: 4, 
        concentracao_mg: 100, 
        volume_ml: 5,
        medicamentos: [
          { nome: "Sulfato de magnésio", dose_ataque: "4 g IV, seguido por 1 g/h por 24 horas", dose_manutencao: "Não aplicável" }
        ]
      },
      "Pré-eclâmpsia": {
        dose: 5, 
        concentracao_mg: 100, 
        volume_ml: 5,
        medicamentos: [
          { nome: "Labetalol", dose_ataque: "20 mg IV, repetido conforme necessário até 200 mg", dose_manutencao: "100 mg/dia" },
          { nome: "Nifedipino", dose_ataque: "10 mg a cada 20 minutos até 40 mg", dose_manutencao: "30 mg/dia" }
        ]
      },
      "Diabetes mellitus tipo 2": {
        dose: 500, 
        concentracao_mg: 500, 
        volume_ml: 1,
        medicamentos: [
          { nome: "Metformina", dose_ataque: "500 mg a cada 12 horas", dose_manutencao: "850 mg/dia" }
        ]
      },
      "Hidratação geral": {
        dose: 0, 
        concentracao_mg: 0, 
        volume_ml: 500,
        medicamentos: [
          { nome: "Soro fisiológico", dose_ataque: "500 mL a cada 6 horas", dose_manutencao: "500 mL a cada 12 horas" },
          { nome: "Ringer lactato", dose_ataque: "500 mL a cada 6 horas", dose_manutencao: "500 mL a cada 12 horas" }
        ]
      },
      "Hidratação em Dengue": {
        dose: 0, 
        concentracao_mg: 0, 
        volume_ml: 500,
        medicamentos: [
          { nome: "Soro fisiológico", dose_ataque: "500 mL a cada 6 horas nas primeiras 24h", dose_manutencao: "250 mL a cada 6 horas depois" },
          { nome: "Ringer lactato", dose_ataque: "500 mL a cada 6 horas nas primeiras 24h", dose_manutencao: "250 mL a cada 6 horas depois" }
        ]
      },
      "Hidratação em Insuficiência Cardíaca": {
        dose: 0, 
        concentracao_mg: 0, 
        volume_ml: 500,
        medicamentos: [
          { nome: "Soro fisiológico", dose_ataque: "500 mL a cada 24 horas ou conforme necessidade", dose_manutencao: "500 mL a cada 48 horas conforme avaliação clínica" }
        ]
      },
      "Hidratação em Insuficiência Renal": {
        dose: 0, 
        concentracao_mg: 0, 
        volume_ml: 500,
        medicamentos: [
          { nome: "Soro fisiológico", dose_ataque: "500 mL a cada 24 horas ou conforme avaliação clínica", dose_manutencao: "500 mL a cada 48 horas conforme avaliação clínica" }
        ]
      }
    };

    function preencherCampos() {
      const condicao = document.getElementById("condicao").value;
      const protocolo = protocolos[condicao];

      if (protocolo) {
        document.getElementById("dose").value = protocolo.dose;
        document.getElementById("concentracao_mg").value = protocolo.concentracao_mg;
        document.getElementById("volume_ml").value = protocolo.volume_ml;
      }
    }

    function calcularDose() {
      const peso = parseFloat(document.getElementById("peso").value);
      const dose = parseFloat(document.getElementById("dose").value);
      const concentracao_mg = parseFloat(document.getElementById("concentracao_mg").value);
      const volume_ml = parseFloat(document.getElementById("volume_ml").value);

      if (peso <= 0 || dose <= 0 || concentracao_mg <= 0 || volume_ml <= 0) {
        alert("Por favor, insira todos os valores corretamente.");
        return;
      }

      const dose_total_mg = peso * dose;
      const volume_a_administrar = (dose_total_mg * volume_ml) / concentracao_mg;

      document.getElementById("resultado").textContent = `Volume a ser administrado: ${volume_a_administrar.toFixed(2)} mL`;
    }

    window.onload = function() {
      const condicoes = Object.keys(protocolos);
      const select = document.getElementById("condicao");

      condicoes.forEach(condicao => {
        const option = document.createElement("option");
        option.value = condicao;
        option.textContent = condicao;
        select.appendChild(option);
      });
    };
  </script>
</body>
</html>
