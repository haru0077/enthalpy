<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>엔탈피 실험 – 발열·흡열 지원</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body { font-family: 'Segoe UI', sans-serif; text-align: center; margin: 20px; }
    .container { width: 600px; margin: auto; }
    input, button { padding: 8px; width: 90%; margin: 8px 0; font-size: 1em; }
    canvas { display: block; margin: 20px auto; }
    .explain { font-size: 1.1em; margin-top: 10px; }
  </style>
</head>
<body>
  <div class="container">
    <h2>🔥❄️ 엔탈피 실험 계산기</h2>

    <input type="number" id="mass" placeholder="물질 질량 (g)">
    <input type="number" id="specificHeat" placeholder="비열 (물=4.18 J/g°C)">
    <input type="number" id="tempChange" placeholder="온도 변화 ΔT (°C)">
    <input type="number" id="moles" placeholder="몰 수 (mol)">
    <button onclick="runExperiment()">계산 및 시각화</button>

    <div class="explain" id="explanation"></div>
    <canvas id="chartCanvas" width="600" height="300"></canvas>
  </div>

  <script>
    function runExperiment() {
      // 입력값 읽기
      const m = parseFloat(document.getElementById('mass').value);
      const c = parseFloat(document.getElementById('specificHeat').value);
      const dT = parseFloat(document.getElementById('tempChange').value);
      const n = parseFloat(document.getElementById('moles').value);
      if ([m, c, dT, n].some(isNaN)) {
        alert('모든 값을 입력해주세요!');
        return;
      }

      // 엔탈피 계산
      const q = m * c * dT;        // q = m·c·ΔT
      const ΔH = q / n;            // ΔH = q / n

      // 반응 유형 판별
      const isExo = ΔH < 0;
      const typeLabel = isExo
        ? '발열반응 (열 방출🔥)'
        : '흡열반응 (열 흡수❄️)';

      // 결과 텍스트 출력
      document.getElementById('explanation').innerHTML = `
        이 반응은 <b>${typeLabel}</b><br>
        전체 열량 Q = ${q.toFixed(2)} J<br>
        몰당 엔탈피 ΔH = ${ΔH.toFixed(2)} J/mol
      `;

      // 차트 그리기
      drawChart(Math.abs(q), Math.abs(ΔH), isExo);
    }

    function drawChart(qAbs, ΔHAbs, isExo) {
      const ctx = document.getElementById('chartCanvas').getContext('2d');
      if (window._enthalpyChart) window._enthalpyChart.destroy();
      window._enthalpyChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: ['전체 열량 Q', '몰당 엔탈피 ΔH'],
          datasets: [{
            label: '절댓값 (J)',
            data: [qAbs, ΔHAbs],
            backgroundColor: isExo
              ? ['#FF6347', '#FF6347']  // 발열: 빨간 계열
              : ['#1E90FF', '#1E90FF']  // 흡열: 파란 계열
          }]
        },
        options: {
          scales: { y: { beginAtZero: true } },
          plugins: { legend: { display: false } }
        }
      });
    }
  </script>
</body>
</html>
