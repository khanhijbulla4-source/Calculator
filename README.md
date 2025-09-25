# Calculator<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Simple Calculator</title>
  <style>
    body{font-family:system-ui,Segoe UI,Roboto;display:flex;min-height:100vh;align-items:center;justify-content:center;background:#f3f4f6;margin:0}
    .calc{width:320px;background:#fff;border-radius:12px;box-shadow:0 6px 18px rgba(0,0,0,.08);padding:18px}
    .display{height:56px;border-radius:8px;background:#f9fafb;margin-bottom:12px;padding:10px;font-size:24px;display:flex;align-items:center;justify-content:flex-end;overflow:hidden}
    .keys{display:grid;grid-template-columns:repeat(4,1fr);gap:8px}
    button{padding:14px;border-radius:8px;border:0;font-size:18px;background:#eef2f7}
    .op{background:#ffd9a8}
    .wide{grid-column:span 2}
  </style>
</head>
<body>
  <div class="calc">
    <div id="disp" class="display">0</div>
    <div class="keys">
      <button onclick="clearAll()">C</button>
      <button onclick="press('%')">%</button>
      <button onclick="press('±')">±</button>
      <button class="op" onclick="press('/')">÷</button>

      <button onclick="press('7')">7</button>
      <button onclick="press('8')">8</button>
      <button onclick="press('9')">9</button>
      <button class="op" onclick="press('*')">×</button>

      <button onclick="press('4')">4</button>
      <button onclick="press('5')">5</button>
      <button onclick="press('6')">6</button>
      <button class="op" onclick="press('-')">−</button>

      <button onclick="press('1')">1</button>
      <button onclick="press('2')">2</button>
      <button onclick="press('3')">3</button>
      <button class="op" onclick="press('+')">+</button>

      <button class="wide" onclick="press('0')">0</button>
      <button onclick="press('.')">.</button>
      <button class="op" onclick="calculate()">=</button>
    </div>
  </div>

  <script>
    let expr = "";
    const disp = document.getElementById('disp');

    function update() {
      disp.textContent = expr || "0";
    }

    function clearAll(){ expr = ""; update() }

    function press(k){
      if(k==='±'){
        if(expr==='') return;
        // toggle sign on last number
        expr = expr.replace(/(\d+(\.\d+)?)$/,(m)=> (m.startsWith('-')?m.slice(1):'-'+m));
        update(); return;
      }
      if(k === '%'){
        try{
          const val = eval(expr||"0")/100;
          expr = String(val);
        }catch(e){}
        update(); return;
      }
      // prevent two operators
      if(/[+\-*/]/.test(k)){
        if(expr==="" && k!=='-') return;
        expr = expr.replace(/[+\-*/]+$/,'') + k;
      }else{
        expr += k;
      }
      update();
    }

    function calculate(){
      try{
        // safe-ish: disallow letters
        if(/[a-zA-Z]/.test(expr)) { disp.textContent = "Error"; return; }
        const val = Function('"use strict";return ('+expr+')')();
        expr = String(Number.isFinite(val) ? +val.toPrecision(12) : val);
      }catch(e){
        expr = "";
        disp.textContent = "Error";
        setTimeout(update,800);
        return;
      }
      update();
    }

    // touch-friendly: allow keyboard enter
    window.addEventListener('keydown', e=>{
      if(e.key === 'Enter') calculate();
      if(/\d|\./.test(e.key)) press(e.key);
      if(['+','-','*','/'].includes(e.key)) press(e.key);
      if(e.key === 'Backspace') expr = expr.slice(0,-1), update();
    });
  </script>
</body>
</html>
