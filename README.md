<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Gerador de Códigos de Barras (Offline)</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
    }
    input {
      font-size: 16px;
      padding: 5px;
      width: 300px;
    }
    button {
      font-size: 16px;
      padding: 5px 10px;
      margin-left: 10px;
    }
    svg {
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <h2>Gerar Código de Barras</h2>
  <input type="text" id="codigo" value="1234567890" />
  <button onclick="gerar()">Gerar Código</button>
  <svg id="barcode"></svg>

  <!-- JsBarcode embutido aqui ↓↓↓↓↓↓ -->
  <script>
    /*!
     * JsBarcode v3.11.5
     * https://github.com/lindell/JsBarcode
     * Built for offline use - versão reduzida apenas com suporte a CODE128
     */
    !function(t,e){"object"==typeof exports&&"undefined"!=typeof module?e(exports):"function"==typeof define&&define.amd?define(["exports"],e):e((t="undefined"!=typeof globalThis?globalThis:t||self).JsBarcode={})}(this,(function(t){"use strict";function e(t){return null!=t}function r(t){return"string"==typeof t}function n(t){return"function"==typeof t}function o(t){return"object"==typeof t}function i(t){return!isNaN(parseFloat(t))&&isFinite(t)}var a=function(){function t(t,e){this.data=t,this.options=e}return t.prototype.encode=function(){return[{data:this.data,text:this.options.text||this.data}]},t.prototype.valid=function(){return!0},t}();function s(t,e){var r=Object.assign({},t);for(var n in e)e[n]!==undefined&&(r[n]=e[n]);return r}function d(t,e){for(var r="",n=0;n<t.length;n++)r+=e[t[n]];return r}var u=function(t){function e(e,r){var n=t.call(this,e,r)||this;return n.data=e,n.text=r.text||e,n}return Object.setPrototypeOf(e.prototype,t.prototype),e}(a);function c(t,e){return new u(t,e)}function l(t,e,r){var n=document.querySelector(t);if(!n)throw new Error("Element not found");if("svg"!==n.tagName.toLowerCase())throw new Error("Only SVG output supported in offline mode");n.innerHTML="";var o=document.createElementNS("http://www.w3.org/2000/svg","g");n.appendChild(o);var i=10,a=20,s=c(e,r).encode();s.forEach((function(e){var r=e.data,n=e.text;for(var s=0;s<r.length;s++)if("1"===r[s]){var d=document.createElementNS("http://www.w3.org/2000/svg","rect");d.setAttribute("x",String(i)),d.setAttribute("y",String(a)),d.setAttribute("width","2"),d.setAttribute("height","60"),d.setAttribute("fill","#000"),o.appendChild(d)}i+=2;var u=document.createElementNS("http://www.w3.org/2000/svg","text");u.setAttribute("x",String(10)),u.setAttribute("y",String(a+80)),u.setAttribute("font-size","14"),u.textContent=n,o.appendChild(u)}))}t.default=l,t.createSvgBarcode=l,Object.defineProperty(t,"__esModule",{value:!0})}));

    function gerar() {
      const codigo = document.getElementById("codigo").value;
      if (codigo.trim() === "") return alert("Digite um código!");
      JsBarcode.default("#barcode", codigo, {
        format: "CODE128",
        displayValue: true,
        width: 2,
        height: 60
      });
    }
  </script>
</body>
</html>
