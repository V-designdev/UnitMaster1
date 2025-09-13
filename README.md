<!DOCTYPE html>
<html>

<head>
  <title>Simple Calculator</title>
  <style>
    /* CSS */
      body {
        background: #fff;
        color: #000;
        font-family: 'Segoe UI', Arial, sans-serif;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100vh;
        margin: 0;
      }
      #calculator {
        background: #fff;
        border: 1px solid #000;
        padding: 2em 2.5em;
        border-radius: 12px;
        min-width: 300px;
        box-shadow: none;
      }
      #display {
        background: #fff;
        color: #000;
        font-size: 2em;
        padding: 0.5em 1em;
        border-radius: 6px;
        margin-bottom: 1em;
        text-align: right;
        letter-spacing: 2px;
        min-height: 2em;
        border: 1px solid #000;
      }
      #buttons {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 0.5em;
      }
      #buttons button {
        background: #fff;
        color: #000;
        border: 1px solid #000;
        border-radius: 6px;
        font-size: 1.1em;
        padding: 0.7em 0;
        cursor: pointer;
        font-family: inherit;
        font-weight: normal;
        transition: background 0.2s, color 0.2s;
      }
      #buttons button:hover {
        background: #000;
        color: #fff;
      }
      h1 {
        margin-bottom: 1em;
        font-size: 2em;
        letter-spacing: 1px;
        font-weight: normal;
      }
      .tabs {
        display: flex;
        justify-content: center;
        margin-bottom: 1em;
        gap: 1em;
      }
      .tab {
        background: #fff;
        color: #000;
        border: 1px solid #000;
        border-radius: 6px 6px 0 0;
        padding: 0.5em 2em;
        cursor: pointer;
        font-size: 1em;
        font-family: inherit;
        font-weight: normal;
        transition: background 0.2s, color 0.2s;
      }
      .tab.active {
        background: #000;
        color: #fff;
      }
      .panel { display: none; }
      .panel.active { display: block; }
      #history {
        background: #fff;
        color: #000;
        border: 1px solid #000;
        border-radius: 6px;
        margin-top: 1em;
        padding: 0.5em 1em;
        font-size: 0.95em;
        min-height: 2em;
        max-height: 8em;
        overflow-y: auto;
      }
      .watermark {
        margin-top: 2em;
        font-size: 0.9em;
        color: #aaa;
        text-align: center;
        user-select: none;
        -webkit-user-select: none;
        }
        .subtitle {
          font-style: italic;
          text-align: center;
          margin-bottom: 1em;
      }
  </style>
</head>

<body>
  <h1>UnitMaster</h1>
  <div class="subtitle">Numbers Made Simple</div>
  <div class="tabs">
    <div class="tab active" data-panel="calculator-panel">Calculator</div>
    <div class="tab" data-panel="currency-panel">Currency Converter</div>
    <div class="tab" data-panel="unit-panel">Unit Converter</div>
  </div>
  <div id="calculator-panel" class="panel active">
    <div id="calculator">
      <div id="display">0</div>
      <div id="buttons">
        <button>1</button>
        <button>2</button>
        <button>3</button>
        <button>4</button>
        <button>5</button>
        <button>6</button>
        <button>7</button>
        <button>8</button>
        <button>9</button>
        <button>0</button>
        <button>+</button>
        <button>-</button>
        <button>*</button>
        <button>/</button>
        <button>C</button>
        <button>=</button>
        <button>^</button>
        <button>√</button>
        <button>log</button>
        <button>ln</button>
        <button>sin</button>
        <button>cos</button>
        <button>tan</button>
      </div>
    </div>
    <div id="history"></div>
  </div>
  <div id="currency-panel" class="panel">
    <div style="margin-bottom:1em;">Currency Converter</div>
    <input type="number" id="currency-amount" placeholder="Amount" style="width:80px;"> 
    <select id="currency-from">
      <option value="USD">USD</option>
      <option value="EUR">EUR</option>
      <option value="GBP">GBP</option>
    </select>
    to
    <select id="currency-to">
      <option value="USD">USD</option>
      <option value="EUR">EUR</option>
      <option value="GBP">GBP</option>
    </select>
    <button id="convert-currency">Convert</button>
    <div id="currency-result" style="margin-top:1em;"></div>
  </div>
  <div id="unit-panel" class="panel">
    <div style="margin-bottom:1em;">Unit Converter</div>
    <input type="number" id="unit-amount" placeholder="Amount" style="width:80px;"> 
    <select id="unit-from">
      <option value="cm">cm</option>
      <option value="m">m</option>
      <option value="km">km</option>
      <option value="in">in</option>
      <option value="ft">ft</option>
      <option value="yd">yd</option>
      <option value="mi">mi</option>
    </select>
    to
    <select id="unit-to">
      <option value="cm">cm</option>
      <option value="m">m</option>
      <option value="km">km</option>
      <option value="in">in</option>
      <option value="ft">ft</option>
      <option value="yd">yd</option>
      <option value="mi">mi</option>
    </select>
    <button id="convert-unit">Convert</button>
    <div id="unit-result" style="margin-top:1em;"></div>
  </div>
