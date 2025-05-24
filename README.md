<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gerador de Códigos de Barras em Lote - EAN13</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      max-width: 800px;
      margin: 0 auto;
      background-color: #f5f5f5;
    }
    .input-area, .output-area {
      margin-bottom: 20px;
      background: white;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    textarea {
      width: 100%;
      height: 150px;
      padding: 10px;
      font-family: monospace;
      border: 1px solid #ddd;
      border-radius: 4px;
      resize: vertical;
    }
    button {
      font-size: 16px;
      padding: 8px 16px;
      margin: 10px 5px 10px 0;
      cursor: pointer;
      color: white;
      border: none;
      border-radius: 4px;
      transition: background-color 0.3s;
    }
    #gerar {
      background-color: #4CAF50;
    }
    #gerar:hover {
      background-color: #45a049;
    }
    #limpar {
      background-color: #f44336;
    }
    #limpar:hover {
      background-color: #d32f2f;
    }
    #imprimir {
      background-color: #2196F3;
    }
    #imprimir:hover {
      background-color: #0b7dda;
    }
    #copiar {
      background-color: #ff9800;
    }
    #copiar:hover {
      background-color: #e68a00;
    }
    .barcode-container {
      display: flex;
      flex-wrap: wrap;
      gap: 20px;
      margin-top: 20px;
    }
    .barcode-item {
      text-align: center;
      margin-bottom: 20px;
      page-break-inside: avoid;
      background: white;
      padding: 15px;
      border-radius: 8px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }
    svg {
      border: 1px solid #eee;
      background: white;
    }
    .controls {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin-bottom: 10px;
    }
    .example-title {
      font-style: italic;
      color: #666;
      margin-bottom: 10px;
    }

    @media print {
      .input-area,
      .no-print,
      button,
      .controls,
      .example-title {
        display: none !important;
      }

      .output-area {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        background: none;
        box-shadow: none;
        padding: 0;
      }

      .barcode-item {
        display: inline-block;
        margin: 10px;
        page-break-inside: avoid;
        break-inside: avoid;
        box-shadow: none;
        padding: 5px;
      }
    }
  </style>
</head>
<body>
  <h1>Gerador de Códigos de Barras em Lote - EAN13</h1>

  <div class="input-area no-print">
    <h2>Cole seus códigos (um por linha):</h2>
    <textarea id="codigos" placeholder="Cole aqui vários códigos EAN13, um por linha
