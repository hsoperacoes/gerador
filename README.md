<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Gerador de Códigos de Barras</title>
  <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
    }
    textarea {
      width: 100%;
      height: 100px;
    }
    .barcode {
      margin: 10px 0;
    }
    @media print {
      body * {
        visibility: hidden;
      }
      #barcodes, #barcodes * {
        visibility: visible;
      }
      #barcodes {
        position: absolute;
        left: 0;
        top: 0;
        padding: 20px;
      }
    }
  </style>
</head>
<body>
  <h1>Gerador de Códigos de Barras</h1>
  <p>Cole os códigos abaixo (um por linha):</p>
  <textarea id="inputCodes" placeholder="Ex: ABC123456&#10;9876543210"></textarea><br>
  <button onclick="gerarCodigos()">Gerar</button>
  <button onclick="window.print()">Imprimir</button>

  <div id="barcodes"></div>

  <script>
    function gerarCodigos() {
      const container = document.getElementById('barcodes');
      container.innerHTML = '';
      const codes = document.getElementById('inputCodes').value.split('\n');
      codes.forEach(code => {
        if (code.trim() !== '') {
          const svg = document.createElement('svg');
          svg.classList.add('barcode');
          JsBarcode(svg, code.trim(), {
            format: "CODE128", // Mais flexível que EAN13
            displayValue: true,
            width: 2,
            height: 60
          });
          container.appendChild(svg);
        }
      });
    }
  </script>
</body>
</html>
