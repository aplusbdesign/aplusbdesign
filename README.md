- 👋 Hi, I’m @aplusbdesign
- 👀 I’m interested in AI, ML, DS, CV, etc.
- 🌱 I’m currently learning OpenCV, Python
- 💞️ I’m looking to collaborate on CV of Colors
- 📫 How to reach me ...

<!---
aplusbdesign/aplusbdesign is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Palantir (PLTR) 주가 분석</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
      padding: 1rem;
      margin: 0;
      background: #f9f9f9;
    }
    h1 {
      font-size: 1.6rem;
      font-weight: bold;
      margin-bottom: 1rem;
      text-align: center;
    }
    .card {
      background: #fff;
      border-radius: 12px;
      padding: 1rem;
      margin-top: 1.5rem;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    }
    button {
      display: block;
      width: 100%;
      max-width: 300px;
      margin: 0 auto;
      padding: 0.75rem 1rem;
      font-size: 1rem;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    .result {
      margin-top: 1rem;
      font-weight: 500;
      font-size: 1rem;
      text-align: center;
    }
    canvas {
      max-width: 100%;
      height: 300px !important;
    }
  </style>
</head>
<body>
  <h1>Palantir (PLTR) 주가 분석</h1>
  <button onclick="fetchData()">데이터 새로고침</button>
  <p id="analysis" class="result"></p>
  <div class="card">
    <canvas id="stockChart"></canvas>
  </div>

  <script>
    async function fetchData() {
      const analysisEl = document.getElementById("analysis");
      try {
        const now = Math.floor(Date.now() / 1000);
        const from = now - 5 * 365 * 24 * 60 * 60;
        const url = `https://finnhub.io/api/v1/stock/candle?symbol=PLTR&resolution=D&from=${from}&to=${now}&token=d13paphr01qs7glj2nsgd13paphr01qs7glj2nt0`;

        console.log("요청 URL:", url);
        const res = await fetch(url);

        if (!res.ok) {
          analysisEl.textContent = `HTTP 오류 발생: 상태코드 → ${res.status}`;
          return;
        }

        const json = await res.json();
        console.log("API 응답 결과:", json);

        if (!json || typeof json !== "object") {
          analysisEl.textContent = "API 응답이 비어있거나 잘못되었습니다.";
          return;
        }

        if (json.s !== "ok") {
          analysisEl.textContent = `데이터 오류 발생: API 상태코드 → ${json.s || '없음'}`;
          return;
        }

        const stockData = json.c.map((close, idx) => ({
          date: new Date(json.t[idx] * 1000).toISOString().slice(0, 10),
          close
        }));

        renderChart(stockData);
        analyzeData(stockData);
      } catch (e) {
        console.error("API 호출 중 오류:", e);
        analysisEl.textContent = `API 호출 오류: ${e.message || '네트워크 오류 또는 CORS 차단 가능성'}`;
      }
    }

    function renderChart(data) {
      const ctx = document.getElementById("stockChart").getContext("2d");
      new Chart(ctx, {
        type: "line",
        data: {
          labels: data.map(d => d.date),
          datasets: [{
            label: "PLTR 종가",
            data: data.map(d => d.close),
            borderColor: "#007bff",
            borderWidth: 2,
            fill: false
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            x: {
              title: { display: true, text: "날짜" }
            },
            y: {
              title: { display: true, text: "종가 ($)" }
            }
          }
        }
      });
    }

    function analyzeData(data) {
      const prices = data.map(d => d.close);
      const currentPrice = prices[prices.length - 1];
      const high52 = Math.max(...prices.slice(-252));
      const low52 = Math.min(...prices.slice(-252));
      let recommendation = "";
      if (currentPrice < low52 * 1.1) {
        recommendation = "매수 유망 구간입니다.";
      } else if (currentPrice > high52 * 0.9) {
        recommendation = "매도 유망 구간입니다.";
      } else {
        recommendation = "관망이 필요한 시점입니다.";
      }
      document.getElementById("analysis").textContent =
        `현재가: $${currentPrice.toFixed(2)} | 52주 최저: $${low52} | 52주 최고: $${high52} → ${recommendation}`;
    }

    window.onload = fetchData;
  </script>
</body>
</html>