<script>
// Tabs logic
const tabs = document.querySelectorAll('.tab');
const panels = document.querySelectorAll('.panel');
tabs.forEach(tab => {
  tab.addEventListener('click', () => {
    tabs.forEach(t => t.classList.remove('active'));
    panels.forEach(p => p.classList.remove('active'));
    tab.classList.add('active');
    document.getElementById(tab.dataset.panel).classList.add('active');
  });
});

// Calculator logic with history
const display = document.getElementById('display');
const buttons = document.getElementById('buttons');
const historyDiv = document.getElementById('history');
let current = '';
let history = [];

function safeEval(expr) {
  expr = expr.replace(/(\d+(?:\.\d+)?)\s*\^\s*(\d+(?:\.\d+)?)/g, 'Math.pow($1,$2)');
  expr = expr.replace(/√(\d+(?:\.\d+)?)/g, 'Math.sqrt($1)');
  expr = expr.replace(/log(\d+(?:\.\d+)?)/g, 'Math.log10($1)');
  expr = expr.replace(/ln(\d+(?:\.\d+)?)/g, 'Math.log($1)');
  expr = expr.replace(/sin(\d+(?:\.\d+)?)/g, 'Math.sin($1*Math.PI/180)');
  expr = expr.replace(/cos(\d+(?:\.\d+)?)/g, 'Math.cos($1*Math.PI/180)');
  expr = expr.replace(/tan(\d+(?:\.\d+)?)/g, 'Math.tan($1*Math.PI/180)');
  return eval(expr);
}

function updateHistory() {
  historyDiv.innerHTML = history.map(item => `<div>${item}</div>`).join('');
}

buttons.addEventListener('click', function(e) {
  if (e.target.tagName !== 'BUTTON') return;
  const value = e.target.textContent;

  if (value === 'C') {
    current = '';
    display.textContent = '0';
  } else if (value === '=') {
    try {
      let result = safeEval(current);
      history.unshift(current + ' = ' + result);
      if (history.length > 10) history.pop();
      updateHistory();
      current = result.toString();
      display.textContent = current;
    } catch {
      display.textContent = 'Error';
      current = '';
    }
  } else if (["√", "log", "ln", "sin", "cos", "tan"].includes(value)) {
    current += value;
    display.textContent = current;
  } else {
    current += value;
    display.textContent = current;
  }
});

// Currency converter logic (static rates for demo)
document.getElementById('convert-currency').onclick = function() {
  const amount = parseFloat(document.getElementById('currency-amount').value);
  const from = document.getElementById('currency-from').value;
  const to = document.getElementById('currency-to').value;
  const rates = { USD: 1, EUR: 0.93, GBP: 0.8 };
  if (isNaN(amount)) {
    document.getElementById('currency-result').textContent = 'Enter a valid amount.';
    return;
  }
  const result = (amount / rates[from] * rates[to]).toFixed(2);
  document.getElementById('currency-result').textContent = `${amount} ${from} = ${result} ${to}`;
};

// Unit converter logic (length only, demo)
const unitFactors = {
  cm: 1,
  m: 100,
  km: 100000,
  in: 2.54,
  ft: 30.48,
  yd: 91.44,
  mi: 160934
};
document.getElementById('convert-unit').onclick = function() {
  const amount = parseFloat(document.getElementById('unit-amount').value);
  const from = document.getElementById('unit-from').value;
  const to = document.getElementById('unit-to').value;
  if (isNaN(amount)) {
    document.getElementById('unit-result').textContent = 'Enter a valid amount.';
    return;
  }
  const result = (amount * unitFactors[from] / unitFactors[to]).toFixed(4);
  document.getElementById('unit-result').textContent = `${amount} ${from} = ${result} ${to}`;
};

</script>
  <div class="watermark">Created by V</div>
</body>
</html>
