from flask import Flask, render_template, request, redirect, url_for, session

app = Flask(__name__)
app.secret_key = "pixelpulse_secret_key"

# Dummy user data for demo
users = {"user1": "password1", "user2": "password2"}

@app.route("/")
def home():
    if "username" in session:
        return redirect(url_for("dashboard"))
    return render_template("index.html")

@app.route("/rules")
def rules():
    if "rules_accepted" in session:
        return redirect(url_for("home"))
    return render_template("rules.html")

@app.route("/accept_rules", methods=["POST"])
def accept_rules():
    session["rules_accepted"] = True
    return redirect(url_for("login"))

@app.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "POST":
        uname = request.form["username"]
        pwd = request.form["password"]
        if uname in users and users[uname] == pwd:
            session["username"] = uname
            return redirect(url_for("dashboard"))
        else:
            return render_template("login.html", error="Invalid credentials")
    return render_template("login.html")

@app.route("/logout")
def logout():
    session.pop("username", None)
    session.pop("rules_accepted", None)
    return redirect(url_for("home"))

@app.route("/dashboard")
def dashboard():
    if "username" not in session:
        return redirect(url_for("login"))
    return render_template("dashboard.html", username=session["username"])

@app.route("/search")
def search():
    # Demo search page - no real search logic
    query = request.args.get("q", "")
    videos = []
    if query:
        videos = [
            {"title": "Demo Video 1", "channel": "Channel A"},
            {"title": "Demo Video 2", "channel": "Channel B"},
        ]
    return render_template("search.html", videos=videos, query=query)

@app.route("/upload", methods=["GET", "POST"])
def upload():
    if "username" not in session:
        return redirect(url_for("login"))
    if request.method == "POST":
        # For demo, no file upload, just accept title and channel
        title = request.form["title"]
        channel = session["username"]
        # Here you would save video info to DB
        return render_template("upload.html", success="Video uploaded (demo)")
    return render_template("upload.html")

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=81, debug=True)


---

Step 2: Create folder templates and inside add these HTML files:

index.html

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pixel Pulse - Home</title>
</head>
<body>
<h1>Welcome to Pixel Pulse</h1>
<p><a href="/rules">Start Here (Read Rules)</a></p>
</body>
</html>


---

rules.html

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pixel Pulse - Rules</title>
</head>
<body>
<h2>Rules and Revenue Sharing</h2>
<ul>
<li>20% revenue goes to website</li>
<li>10% revenue goes to uploader</li>
<li>Video usage limits apply</li>
</ul>
<form action="/accept_rules" method="post">
<button type="submit">I Agree</button>
</form>
</body>
</html>


---

login.html

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pixel Pulse - Login</title>
</head>
<body>
<h2>Login</h2>
{% if error %}
<p style="color:red;">{{ error }}</p>
{% endif %}
<form method="post" action="/login">
<input type="text" name="username" placeholder="Username" required /><br />
<input type="password" name="password" placeholder="Password" required /><br />
<button type="submit">Login</button>
</form>
<p>Demo users: user1/password1, user2/password2</p>
</body>
</html>


---

dashboard.html

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pixel Pulse - Dashboard</title>
</head>
<body>
<h1>Hello, {{ username }}!</h1>
<p><a href="/search">Search Videos</a></p>
<p><a href="/upload">Upload Video</a></p>
<p><a href="/logout">Logout</a></p>
</body>
</html>


---

search.html

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pixel Pulse - Search</title>
</head>
<body>
<h2>Search Videos</h2>
<form action="/search" method="get">
<input type="text" name="q" value="{{ query }}" placeholder="Search videos" />
<button type="submit">Search</button>
</form>

{% if videos %}
<ul>
{% for video in videos %}
<li>{{ video.title }} by {{ video.channel }}</li>
{% endfor %}
</ul>
{% elif query %}
<p>No videos found for "{{ query }}"</p>
{% endif %}

<p><a href="/dashboard">Back to Dashboard</a></p>
</body>
</html>


---

upload.html

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Pixel Pulse - Upload</title>
</head>
<body>
<h2>Upload Video (Demo)</h2>
{% if success %}
<p style="color:green;">{{ success }}</p>
{% endif %}
<form method="post" action="/upload">
<input type="text" name="title" placeholder="Video Title" required /><br />
<button type="submit">Upload</button>
</form>
<p><a href="/dashboard">Back to Dashboard</a></p>
</body>
</html>
# Pixel-pulse-
