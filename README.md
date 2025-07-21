<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>NeoCore Esport Tournament</title>
  <style>
    * { box-sizing: border-box; }
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      background-color: #121212;
      color: #fff;
    }
    header {
      background-color: #1f1f1f;
      padding: 1em;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    header h1 { margin: 0; }
    .admin-status {
      font-size: 0.9em;
      color: #4caf50;
      margin-right: 1em;
    }
    nav {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 1em;
      background: #2a2a2a;
      padding: 1em;
    }
    nav a {
      color: white;
      text-decoration: none;
      font-weight: bold;
      cursor: pointer;
    }
    .container { padding: 2em; }
    .page { display: none; }
    .page.active { display: block; }
    form {
      max-width: 400px;
      margin: auto;
      background-color: #1f1f1f;
      padding: 2em;
      border-radius: 8px;
    }
    label {
      display: block;
      margin-bottom: 0.5em;
    }
    input, button {
      width: 100%;
      padding: 0.75em;
      margin-bottom: 1em;
      border: none;
      border-radius: 4px;
    }
    input {
      background-color: #2a2a2a;
      color: #fff;
    }
    button {
      background-color: #007bff;
      color: #fff;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover { background-color: #0056b3; }
    .admin-section {
      max-width: 700px;
      margin: 2em auto;
      background: #1f1f1f;
      padding: 1em;
      border-radius: 8px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      color: white;
      margin-top: 1em;
    }
    table, th, td { border: 1px solid #444; }
    th, td {
      padding: 0.75em;
      text-align: left;
    }
    .error { color: red; text-align: center; }
    .success {
      color: #4caf50;
      text-align: center;
      margin-bottom: 1em;
    }
    @media (max-width: 600px) {
      nav { flex-direction: column; }
      form, .admin-section { padding: 1em; }
      header { flex-direction: column; align-items: flex-start; }
    }
  </style>
</head>
<body>
  <header>
    <h1>NeoCore ESports Tournament</h1>
    <div id="admin-status" class="admin-status"></div>
  </header>

  <nav>
    <a onclick="showPage('register')">Register</a>
    <a onclick="showPage('login')">Login</a>
    <a onclick="showPage('tournament')">Tournament Registration</a>
    <a onclick="showPage('admin')">Admin Panel</a>
  </nav>

  <div class="container">
    <section id="register" class="page">
      <h2>Register</h2>
      <form onsubmit="event.preventDefault(); registerUser(event)">
        <label>Username</label>
        <input type="text" required placeholder="Enter username" />
        <label>Email</label>
        <input type="email" required placeholder="Enter email" />
        <label>Password</label>
        <input type="password" required placeholder="Enter password" />
        <button type="submit">Register</button>
      </form>
    </section>

    <section id="login" class="page">
      <h2>Login</h2>
      <p id="register-success" class="success" style="display: none;">Registration successful. Please log in.</p>
      <p id="login-error" class="error"></p>
      <form onsubmit="event.preventDefault(); loginUser(event)">
        <label>Email</label>
        <input type="email" id="login-email" required placeholder="Enter email" />
        <label>Password</label>
        <input type="password" id="login-password" required placeholder="Enter password" />
        <button type="submit">Login</button>
      </form>
    </section>

    <section id="tournament" class="page">
      <h2>Tournament Registration</h2>
      <form onsubmit="event.preventDefault(); registerTeam(event)">
        <label>Team Name</label>
        <input type="text" id="team-name" placeholder="Enter team name" required />
        <label>Captain Email</label>
        <input type="email" id="captain-email" placeholder="Enter email" required />
        <button type="submit">Register Team</button>
      </form>
    </section>

    <section id="verification" class="page">
      <h2>Thanks for your verification!</h2>
      <p>Your account has been successfully verified. You may now log in and register for tournaments.</p>
      <button onclick="showPage('login')">Back to Login</button>
    </section>

    <section id="admin" class="admin-section page">
      <h2>Admin Login</h2>
      <form onsubmit="event.preventDefault(); showAccounts();">
        <label>Admin Email</label>
        <input type="email" id="admin-email" placeholder="Admin email" required />
        <label>Password</label>
        <input type="password" id="admin-password" placeholder="Password" required />
        <button type="submit">Login as Admin</button>
        <p id="admin-error" class="error"></p>
      </form>

      <div id="admin-panel" style="display: none;">
        <h3>Registered Users</h3>
        <table>
          <thead>
            <tr><th>Username</th><th>Email</th></tr>
          </thead>
          <tbody id="accounts-table"></tbody>
        </table>

        <h3 style="margin-top:2em;">Tournament Teams</h3>
        <table>
          <thead>
            <tr><th>Team Name</th><th>Captain Email</th></tr>
          </thead>
          <tbody id="teams-table"></tbody>
        </table>
      </div>
    </section>
  </div>

  <script>
    const registeredUsers = [];
    const registeredTeams = [];

    function showPage(pageId) {
      document.querySelectorAll('.page').forEach(page => page.classList.remove('active'));
      document.getElementById(pageId)?.classList.add('active');
    }

    function registerUser(event) {
      const inputs = event.target.querySelectorAll('input');
      const username = inputs[0].value;
      const email = inputs[1].value;
      const password = inputs[2].value;

      registeredUsers.push({ username, email, password });
      event.target.reset();

      document.getElementById('register-success').style.display = 'block';
      showPage('login');
    }

    function loginUser(event) {
      const email = document.getElementById('login-email').value;
      const password = document.getElementById('login-password').value;
      const errorEl = document.getElementById('login-error');

      const user = registeredUsers.find(u => u.email === email && u.password === password);

      if (user) {
        errorEl.textContent = '';
        showPage('tournament');
      } else {
        errorEl.textContent = 'Invalid email or password.';
      }
    }

    function registerTeam(event) {
      const teamName = document.getElementById('team-name').value;
      const captainEmail = document.getElementById('captain-email').value;
      registeredTeams.push({ teamName, captainEmail });

      event.target.reset();
      showPage('verification');
    }

    function showAccounts() {
      const email = document.getElementById("admin-email").value;
      const password = document.getElementById("admin-password").value;
      const errorMsg = document.getElementById("admin-error");
      const adminPanel = document.getElementById("admin-panel");
      const adminStatus = document.getElementById("admin-status");

      if (email === "admin@example.com" && password === "one") {
        errorMsg.textContent = "";
        adminPanel.style.display = "block";
        adminStatus.textContent = "Logged in as Admin";

        const accountsTable = document.getElementById("accounts-table");
        const teamsTable = document.getElementById("teams-table");

        accountsTable.innerHTML = registeredUsers
          .map(user => `<tr><td>${user.username}</td><td>${user.email}</td></tr>`)
          .join("");

        teamsTable.innerHTML = registeredTeams
          .map(team => `<tr><td>${team.teamName}</td><td>${team.captainEmail}</td></tr>`)
          .join("");
      } else {
        adminPanel.style.display = "none";
        errorMsg.textContent = "Invalid admin credentials.";
        adminStatus.textContent = "";
      }
    }

    // Default to Register page
    window.onload = () => showPage('register');
  </script>
</body>
</html>
