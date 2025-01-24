<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MoodJournal</title>
    <style>
        :root {
            --bg-color: #f4f4f4;
            --text-color: #333;
            --primary-color: #6200ea;
            --button-bg: #6200ea;
            --button-hover: #3700b3;
        }

        body.dark-mode {
            --bg-color: #1e1e1e;
            --text-color: #ffffff;
            --primary-color: #bb86fc;
            --button-bg: #bb86fc;
            --button-hover: #6200ea;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: Arial, sans-serif;
        }

        body {
            background: var(--bg-color);
            color: var(--text-color);
            transition: all 0.3s ease;
        }

        header {
            background: var(--primary-color);
            color: #fff;
            padding: 20px;
            text-align: center;
        }

        nav ul {
            list-style: none;
            display: flex;
            justify-content: center;
            margin-top: 10px;
        }

        nav ul li {
            margin: 0 15px;
        }

        nav ul li a {
            color: #fff;
            text-decoration: none;
            font-size: 1.2em;
            transition: color 0.3s ease;
        }

        .container {
            padding: 40px;
            text-align: center;
        }

        button {
            background: var(--button-bg);
            color: #fff;
            border: none;
            padding: 15px 30px;
            font-size: 1.2em;
            cursor: pointer;
            border-radius: 5px;
            margin: 10px;
            transition: background 0.3s ease;
        }

        button:hover {
            background: var(--button-hover);
        }

        textarea {
            width: 100%;
            max-width: 600px;
            height: 150px;
            padding: 15px;
            font-size: 1.1em;
            border: 1px solid #ccc;
            border-radius: 5px;
            margin: 20px 0;
        }

        #mood-history {
            max-width: 600px;
            margin: 0 auto;
            text-align: left;
        }

        #theme-toggle {
            position: fixed;
            top: 20px;
            right: 20px;
            cursor: pointer;
        }

        footer {
            text-align: center;
            padding: 20px;
            background: var(--primary-color);
            color: #fff;
            margin-top: 20px;
        }

        #login-form, #signup-form {
            display: none;
        }

        #user-info {
            display: none;
        }

        #analytics {
            margin-top: 40px;
        }

        #suggestions {
            margin-top: 20px;
        }

        .calendar {
            display: flex;
            flex-wrap: wrap;
            max-width: 400px;
            margin: 0 auto;
            justify-content: space-between;
            padding: 10px;
        }

        .calendar .day {
            width: 30px;
            height: 30px;
            border-radius: 5px;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
        }

        .calendar .day:hover {
            background-color: #6200ea;
            color: #fff;
        }
    </style>