Exemplo:
7891000315507
7891910000197
7891234567890"></textarea>

    <div class="controls">
      <button id="gerar" onclick="gerarTodos()">Gerar Códigos</button>
      <button id="limpar" onclick="limparTudo()">Limpar Tudo</button>
      <button id="copiar" onclick="copiarCodigos()">Copiar Códigos</button>
      <button id="imprimir" onclick="window.print()">Imprimir Códigos</button>
    </div>

    <div class="example-title">Exemplos (não são apagados ao limpar):</div>
  </div>

  <div class="output-area">
    <h2 class="no-print">Códigos Gerados:</h2>
    <div id="barcodes" class="barcode-container"></div>
  </div>

  <script>
    function generateEAN13(code, skipValidation = false) {
      if (!/^\d{12,13}$/.test(code)) {
        throw new Error(`Código inválido: "${code}" - Deve conter 12 ou 13 dígitos`);
      }

      if (code.length === 12) {
        code = code + calculateChecksum(code);
      } else if (!skipValidation && calculateChecksum(code.substring(0, 12)) != code[12]) {
        throw new Error(`Dígito verificador inválido para código: "${code}"`);
      }

      const patterns = {
        L: ["0001101","0011001","0010011","0111101","0100011","0110001","0101111","0111011","0110111","0001011"],
        G: ["0100111","0110011","0011011","0100001","0011101","0111001","0000101","0010001","0001001","0010111"],
        R: ["1110010","1100110","1101100","1000010","1011100","1001110","1010000","1000100","1001000","1110100"]
      };

      const structure = ["LLLLLL","LLGLGG","LLGGLG","LLGGGL","LGLLGG","LGGLLG","LGGGLL","LGLGLG","LGLGGL","LGGLGL"];

      const firstDigit = parseInt(code[0]);
      const pattern = structure[firstDigit];

      let barcode = "101";

      for (let i = 1; i <= 6; i++) {
        const digit = parseInt(code[i]);
        const patternType = pattern[i-1];
        barcode += patterns[patternType][digit];
      }

      barcode += "01010";

      for (let i = 7; i <= 12; i++) {
        const digit = parseInt(code[i]);
        barcode += patterns["R"][digit];
      }

      barcode += "101";

      return {
        code: code,
        binary: barcode
      };
    }

    function calculateChecksum(code) {
      let sum = 0;
      for (let i = 0; i < 12; i++) {
        const digit = parseInt(code[i]);
        sum += (i % 2 === 0) ? digit * 1 : digit * 3;
      }
      return (10 - (sum % 10)) % 10;
    }

    function renderBarcode(barcodeData, container, isExample = false) {
      const binary = barcodeData.binary;
      const code = barcodeData.code;
      const width = 2;
      const height = 60;
      const margin = 10;

      const svg = document.createElementNS("http://www.w3.org/2000/svg", "svg");
      svg.setAttribute("width", (binary.length * width) + (margin * 2));
      svg.setAttribute("height", height + margin);

      let x = margin;
      for (let i = 0; i < binary.length; i++) {
        if (binary[i] === '1') {
          const rect = document.createElementNS("http://www.w3.org/2000/svg", "rect");
          rect.setAttribute("x", x);
          rect.setAttribute("y", margin);
          rect.setAttribute("width", width);
          rect.setAttribute("height", height);
          rect.setAttribute("fill", "#000");
          svg.appendChild(rect);
        }
        x += width;
      }

      const item = document.createElement("div");
      item.className = "barcode-item";
      if (isExample) item.classList.add("example-barcode");
      item.appendChild(svg);

      const numberDiv = document.createElement("div");
      numberDiv.className = "barcode-number";
      numberDiv.textContent = code;

      // 🛠️ Aplicando estilos inline para garantir impressão
      numberDiv.style.display = "block";
      numberDiv.style.marginTop = "5px";
      numberDiv.style.fontSize = "12px";
      numberDiv.style.color = "black";
      numberDiv.style.fontFamily = "monospace";

      item.appendChild(numberDiv);
      container.appendChild(item);
    }

    function gerarTodos() {
      const input = document.getElementById("codigos").value.trim();
      const container = document.getElementById("barcodes");

      const items = container.querySelectorAll('.barcode-item:not(.example-barcode)');
      items.forEach(item => item.remove());

      if (!input) {
        alert("Por favor, cole alguns códigos EAN13 no campo de texto.");
        return;
      }

      const codigos = input.split('\n').map(line => line.trim()).filter(line => line.length > 0);

      let successCount = 0;
      let errorCount = 0;
      const errorMessages = [];

      codigos.forEach((codigo, index) => {
        try {
          const barcode = generateEAN13(codigo, true);
          renderBarcode(barcode, container);
          successCount++;
        } catch (error) {
          errorCount++;
          errorMessages.push(`Linha ${index + 1}: ${error.message}`);
          const errorDiv = document.createElement("div");
          errorDiv.className = "barcode-item no-print";
          errorDiv.style.color = "red";
          errorDiv.textContent = `Erro: ${codigo} - ${error.message}`;
          container.appendChild(errorDiv);
        }
      });

      if (errorCount > 0) {
        alert(`Foram gerados ${successCount} códigos com sucesso.\n\nErros encontrados (${errorCount}):\n${errorMessages.join('\n')}`);
      } else if (successCount > 0) {
        alert(`Todos os ${successCount} códigos foram gerados com sucesso!`);
      }
    }

    function limparTudo() {
      document.getElementById("codigos").value = '';
      const container = document.getElementById("barcodes");
      const items = container.querySelectorAll('.barcode-item:not(.example-barcode)');
      items.forEach(item => item.remove());
    }

    function copiarCodigos() {
      const input = document.getElementById("codigos");
      input.select();
      document.execCommand('copy');
      alert("Códigos copiados para a área de transferência!");
    }

    function adicionarExemplos() {
      const container = document.getElementById("barcodes");
      const exemplos = ['7891000315507', '7891910000197', '7891234567890'];
      exemplos.forEach(codigo => {
        try {
          const barcode = generateEAN13(codigo, true);
          renderBarcode(barcode, container, true);
        } catch (error) {
          console.error("Erro ao gerar exemplo:", error);
        }
      });
    }

    document.addEventListener('DOMContentLoaded', function() {
      adicionarExemplos();
    });
  </script>
</body>
</html>
