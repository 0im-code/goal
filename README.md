<!DOCTYPE html>
<html>
<head>
  <title>monthly Goal Tracker</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 1000px;
      margin: auto;
      padding: 20px;
    }

    h1 {
      text-align: center;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
    }

    th, td {
      border: 1px solid #ccc;
      padding: 8px;
      text-align: center;
      min-width: 80px;
    }

    th {
      background-color: #f0f0f0;
    }

    .yes {
      background-color: #c8e6c9;
    }

    .no {
      background-color: #ffcdd2;
    }

    .goal-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }

    .delete-btn {
      margin-left: 5px;
      cursor: pointer;
      color: red;
      font-weight: bold;
    }

    #addGoalForm {
      display: flex;
      justify-content: center;
      margin-top: 20px;
      gap: 10px;
    }

    #goalInput {
      padding: 8px;
      font-size: 14px;
      width: 60%;
    }

    #addGoalBtn {
      padding: 8px 16px;
      font-size: 14px;
    }

    .clickable {
      cursor: pointer;
    }

    #chartContainer {
      margin-top: 40px;
    }

    canvas {
      max-width: 100%;
    }
  </style>
</head>
<body>
  <h1>31 days Goal Tracker</h1>

  <div id="addGoalForm">
    <input type="text" id="goalInput" placeholder="Enter new goal..." />
    <button id="addGoalBtn">Add Goal</button>
  </div>

  <table id="goalTable"></table>

  <div id="chartContainer">
    <h2>Goal Completion Chart</h2>
    <canvas id="goalChart"></canvas>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script>
    const table = document.getElementById("goalTable");
    const goalInput = document.getElementById("goalInput");
    const addGoalBtn = document.getElementById("addGoalBtn");

    const year = any;
    const month = 1,3,5,7,8,10,or12; // January,march,may,july,august,october,ordecember = 1,3,5,7,8,10,or12
    const daysInMonth = 31;
    const storageKey = "goal-grid-july2025";

    let goalData = {
      goals: [],
      grid: {}
    };

    let chart;

    function loadData() {
      const saved = localStorage.getItem(storageKey);
      if (saved) {
        goalData = JSON.parse(saved);
      }
    }

    function saveData() {
      localStorage.setItem(storageKey, JSON.stringify(goalData));
    }

    function renderTable() {
      table.innerHTML = "";

      const thead = document.createElement("thead");
      const headerRow = document.createElement("tr");
      const dateTh = document.createElement("th");
      dateTh.textContent = "Date";
      headerRow.appendChild(dateTh);

      goalData.goals.forEach((goal, i) => {
        const th = document.createElement("th");
        const div = document.createElement("div");
        div.className = "goal-header";

        const span = document.createElement("span");
        span.textContent = goal;

        const del = document.createElement("span");
        del.className = "delete-btn";
        del.textContent = "🗑️";
        del.onclick = () => {
          goalData.goals.splice(i, 1);
          for (let d = 1; d <= daysInMonth; d++) {
            const dayKey = d.toString();
            if (goalData.grid[dayKey]) {
              delete goalData.grid[dayKey][goal];
            }
          }
          saveData();
          renderTable();
        };

        div.appendChild(span);
        div.appendChild(del);
        th.appendChild(div);
        headerRow.appendChild(th);
      });

      thead.appendChild(headerRow);
      table.appendChild(thead);

      const tbody = document.createElement("tbody");

      for (let d = 1; d <= daysInMonth; d++) {
        const tr = document.createElement("tr");
        const dateCell = document.createElement("td");
        dateCell.textContent = ${d}`;
        tr.appendChild(dateCell);

        const dayKey = d.toString();
        if (!goalData.grid[dayKey]) {
          goalData.grid[dayKey] = {};
        }

        goalData.goals.forEach(goal => {
          const td = document.createElement("td");
          td.className = "clickable";

          const value = goalData.grid[dayKey][goal];
          if (value === true) {
            td.classList.add("yes");
            td.textContent = "✅";
          } else if (value === false) {
            td.classList.add("no");
            td.textContent = "❌";
          } else {
            td.textContent = "";
          }

          td.onclick = () => {
            const current = goalData.grid[dayKey][goal];
            goalData.grid[dayKey][goal] =
              current === true ? false :
              current === false ? undefined :
              true;
            saveData();
            renderTable();
          };

          tr.appendChild(td);
        });

        tbody.appendChild(tr);
      }

      table.appendChild(tbody);
      renderChart();
    }

    addGoalBtn.onclick = () => {
      const goalName = goalInput.value.trim();
      if (!goalName) return;

      if (!goalData.goals.includes(goalName)) {
        goalData.goals.push(goalName);
        saveData();
        goalInput.value = "";
        renderTable();
      }
    };

    function renderChart() {
      const counts = {};
      for (let goal of goalData.goals) {
        counts[goal] = 0;
      }

      for (let day in goalData.grid) {
        for (let goal in goalData.grid[day]) {
          if (goalData.grid[day][goal] === true) {
            counts[goal]++;
          }
        }
      }

      const sorted = Object.entries(counts).sort((a, b) => b[1] - a[1]);
      const labels = sorted.map(x => x[0]);
      const data = sorted.map(x => x[1]);

      if (chart) chart.destroy();

      const ctx = document.getElementById("goalChart").getContext("2d");
      chart = new Chart(ctx, {
        type: "bar",
        data: {
          labels: labels,
          datasets: [{
            label: "✅ Days Completed",
            data: data,
            backgroundColor: "#4caf50"
          }]
        },
        options: {
          indexAxis: "y",
          responsive: true,
          scales: {
            x: { beginAtZero: true, ticks: { precision: 0 } },
          },
        }
      });
    }

    loadData();
    renderTable();
  </script>
</body>
</html>