</head>
<body>
    <header>
        <h1>MoodJournal</h1>
        <nav>
            <ul>
                <li><a href="#track">Mood Tracker</a></li>
                <li><a href="#journal">Journal</a></li>
                <li><a href="#history">Mood History</a></li>
                <li><a href="#insights">Insights</a></li>
                <li><a href="#calendar">Calendar</a></li>
            </ul>
        </nav>
    </header>

    <button id="theme-toggle">🌙</button>

    <div id="login-form" class="container">
        <h2>Login</h2>
        <input type="text" id="login-username" placeholder="Username" />
        <input type="password" id="login-password" placeholder="Password" />
        <button id="login-btn">Login</button>
        <button id="signup-link">Don't have an account? Sign up</button>
    </div>

    <div id="signup-form" class="container">
        <h2>Sign Up</h2>
        <input type="text" id="signup-username" placeholder="Username" />
        <input type="password" id="signup-password" placeholder="Password" />
        <button id="signup-btn">Sign Up</button>
        <button id="login-link">Already have an account? Login</button>
    </div>

    <div id="user-info" class="container">
        <h3>Welcome, <span id="username-display"></span></h3>
        <button id="logout-btn">Logout</button>
    </div>

    <section id="track" class="container">
        <h2>Track Your Mood</h2>
        <input type="text" id="custom-mood" placeholder="Add your mood..." />
        <button id="add-mood">Add Mood</button>
        <div id="mood-list"></div>
    </section>

    <section id="journal" class="container">
        <h2>Daily Journal</h2>
        <textarea id="journal-entry" placeholder="Write your thoughts here..."></textarea>
        <button id="save-entry">Save Entry</button>
    </section>

    <section id="history" class="container">
        <h2>Mood History</h2>
        <div id="mood-history"></div>
        <button id="export-data">Export to CSV</button>
    </section>

    <section id="analytics" class="container">
        <h2>Your Mood Analytics</h2>
        <canvas id="moodChart"></canvas>
    </section>

    <section id="suggestions" class="container">
        <h2>Personalized Suggestions</h2>
        <div id="suggestion-text"></div>
    </section>

    <section id="calendar" class="container">
        <h2>Calendar View</h2>
        <div id="calendar-view" class="calendar"></div>
    </section>

    <footer>
        <p>Created by Stephen Solomon</p>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const themeToggle = document.getElementById('theme-toggle');
            const loginForm = document.getElementById('login-form');
            const signupForm = document.getElementById('signup-form');
            const userInfo = document.getElementById('user-info');
            const loginBtn = document.getElementById('login-btn');
            const signupBtn = document.getElementById('signup-btn');
            const logoutBtn = document.getElementById('logout-btn');
            const usernameDisplay = document.getElementById('username-display');
            const moodHistory = document.getElementById('mood-history');
            const addMoodBtn = document.getElementById('add-mood');
            const customMoodInput = document.getElementById('custom-mood');
            const moodList = document.getElementById('mood-list');
            const saveEntryBtn = document.getElementById('save-entry');
            const journalEntry = document.getElementById('journal-entry');
            const exportBtn = document.getElementById('export-data');
            const analyticsSection = document.getElementById('analytics');
            const suggestionText = document.getElementById('suggestion-text');
            const signupLink = document.getElementById('signup-link');
            const loginLink = document.getElementById('login-link');
            const calendarView = document.getElementById('calendar-view');
            const moodData = JSON.parse(localStorage.getItem('moodData')) || [];
            const users = JSON.parse(localStorage.getItem('users')) || [];
            let currentUser = JSON.parse(localStorage.getItem('currentUser')) || null;

            if (currentUser) {
                loginForm.style.display = 'none';
                signupForm.style.display = 'none';
                userInfo.style.display = 'block';
                usernameDisplay.textContent = currentUser.username;
                loadMoodHistory();
                loadAnalytics();
                loadSuggestions();
                loadCalendar();
            } else {
                loginForm.style.display = 'block';
                signupForm.style.display = 'none';
                userInfo.style.display = 'none';
            }

            loginBtn.addEventListener('click', () => {
                const username = document.getElementById('login-username').value;
                const password = document.getElementById('login-password').value;
                const user = users.find(u => u.username === username && u.password === password);
                if (user) {
                    currentUser = user;
                    localStorage.setItem('currentUser', JSON.stringify(currentUser));
                    loginForm.style.display = 'none';
                    signupForm.style.display = 'none';
                    userInfo.style.display = 'block';
                    usernameDisplay.textContent = currentUser.username;
                    loadMoodHistory();
                    loadAnalytics();
                    loadSuggestions();
                    loadCalendar();
                } else {
                    alert('Invalid username or password');
                }
            });

            signupBtn.addEventListener('click', () => {
                const username = document.getElementById('signup-username').value;
                const password = document.getElementById('signup-password').value;
                if (username && password) {
                    const newUser = { username, password };
                    users.push(newUser);
                    localStorage.setItem('users', JSON.stringify(users));
                    alert('Account created! Please log in.');
                    signupForm.style.display = 'none';
                    loginForm.style.display = 'block';
                }
            });

            logoutBtn.addEventListener('click', () => {
                currentUser = null;
                localStorage.removeItem('currentUser');
                loginForm.style.display = 'block';
                signupForm.style.display = 'none';
                userInfo.style.display = 'none';
            });

            function loadMoodHistory() {
                moodHistory.innerHTML = '';
                moodData.forEach(entry => {
                    const div = document.createElement('div');
                    div.textContent = `${entry.date}: ${entry.mood}`;
                    moodHistory.appendChild(div);
                });
            }

            function loadAnalytics() {
                const moodCounts = { happy: 0, sad: 0, neutral: 0 };
                moodData.forEach(entry => moodCounts[entry.mood]++);
                const ctx = document.getElementById('moodChart').getContext('2d');
                new Chart(ctx, {
                    type: 'bar',
                    data: {
                        labels: ['Happy', 'Sad', 'Neutral'],
                        datasets: [{
                            label: 'Mood Counts',
                            data: [moodCounts.happy, moodCounts.sad, moodCounts.neutral],
                            backgroundColor: ['#4caf50', '#f44336', '#ff9800']
                        }]
                    }
                });
            }

            function loadSuggestions() {
                let suggestion = '';
                const moodCount = moodData.reduce((acc, entry) => {
                    acc[entry.mood] = (acc[entry.mood] || 0) + 1;
                    return acc;
                }, {});

                if (moodCount.happy > moodCount.sad) {
                    suggestion = 'Keep up the great vibes! Try a fun activity like going for a walk.';
                } else if (moodCount.sad > moodCount.happy) {
                    suggestion = 'Take some time to relax, maybe try some breathing exercises.';
                } else {
                    suggestion = 'Your mood is balanced. Consider journaling about your thoughts.';
                }

                suggestionText.textContent = suggestion;
            }

            function loadCalendar() {
                const currentDate = new Date();
                const currentMonth = currentDate.getMonth();
                const currentYear = currentDate.getFullYear();

                const firstDay = new Date(currentYear, currentMonth, 1);
                const lastDay = new Date(currentYear, currentMonth + 1, 0);

                const totalDays = lastDay.getDate();
                calendarView.innerHTML = '';

                for (let i = 1; i <= totalDays; i++) {
                    const dayDiv = document.createElement('div');
                    dayDiv.className = 'day';
                    dayDiv.textContent = i;
                    calendarView.appendChild(dayDiv);
                }
            }

            addMoodBtn.addEventListener('click', () => {
                if (customMoodInput.value) {
                    const newMood = { date: new Date().toLocaleDateString(), mood: customMoodInput.value };
                    moodData.push(newMood);
                    localStorage.setItem('moodData', JSON.stringify(moodData));
                    loadMoodHistory();
                    customMoodInput.value = '';
                    loadAnalytics();
                    loadSuggestions();
                }
            });

            saveEntryBtn.addEventListener('click', () => {
                if (journalEntry.value) {
                    alert('Journal entry saved!');
                    journalEntry.value = '';
                }
            });

            exportBtn.addEventListener('click', () => {
                const csv = moodData.map(entry => `${entry.date},${entry.mood}`).join('\n');
                const blob = new Blob([csv], { type: 'text/csv' });
                const link = document.createElement('a');
                link.href = URL.createObjectURL(blob);
                link.download = 'moodData.csv';
                link.click();
            });

            themeToggle.addEventListener('click', () => {
                document.body.classList.toggle('dark-mode');
            });

            signupLink.addEventListener('click', () => {
                signupForm.style.display = 'block';
                loginForm.style.display = 'none';
            });

            loginLink.addEventListener('click', () => {
                signupForm.style.display = 'none';
                loginForm.style.display = 'block';
            });
        });
    </script>
</body>
</html>

