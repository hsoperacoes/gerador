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
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 4px;
      transition: background-color 0.3s;
    }
    button:hover {
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
    @media print {
      body * {
        visibility: hidden;
      }
      .output-area, .output-area * {
        visibility: visible;
      }
      .output-area {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        background: none;
        box-shadow: none;
      }
      .barcode-item {
        display: inline-block;
        margin: 10px;
        page-break-inside: avoid;
        break-inside: avoid;
      }
      button, .input-area, .no-print, text {
        display: none !important;
      }
    }
    /* Garante que nenhum elemento do GitHub apareça */
    .Header, .header, .footer, .site-header, .site-footer, .repo-link, .Layout {
      display: none !important;
    }
    body > .Layout {
      padding: 0 !important;
      margin: 0 !important;
    }
  </style>
</head>
<body>
  <h1>Gerador de Códigos de Barras em Lote - EAN13</h1>
  
  <div class="input-area">
    <h2>Cole seus códigos (um por linha):</h2>
    <textarea id="codigos" placeholder="Cole aqui vários códigos EAN13, um por linha"></textarea>
    <div>
      <button id="gerar" onclick="gerarTodos()">Gerar Códigos</button>
      <button id="limpar" onclick="limparTudo()">Limpar Tudo</button>
      <button id="imprimir" onclick="window.print()">Imprimir Códigos</button>
    </div>
  </div>
  
  <div class="output-area">
    <h2 class="no-print">Códigos Gerados:</h2>
    <div id="barcodes" class="barcode-container"></div>
  </div>

  <script>
    // Implementação do EAN13
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
    
    function renderBarcode(binary, container, options = {}) {
      const width = options.width || 2;
      const height = options.height || 60;
      const margin = options.margin || 10;
      const displayValue = options.displayValue !== false;
      
      const svg = document.createElementNS("http://www.w3.org/2000/svg", "svg");
      svg.setAttribute("width", (binary.length * width) + (margin * 2));
      svg.setAttribute("height", height + margin + (displayValue ? 30 : 0));
      svg.classList.add("barcode-svg");
      
      const group = document.createElementNS("http://www.w3.org/2000/svg", "g");
      svg.appendChild(group);
      
      let x = margin;
      for (let i = 0; i < binary.length; i++) {
        if (binary[i] === '1') {
          const rect = document.createElementNS("http://www.w3.org/2000/svg", "rect");
          rect.setAttribute("x", x);
          rect.setAttribute("y", margin);
          rect.setAttribute("width", width);
          rect.setAttribute("height", height);
          rect.setAttribute("fill", "#000");
          group.appendChild(rect);
        }
        x += width;
      }
      
      if (displayValue && options.code) {
        const text = document.createElementNS("http://www.w3.org/2000/svg", "text");
        text.setAttribute("x", (binary.length * width + margin * 2) / 2);
        text.setAttribute("y", height + margin + 20);
        text.setAttribute("text-anchor", "middle");
        text.setAttribute("font-family", "Arial");
        text.setAttribute("font-size", "14");
        text.textContent = options.code;
        text.classList.add("no-print");
        group.appendChild(text);
      }
      
      const item = document.createElement("div");
      item.className = "barcode-item";
      item.appendChild(svg);
      container.appendChild(item);
    }
    
    function gerarTodos() {
      const input = document.getElementById("codigos").value.trim();
      const container = document.getElementById("barcodes");
      container.innerHTML = '';
      
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
          renderBarcode(barcode.binary, container, {
            width: 2,
            height: 60,
            margin: 10,
            code: barcode.code,
            displayValue: true
          });
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
      document.getElementById("barcodes").innerHTML = '';
    }
    
    // Remove elementos do GitHub após o carregamento
    document.addEventListener('DOMContentLoaded', function() {
      // Limpa qualquer cabeçalho residual
      const unwantedElements = [
        '.Header', '.header', '.footer', 
        '.site-header', '.site-footer', '.repo-link',
        '.Layout', 'header', 'footer'
      ];
      
      unwantedElements.forEach(selector => {
        document.querySelectorAll(selector).forEach(el => {
          el.remove();
        });
      });

      // Corrige o estilo do body
      document.body.style.padding = '20px';
      document.body.style.margin = '0 auto';
      document.body.style.maxWidth = '800px';
    });
  </script>
</body>
</html>
