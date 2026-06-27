from flask import Flask, render_template, request
import os
import PyPDF2

app = Flask(__name__)

UPLOAD_FOLDER = "uploads"
app.config["UPLOAD_FOLDER"] = UPLOAD_FOLDER

if not os.path.exists(UPLOAD_FOLDER):
    os.makedirs(UPLOAD_FOLDER)

SKILLS = ["python", "java", "html", "css", "javascript", "flask", "sql", "machine learning"]

def extract_text(file_path):
    text = ""
    with open(file_path, "rb") as f:
        reader = PyPDF2.PdfReader(f)
        for page in reader.pages:
            text += page.extract_text() or ""
    return text.lower()

def find_skills(text):
    found = []
    for skill in SKILLS:
        if skill in text:
            found.append(skill)
    return found

@app.route("/", methods=["GET", "POST"])
def index():
    skills = []
    text = ""

    if request.method == "POST":
        file = request.files["resume"]
        if file:
            path = os.path.join(app.config["UPLOAD_FOLDER"], file.filename)
            file.save(path)

            text = extract_text(path)
            skills = find_skills(text)

    return render_template("index.html", skills=skills)

if __name__ == "__main__":
    app.run(debug=True)
    <!DOCTYPE html>
<html>
<head>
    <title>Resume Parser</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>
    <div class="container">
        <h1>Resume Parser 📄</h1>

        <form method="POST" enctype="multipart/form-data">
            <input type="file" name="resume" required>
            <button type="submit">Upload</button>
        </form>

        {% if skills %}
        <h2>Detected Skills:</h2>
        <ul>
            {% for skill in skills %}
                <li>{{ skill }}</li>
            {% endfor %}
        </ul>
        {% endif %}
    </div>
</body>
</html>
body {
    font-family: Arial;
    background: #f2f2f2;
    text-align: center;
}

.container {
    margin-top: 50px;
    background: white;
    padding: 20px;
    width: 50%;
    margin-left: auto;
    margin-right: auto;
    border-radius: 10px;
    box-shadow: 0px 0px 10px gray;
}

button {
    padding: 10px;
    background: black;
    color: white;
    border: none;
    cursor: pointer;
    flask
PyPDF2
}

