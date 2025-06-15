<!DOCTYPE html>
<html lang="nl">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>AimZzPK Community</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #000;
      color: white;
      margin: 0;
      padding: 0;
    }
    header {
      background-color: #d00000;
      padding: 1em;
      text-align: center;
    }
    nav {
      background-color: #111;
      padding: 1em;
      text-align: center;
    }
    nav a {
      color: white;
      margin: 0 1em;
      text-decoration: none;
      font-weight: bold;
    }
    main {
      padding: 2em;
      max-width: 600px;
      margin: auto;
    }
    section {
      margin-bottom: 2em;
    }
    input,
    textarea,
    button {
      display: block;
      width: 100%;
      margin-top: 0.5em;
      padding: 0.5em;
      border: none;
      border-radius: 4px;
    }
    input,
    textarea {
      background-color: #111;
      color: white;
    }
    button {
      background-color: #00b300;
      color: white;
      font-weight: bold;
      cursor: pointer;
    }
    .hidden {
      display: none;
    }
    #comment-list {
      border: 1px solid #333;
      padding: 1em;
      background-color: #111;
      max-height: 300px;
      overflow-y: auto;
    }
    .comment {
      border-bottom: 1px solid #444;
      padding: 0.5em 0;
    }
  </style>

  <!-- Firebase compat libraries -->
  <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-auth-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-database-compat.js"></script>
</head>
<body>
  <header>
    <h1>AimZzPK Community</h1>
  </header>
  <nav>
    <a href="#">Home</a>
    <a href="#comments">Comments</a>
    <a href="#login">Login</a>
  </nav>
  <main>
    <section id="login">
      <h2>Login of Registreer</h2>
      <input type="email" id="email" placeholder="E-mail" />
      <input type="password" id="password" placeholder="Wachtwoord" />
      <button onclick="login()">Inloggen</button>
      <button onclick="register()">Registreren</button>
      <button onclick="logout()" class="hidden" id="logoutBtn">Uitloggen</button>
      <p id="userStatus"></p>
    </section>

    <section id="comments" class="hidden">
      <h2>Laat een reactie achter</h2>
      <textarea id="commentInput" rows="3" placeholder="Typ je bericht..."></textarea>
      <button onclick="postComment()">Plaats reactie</button>
      <div id="comment-list"></div>
    </section>
  </main>
  <footer>
    &copy; 2025 AimZzPK Community - Powered by Fans ❤️
  </footer>

  <script>
    // Jouw Firebase-config
    const firebaseConfig = {
      apiKey: "AIzaSyByb0Ud1_PuWj_QF1XeTMErxC-_z59EVzw",
      authDomain: "aimzzpkcommunity.firebaseapp.com",
      projectId: "aimzzpkcommunity",
      storageBucket: "aimzzpkcommunity.firebasestorage.app",
      messagingSenderId: "507872075807",
      appId: "1:507872075807:web:f4545cbcbae65812eb352a",
      measurementId: "G-CZ0E88XFB2",
      databaseURL: "https://aimzzpkcommunity-default-rtdb.firebaseio.com/"
    };

    // Firebase initialiseren
    firebase.initializeApp(firebaseConfig);
    const auth = firebase.auth();
    const db = firebase.database();

    // UI elementen
    const commentsSection = document.getElementById("comments");
    const logoutBtn = document.getElementById("logoutBtn");
    const userStatus = document.getElementById("userStatus");

    auth.onAuthStateChanged((user) => {
      if (user) {
        commentsSection.classList.remove("hidden");
        logoutBtn.classList.remove("hidden");
        userStatus.textContent = `Ingelogd als: ${user.email}`;
        loadComments();
      } else {
        commentsSection.classList.add("hidden");
        logoutBtn.classList.add("hidden");
        userStatus.textContent = "Niet ingelogd";
        clearComments();
      }
    });

    function login() {
      const email = document.getElementById("email").value;
      const password = document.getElementById("password").value;
      auth.signInWithEmailAndPassword(email, password).catch((error) => {
        alert("Fout bij inloggen: " + error.message);
      });
    }

    function register() {
      const email = document.getElementById("email").value;
      const password = document.getElementById("password").value;
      auth.createUserWithEmailAndPassword(email, password).catch((error) => {
        alert("Fout bij registreren: " + error.message);
      });
    }

    function logout() {
      auth.signOut();
    }

    function postComment() {
      const commentInput = document.getElementById("commentInput");
      const comment = commentInput.value.trim();
      const user = auth.currentUser;
      if (!user) {
        alert("Je moet ingelogd zijn om te reageren.");
        return;
      }
      if (comment.length === 0) {
        alert("Typ eerst een bericht.");
        return;
      }
      db.ref("comments").push({
        text: comment,
        user: user.email,
        timestamp: Date.now(),
      });
      commentInput.value = "";
    }

    function loadComments() {
      const commentList = document.getElementById("comment-list");
      db.ref("comments").on("value", (snapshot) => {
        commentList.innerHTML = "";
        snapshot.forEach((child) => {
          const comment = child.val();
          const div = document.createElement("div");
          div.className = "comment";
          const time = new Date(comment.timestamp).toLocaleString();
          div.textContent = `[${time}] ${comment.user}: ${comment.text}`;
          commentList.appendChild(div);
        });
      });
    }

    function clearComments() {
      document.getElementById("comment-list").innerHTML = "";
    }
  </script>
</body>
</html>
