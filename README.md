- 👋 Hi, I’m @aplusbdesign
- 👀 I’m interested in AI, ML, DS, CV, etc.
- 🌱 I’m currently learning OpenCV, Python
- 💞️ I’m looking to collaborate on CV of Colors
- 📫 How to reach me ...

<!---
aplusbdesign/aplusbdesign is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
<!DOCTYPE html><html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Palantir (PLTR) 주가 분석</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: sans-serif;
      padding: 2rem;
    }
    h1 {
      font-size: 1.8rem;
      font-weight: bold;
    }
    .card {
      border: 1px solid #ddd;
      border-radius: 10px;
      padding: 1rem;
      margin-top: 2rem;
      box-shadow: 0 2px 8px rgba(0,0,0,0.05);
    }
    button {
      padding: 0.5rem 1rem;
      font-size: 1rem;
      margin-top: 1rem;
    }
    .result {
      margin-top: 1rem;
      font-weight: 500;
      font-size: 1.1rem;
    }
  </style>
</head>
<body>
  <h1>Palantir (PLTR) 주가 분석</h1>
  <button onclick="fetchData()">데이터 새로고침</button>
  <p id="analysis" class="result"></p>
  <div class="card">
    <canvas id="stockChart"></canvas>
  </div>  <script>
    async function fetchData() {
      try {
        const response = await fetch("https://api.example.com/palantir/historical"); // 실제 API로 교체 필요
        const data = await response.json();
        renderChart(data);
        analyzeData(data);
      } catch (error) {
        document.getElementById("analysis").textContent = "데이터 불러오기 실패";
        console.error("Error fetching stock data:", error);
      }
    }

    function renderChart(data) {
      const ctx = document.getElementById("stockChart").getContext("2d");
      const chartData = {
        labels: data.map(d => d.date),
        datasets: [
          {
            label: "PLTR 종가",
            data: data.map(d => d.close),
            borderColor: "blue",
            borderWidth: 2,
            fill: false
          }
        ]
      };

      new Chart(ctx, {
        type: "line",
        data: chartData,
        options: {
          scales: {
            x: {
              display: true,
              title: {
                display: true,
                text: "날짜"
              }
            },
            y: {
              display: true,
              title: {
                display: true,
                text: "종가 ($)"
              }
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
  </script></body>
</html>
