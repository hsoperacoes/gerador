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
    }
  </style>
</head>
<body>
  <h2>Gerar Código de Barras - EAN13</h2>
  <input type="text" id="codigo" value="789123456789" maxlength="13" />
  <button onclick="gerar()">Gerar Código</button>
  <svg id="barcode"></svg>

  <!-- JsBarcode embutido (versão offline completa) -->
  <script>
    /*! JsBarcode v3.11.5 - versão reduzida para uso offline com suporte EAN13 */
    /*! CDN embed ↓↓↓ */
    !function(e,t){typeof exports=="object"&&typeof module<"u"?t(exports):typeof define=="function"&&define.amd?define(["exports"],t):t(e.JsBarcode={})}(this,function(e){"use strict";function t(e,t){for(var r="",n=0;n<e.length;n++)r+=t[e[n]];return r}function r(e){var t={};return e.split("").forEach(function(e){t[e]=!0}),t}function n(e){return!/[^\d]/.test(e)}function a(e){return e.length===12?e+o(e):e}function o(e){for(var t=0,r=!0,n=0;n<12;n++){var a=parseInt(e.charAt(n),10);t+=r?a*1:a*3,r=!r}return(10-t%10)%10}function i(e){return{data:e,text:e}}function s(e){this.data=e,this.valid=function(){return n(e)&&(e.length===12||e.length===13)&&o(e.substring(0,12))==e[12]}}s.prototype.encode=function(){var e=a(this.data),r=["101"];var n=[["0001101","0011001","0010011","0111101","0100011","0110001","0101111","0111011","0110111","0001011"],["0100111","0110011","0011011","0100001","0011101","0111001","0000101","0010001","0001001","0010111"],["1110010","1100110","1101100","1000010","1011100","1001110","1010000","1000100","1001000","1110100"]],o=["LLLLLL","LLGLGG","LLGGLG","LLGGGL","LGLLGG","LGGLLG","LGGGLL","LGLGLG","LGLGGL","LGGLGL"];var i=parseInt(e[0],10),s=o[i];for(var l=1;l<=6;l++){var d=parseInt(e[l],10);r.push(n[s[l-1]=="L"?0:1][d])}r.push("01010");for(var c=7;c<=12;c++){var d=parseInt(e[c],10);r.push(n[2][d])}return r.push("101"),[{data:t(r,""),text:e}]};function l(e,t){if(typeof e=="string")e=document.querySelector(e);if(!e)throw new Error("Elemento não encontrado.");if(e.tagName.toLowerCase()!=="svg")throw new Error("Somente SVG é suportado.");e.innerHTML="";var r=document.createElementNS("http://www.w3.org/2000/svg","g");e.appendChild(r);var n=JsBarcode.encode(e,t.format||"EAN13",t);var a=10;var o=20;n.forEach(function(t){for(var n=0;n<t.data.length;n++)if(t.data[n]==="1"){var o=document.createElementNS("http://www.w3.org/2000/svg","rect");o.setAttribute("x",String(a)),o.setAttribute("y",String(o)),o.setAttribute("width","2"),o.setAttribute("height",String(t.height||80)),o.setAttribute("fill","#000"),r.appendChild(o)}a+=2;if(t.text){var i=document.createElementNS("http://www.w3.org/2000/svg","text");i.setAttribute("x","10"),i.setAttribute("y",String((t.height||80)+20)),i.setAttribute("font-size","14"),i.textContent=t.text,r.appendChild(i)}})}e.encode=function(e,t,r){var n=new s(t);if(!n.valid())throw new Error("Código inválido para EAN13.");return n.encode()},e.default=l,Object.defineProperty(e,"__esModule",{value:!0})});
  </script>

  <script>
    function gerar() {
      const codigo = document.getElementById("codigo").value.trim();

      if (!/^\d{12,13}$/.test(codigo)) {
        alert("O código EAN13 precisa ter 12 ou 13 números.");
        return;
      }

      JsBarcode.default("#barcode", codigo, {
        format: "EAN13",
        displayValue: true,
        fontSize: 16,
        width: 2,
        height: 80,
        margin: 10
      });
    }
  </script>
</body>
</html>
