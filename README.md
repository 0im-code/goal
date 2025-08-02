<!DOCTYPE html>
<html>
<head>
  <title>Monthly Goal Tracker</title>
  <style>
    body { font-family: Arial; padding: 20px; }
    table, td, th { border: 1px solid #888; border-collapse: collapse; }
    td, th { padding: 5px; text-align: center; }
    input[type="text"] { margin-right: 10px; }
    .goal-row { background-color: #f9f9f9; }
  </style>
</head>
<body>

  <h1>📅 Monthly Goal Tracker</h1>

  <label for="monthPicker">Choose a month: </label>
  <input type="month" id="monthPicker" value="2025-07">
  <br><br>

  <input type="text" id="goalInput" placeholder="Add new goal">
  <button onclick="addGoal()">➕ Add Goal</button>
  <br><br>

  <table id="goalTable">
    <thead id="tableHead"></thead>
    <tbody id="tableBody"></tbody>
  </table>

  <script>
    const tableHead = document.getElementById("tableHead");
    const tableBody = document.getElementById("tableBody");
    const monthPicker = document.getElementById("monthPicker");

    let goals = [];

    function generateTable() {
      const [year, month] = monthPicker.value.split("-");
      const daysInMonth = new Date(year, month, 0).getDate();

      // Build header row
      let headerHTML = "<tr><th>Goal</th>";
      for (let d = 1; d <= daysInMonth; d++) {
        headerHTML += `<th>${d}</th>`;
      }
      headerHTML += "<th>✅ Done</th><th>❌ Delete</th></tr>";
      tableHead.innerHTML = headerHTML;

      // Build goal rows
      tableBody.innerHTML = "";
      goals.forEach((goal, goalIndex) => {
        let rowHTML = `<tr class="goal-row"><td>${goal.name}</td>`;
        for (let d = 1; d <= daysInMonth; d++) {
          const checked = goal.days.includes(d) ? "checked" : "";
          rowHTML += `<td><input type="checkbox" ${checked} onchange="toggleDay(${goalIndex}, ${d})"></td>`;
        }
        rowHTML += `<td>${goal.days.length}</td>`;
        rowHTML += `<td><button onclick="deleteGoal(${goalIndex})">🗑️</button></td></tr>`;
        tableBody.innerHTML += rowHTML;
      });
    }

    function addGoal() {
      const goalName = document.getElementById("goalInput").value.trim();
      if (goalName === "") return;
      goals.push({ name: goalName, days: [] });
      document.getElementById("goalInput").value = "";
      generateTable();
    }

    function toggleDay(goalIndex, day) {
      const goal = goals[goalIndex];
      if (goal.days.includes(day)) {
        goal.days = goal.days.filter(d => d !== day);
      } else {
        goal.days.push(day);
      }
      generateTable();
    }

    function deleteGoal(goalIndex) {
      goals.splice(goalIndex, 1);
      generateTable();
    }

    // Regenerate table when month changes
    monthPicker.addEventListener("change", generateTable);

    // Initial table
    generateTable();
  </script>

</body>
</html>
