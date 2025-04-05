<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Gerador de Código de Barras - EAN13 (Offline)</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
    }
    input {
      font-size: 16px;
      padding: 6px;
      width: 300px;
    }
    button {
      font-size: 16px;
      padding: 6px 12px;
      margin-left: 10px;
    }
    svg {
      margin-top: 30px;
      border: 1px solid #ccc;
    }
  </style>
</head>
<body>
  <h2>Gerar Código de Barras - EAN13</h2>
  <input type="text" id="codigo" value="789123456789" maxlength="13" />
  <button onclick="gerar()">Gerar Código</button>
  <svg id="barcode"></svg>

  <script>
    // Implementação simplificada do JsBarcode para EAN13
    function generateEAN13(code) {
      // Verifica se o código é válido (12 ou 13 dígitos)
      if (!/^\d{12,13}$/.test(code)) {
        throw new Error("O código deve conter 12 ou 13 dígitos");
      }
      
      // Calcula o dígito verificador se necessário
      if (code.length === 12) {
        code = code + calculateChecksum(code);
      }
      
      // Padrões de codificação
      const patterns = {
        L: ["0001101", "0011001", "0010011", "0111101", "0100011", 
            "0110001", "0101111", "0111011", "0110111", "0001011"],
        G: ["0100111", "0110011", "0011011", "0100001", "0011101", 
            "0111001", "0000101", "0010001", "0001001", "0010111"],
        R: ["1110010", "1100110", "1101100", "1000010", "1011100", 
            "1001110", "1010000", "1000100", "1001000", "1110100"]
      };
      
      const structure = [
        "LLLLLL",
        "LLGLGG",
        "LLGGLG",
        "LLGGGL",
        "LGLLGG",
        "LGGLLG",
        "LGGGLL",
        "LGLGLG",
        "LGLGGL",
        "LGGLGL"
      ];
      
      // Determina o padrão a ser usado
      const firstDigit = parseInt(code[0]);
      const pattern = structure[firstDigit];
      
      let barcode = "101"; // Início
      
      // Primeira metade (6 dígitos)
      for (let i = 1; i <= 6; i++) {
        const digit = parseInt(code[i]);
        const patternType = pattern[i-1];
        barcode += patterns[patternType][digit];
      }
      
      barcode += "01010"; // Centro
      
      // Segunda metade (6 dígitos)
      for (let i = 7; i <= 12; i++) {
        const digit = parseInt(code[i]);
        barcode += patterns["R"][digit];
      }
      
      barcode += "101"; // Fim
      
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
    
    function renderBarcode(binary, svgElement, options = {}) {
      const width = options.width || 2;
      const height = options.height || 80;
      const margin = options.margin || 10;
      const displayValue = options.displayValue !== false;
      
      // Limpa o SVG
      svgElement.innerHTML = '';
      
      // Cria um grupo para o código de barras
      const group = document.createElementNS("http://www.w3.org/2000/svg", "g");
      svgElement.appendChild(group);
      
      // Desenha as barras
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
      
      // Adiciona o texto (código)
      if (displayValue && options.code) {
        const text = document.createElementNS("http://www.w3.org/2000/svg", "text");
        text.setAttribute("x", x / 2);
        text.setAttribute("y", height + margin + 20);
        text.setAttribute("text-anchor", "middle");
        text.setAttribute("font-family", "Arial");
        text.setAttribute("font-size", "16");
        text.textContent = options.code;
        group.appendChild(text);
      }
      
      // Ajusta o tamanho do SVG
      svgElement.setAttribute("width", x + margin);
      svgElement.setAttribute("height", height + margin + (displayValue ? 30 : 0));
    }
    
    function gerar() {
      const codigo = document.getElementById("codigo").value.trim();
      const svg = document.getElementById("barcode");
      
      try {
        const barcode = generateEAN13(codigo);
        renderBarcode(barcode.binary, svg, {
          width: 2,
          height: 80,
          margin: 10,
          code: barcode.code,
          displayValue: true
        });
      } catch (error) {
        alert(error.message);
      }
    }
    
    // Gera o código ao carregar a página
    window.onload = gerar;
  </script>
</body>
</html>
