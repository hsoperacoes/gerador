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

  <script>
    /*! JsBarcode v3.11.5 - versão simplificada para EAN13 */
    !function(e,t){"object"==typeof exports&&"undefined"!=typeof module?t(exports):"function"==typeof define&&define.amd?define(["exports"],t):t(e.JsBarcode={})}(this,function(e){"use strict";function t(e,t){for(var r="",n=0;n<e.length;n++)r+=t[e[n]];return r}function r(e){var t={};return e.split("").forEach(function(e){t[e]=!0}),t}function n(e){return!/[^\d]/.test(e)}function a(e){for(var t=0,r=!0,n=0;n<12;n++){var a=parseInt(e.charAt(n),10);t+=r?3*a:a,r=!r}return(10-t%10)%10}function o(e){return e.length===12?e+a(e):e}function i(e){return{data:e,text:e}}function s(e){this.data=e,this.valid=function(){return n(e)&&(e.length===12||e.length===13)&&a(e.substring(0,12))==e[12]}}s.prototype.encode=function(){var e=o(this.data),r=["101"],n=[["0001101","0011001","0010011","0111101","0100011","0110001","0101111","0111011","0110111","0001011"],["0100111","0110011","0011011","0100001","0011101","0111001","0000101","0010001","0001001","0010111"],["1110010","1100110","1101100","1000010","1011100","1001110","1010000","1000100","1001000","1110100"]],i=["LLLLLL","LLGLGG","LLGGLG","LLGGGL","LGLLGG","LGGLLG","LGGGLL","LGLGLG","LGLGGL","LGGLGL"],s=parseInt(e[0],10),l=i[s];for(var d=1;d<=6;d++){var c=parseInt(e[d],10);r.push(n["L"===l[d-1]?0:1][c])}r.push("01010");for(var u=7;u<=12;u++){var c=parseInt(e[u],10);r.push(n[2][c])}return r.push("101"),[{data:t(r,""),text:e,height:80}]};function l(e,t){if("string"==typeof e&&(e=document.querySelector(e)),!e)throw new Error("Element not found.");if("svg"!==e.tagName.toLowerCase())throw new Error("Element must be an SVG.");e.innerHTML="";var r=document.createElementNS("http://www.w3.org/2000/svg","g");e.appendChild(r);var n=e.getAttribute("width")||300,a=e.getAttribute("height")||100,o=0;t=t||{};var i=new s(t.value);if(!i.valid())throw new Error("Invalid input for "+t.format);var l=i.encode();l.forEach(function(e){for(var i=0;i<e.data.length;i++){if("1"===e.data[i]){var s=document.createElementNS("http://www.w3.org/2000/svg","rect");s.setAttribute("x",o),s.setAttribute("y",0),s.setAttribute("width",t.width||2),s.setAttribute("height",e.height||a-20),s.setAttribute("fill",t.lineColor||"#000"),r.appendChild(s)}o+=t.width||2}if(e.text&&!1!==t.displayValue){var d=document.createElementNS("http://www.w3.org/2000/svg","text");d.setAttribute("x",n/2),d.setAttribute("y",a-5),d.setAttribute("text-anchor","middle"),d.setAttribute("fill",t.textColor||"#000"),d.setAttribute("font-family",t.font||"Arial"),d.setAttribute("font-size",t.fontSize||20),d.textContent=e.text,r.appendChild(d)}})}e.encode=function(e,t){var r=new s(t);return r.valid()?r.encode():null},e.default=l,Object.defineProperty(e,"__esModule",{value:!0})});
  </script>

  <script>
    function gerar() {
      const codigo = document.getElementById("codigo").value.trim();
      
      try {
        JsBarcode.default("#barcode", codigo, {
          format: "EAN13",
          displayValue: true,
          fontSize: 16,
          width: 2,
          height: 80,
          margin: 10
        });
      } catch (error) {
        alert("Erro ao gerar código: " + error.message);
      }
    }
    
    // Gerar código ao carregar a página
    window.onload = gerar;
  </script>
</body>
</html>
