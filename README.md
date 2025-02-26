# Molten-Pig-Iron-Temperature-Calculation-App
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>溶銑温度予測アプリ (Newton's Cooling)</title>
  <style>
    body {
      font-family: sans-serif;
      max-width: 500px;
      margin: 20px auto;
    }
    label {
      display: inline-block;
      width: 150px;
      margin-top: 8px;
    }
    input[type="number"] {
      width: 100px;
    }
    .readonly-input {
      background-color: #eee;
      border: 1px solid #aaa;
      width: 100px;
    }
    #calc-btn {
      margin-top: 15px;
      padding: 6px 12px;
      cursor: pointer;
    }
    #result {
      font-weight: bold;
      color: #d33;
      margin-top: 15px;
    }
  </style>
</head>
<body>

<h2>溶銑温度予測アプリ</h2>

<div>
  <label>溶銑質量 [t]</label>
  <input id="ton" type="number" step="any" value="400">
</div>
<div>
  <label>初期温度 [℃]</label>
  <input id="temp_init" type="number" step="any" value="1500">
</div>
<div>
  <label>外気温 [℃]</label>
  <input id="temp_env" type="number" step="any" value="10">
</div>
<div>
  <label>経過時間 [h]</label>
  <input id="time_h" type="number" step="any" value="24">
</div>

<!-- 固定値 -->
<div>
  <label>比熱容量 [J/(kg·K)]</label>
  <input id="cp" class="readonly-input" type="number" step="any" value="700" readonly>
</div>
<div>
  <label>有効熱伝達率 [W/K]</label>
  <input id="UA" class="readonly-input" type="number" step="any" value="200" readonly>
</div>

<button id="calc-btn">計算</button>

<div id="result"></div>

<script>
function calcTemp() {
  // 入力値の取得
  const tonValue     = parseFloat(document.getElementById('ton').value);        // 溶銑質量(t)
  const tempInit     = parseFloat(document.getElementById('temp_init').value);  // 初期温度(℃)
  const tempEnv      = parseFloat(document.getElementById('temp_env').value);   // 外気温(℃)
  const timeHours    = parseFloat(document.getElementById('time_h').value);     // 経過時間(h)
  // 固定値（編集不可）
  const cpValue      = parseFloat(document.getElementById('cp').value);         // 比熱容量 [J/(kg·K)]
  const UAValue      = parseFloat(document.getElementById('UA').value);         // 有効熱伝達率 [W/K=J/s/K]

  // 単位変換 (t -> kg)
  const massKg = tonValue * 1000;

  // 冷却係数 (1/hに変換)
  // [1/s] = (UA / (m*c_p))
  // [1/h] = [1/s] * 3600
  const kPerHour = (UAValue / (massKg * cpValue)) * 3600;

  // ニュートンの冷却則 T(t) = T_env + [T(0) - T_env]*exp(-k*t)
  // timeHours [h], kPerHour [1/h]
  const tempAfter = tempEnv + (tempInit - tempEnv) * Math.exp(-kPerHour * timeHours);

  // 結果を表示 (小数点1桁で丸め)
  document.getElementById('result').textContent =
    `予測溶銑温度: ${tempAfter.toFixed(1)} ℃`;
}

// イベント設定
document.getElementById('calc-btn').addEventListener('click', calcTemp);
</script>

</body>
</html>
