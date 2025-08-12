
<html lang="en">
<head>
<meta charset="UTF-8"> <!-- Sets character encoding so emojis and special symbols work -->
<meta name="viewport" content="width=device-width, initial-scale=1.0"> <!-- Makes the page mobile-friendly -->
<title>Task & Mood Tracker</title>

<!-- Load Chart.js from a CDN (used for the mood bar chart) -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
  /* ====== GENERAL PAGE STYLE ====== */
  body {
    font-family: Arial, sans-serif; /* Nice clean font */
    background: #f4f4f4; /* Light gray background for contrast */
    padding: 20px; /* Space between the browser edge and content */
  }

  h1 {
    text-align: center; /* Centers the main title */
  }

  /* ====== MAIN CONTAINER (white box in the center) ====== */
  .container {
    max-width: 800px;       /* Prevents it from being too wide */
    margin: auto;           /* Centers the box horizontally */
    background: white;      /* White background for content */
    padding: 20px;          /* Space inside the box */
    border-radius: 8px;     /* Rounded corners */
  }

  /* ====== SECTIONS ====== */
  .task-section, .mood-section, .dashboard {
    margin-top: 20px; /* Space between sections */
  }

  /* ====== INPUTS & BUTTONS ====== */
  input, select, button {
    padding: 8px;        /* Makes them easier to click */
    margin-right: 5px;   /* Space between elements */
  }

  /* ====== TASK LIST ====== */
  ul {
    list-style: none; /* Removes bullet points */
    padding: 0;       /* Removes extra left padding */
  }

  li {
    padding: 5px; /* Space around task text */
    border-bottom: 1px solid #ddd; /* Light gray separator line */
    display: flex; /* Allows placing text and delete button side-by-side */
    justify-content: space-between; /* Pushes delete button to the right */
  }

  li.completed {
    text-decoration: line-through; /* Cross out completed tasks */
    color: gray; /* Dim the color for completed tasks */
  }
</style>
</head>
<body>

<h1>Task & Mood Tracker</h1>
<div class="container">

  <!-- ====== TASK SECTION ====== -->
  <div class="task-section">
    <h2>Tasks</h2>
    <!-- Input field to type a task -->
    <input type="text" id="taskInput" placeholder="Add a task...">
    <!-- Button that calls addTask() when clicked -->
    <button onclick="addTask()">Add Task</button>
    <!-- Unordered list that will hold the tasks -->
    <ul id="taskList"></ul>
  </div>

  <!-- ====== MOOD SECTION ====== -->
  <div class="mood-section">
    <h2>Mood</h2>
    <!-- Dropdown for selecting a mood -->
    <select id="moodSelect">
      <option value="😊">😊 Happy</option>
      <option value="😐">😐 Neutral</option>
      <option value="😢">😢 Sad</option>
    </select>
    <!-- Button that logs the selected mood -->
    <button onclick="addMood()">Log Mood</button>
  </div>

  <!-- ====== DASHBOARD ====== -->
  <div class="dashboard">
    <h2>Dashboard</h2>
    <!-- Shows task stats -->
    <p id="taskStats">Completed: 0 | Pending: 0</p>
    <!-- Canvas where Chart.js will draw the mood chart -->
    <canvas id="moodChart" height="100"></canvas>
  </div>

</div>

<script>
/* ====== DATA STORAGE ====== */
let tasks = []; // Stores task objects { text: "...", completed: true/false }
let moods = { "😊": 0, "😐": 0, "😢": 0 }; // Counts for each mood

/* ====== ELEMENT REFERENCES ====== */
const taskList = document.getElementById("taskList"); // Where tasks are displayed
const taskStats = document.getElementById("taskStats"); // The stats text
const ctx = document.getElementById("moodChart").getContext("2d"); // Chart.js drawing area

/* ====== INITIALIZE MOOD CHART ====== */
let moodChart = new Chart(ctx, {
  type: 'bar', // Bar chart
  data: {
    labels: ["😊", "😐", "😢"], // X-axis labels
    datasets: [{
      label: 'Mood Count', // Chart title
      data: [0, 0, 0], // Starting counts for each mood
      backgroundColor: ['#ffcd56', '#36a2eb', '#ff6384'] // Colors for bars
    }]
  },
  options: {
    responsive: true, // Adjust size for screen
    scales: {
      y: { beginAtZero: true } // Start Y-axis at 0
    }
  }
});

/* ====== ADDING A TASK ====== */
function addTask() {
  const taskInput = document.getElementById("taskInput"); // Get input box
  if (taskInput.value.trim() === "") return; // Ignore empty inputs
  // Add new task object to the tasks array
  tasks.push({ text: taskInput.value, completed: false });
  taskInput.value = ""; // Clear input box
  renderTasks(); // Refresh the displayed task list
}

/* ====== TOGGLE TASK COMPLETION ====== */
function toggleTask(index) {
  tasks[index].completed = !tasks[index].completed; // Flip true <-> false
  renderTasks(); // Update display
}

/* ====== DELETE A TASK ====== */
function deleteTask(index) {
  tasks.splice(index, 1); // Remove task from array
  renderTasks(); // Update display
}

/* ====== DISPLAY TASKS ON SCREEN ====== */
function renderTasks() {
  taskList.innerHTML = ""; // Clear old tasks
  let completed = 0; // Counter for completed tasks

  // Loop through all tasks
  tasks.forEach((task, index) => {
    if (task.completed) completed++; // Count completed ones

    const li = document.createElement("li"); // Create list item
    li.className = task.completed ? "completed" : ""; // Add style if completed

    // Task text (clicking toggles completion)
    // ❌ button deletes the task
    li.innerHTML = `
      <span onclick="toggleTask(${index})">${task.text}</span>
      <button onclick="deleteTask(${index})">❌</button>
    `;

    taskList.appendChild(li); // Add to the task list
  });

  // Update stats display
  taskStats.textContent = `Completed: ${completed} | Pending: ${tasks.length - completed}`;
}

/* ====== ADD A MOOD ====== */
function addMood() {
  const mood = document.getElementById("moodSelect").value; // Get selected mood
  moods[mood]++; // Increase its count
  updateMoodChart(); // Refresh chart
}

/* ====== UPDATE THE MOOD CHART ====== */
function updateMoodChart() {
  // Update chart data with current mood counts
  moodChart.data.datasets[0].data = [moods["😊"], moods["😐"], moods["😢"]];
  moodChart.update(); // Redraw chart
}
</script>

</body>
</html>
