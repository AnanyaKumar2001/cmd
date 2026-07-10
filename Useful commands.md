# 🛠️ Useful Project Debugging Commands

This document contains useful commands for inspecting, debugging, and managing Python, Flask, Git, and other development projects on Windows.

> **Note:** Most commands below are intended for **Windows Command Prompt (CMD)**. Commands specifically intended for **Git Bash** are marked separately.

<a id="table-of-contents"></a>
## 📑 Table of Contents

1. [🔍 Search for Text Inside Project Files](#search-text)
2. [🌳 View the Project Structure](#project-structure)
3. [📂 Navigate Through the Project](#navigation)
4. [📄 View File Contents](#file-contents)
5. [🐍 Python Environment Commands](#python-environment)
6. [▶️ Run and Debug Python Files](#python-debugging)
7. [🌐 Flask Debugging Commands](#flask-debugging)
8. [🛠️ Check for Common Python Project Problems](#python-problems)
9. [📜 Git Status and History Commands](#git-history)
10. [⬆️ Git Add, Commit, and Push Commands](#git-workflow)
11. [🕒 Find Recently Changed Files](#changed-files)
12. [🔌 Check Network Ports](#network-ports)
13. [🧹 Clear Python Cache Files](#python-cache)
14. [🩺 Useful Combined Debugging Workflow](#debugging-workflow)
15. [⚡ Quick Command Reference](#quick-reference)
16. [💡 Tips](#tips)

---

<a id="search-text"></a>
## 1. 🔍 Search for Text Inside Project Files

### Search inside all Python files

```cmd
findstr /S /N /I "ananya" *.py
```

**Explanation:**

- `/S` — Searches the current folder and all subfolders.
- `/N` — Displays the line number where the text is found.
- `/I` — Makes the search case-insensitive.
- `"ananya"` — Text to search for.
- `*.py` — Searches only Python files.

### Search inside HTML files

```cmd
findstr /S /N /I "dashboard.home" *.html
```

Useful for finding Flask endpoints used in templates.

### Search inside Python and HTML files

```cmd
findstr /S /N /I "dashboard.home" *.py *.html
```

Useful when debugging Flask `BuildError` issues.

### Search inside JavaScript files

```cmd
findstr /S /N /I "fetch(" *.js
```

Useful for locating API calls.

### Search inside multiple file types

```cmd
findstr /S /N /I "localhost" *.py *.html *.js *.css
```

### Search for an IP address

```cmd
findstr /S /N /I "127.0.0.1" *.py *.html *.js
```

Useful when replacing hardcoded server addresses.

### Search for TODO comments

```cmd
findstr /S /N /I "TODO" *.py
```

### Search for print statements

```cmd
findstr /S /N /I "print(" *.py
```

### Search for Flask routes

```cmd
findstr /S /N /I "@*.route" *.py
```

### Search for Blueprint definitions

```cmd
findstr /S /N /I "Blueprint(" *.py
```

### Search for `url_for` usage

```cmd
findstr /S /N /I "url_for" *.py *.html
```

### Search for imports

```cmd
findstr /S /N /I "from routes" *.py
```

### Search all files for specific text

```cmd
findstr /S /N /I "search_text" *.*
```

Use carefully because this may also search unnecessary files.

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="project-structure"></a>
## 2. 🌳 View the Project Structure

### Display the complete project tree

```cmd
tree /F /A
```

- `/F` — Displays files in addition to folders.
- `/A` — Uses normal ASCII characters.

### Display project structure without `.venv`

```cmd
tree /F /A | findstr /V /I ".venv"
```

- `/V` — Displays lines that do **not** contain the specified text.
- `/I` — Makes matching case-insensitive.

### Exclude multiple unwanted folders

```cmd
tree /F /A | findstr /V /I ".venv __pycache__ node_modules"
```

### Display only files and folders in the current directory

```cmd
dir /B
```

Use this when the full tree output is too long.

### Display files recursively

```cmd
dir /S /B
```

### Display only Python files recursively

```cmd
dir /S /B *.py
```

### Display only folders

```cmd
dir /AD /B
```

### Display only files

```cmd
dir /A-D /B
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="navigation"></a>
## 3. 📂 Navigate Through the Project

### Show the current directory

```cmd
cd
```

### Change directory

```cmd
cd E:\Research\DFIR\dfir-framework2
```

### Move one folder backward

```cmd
cd ..
```

### Move two folders backward

```cmd
cd ..\..
```

### Open the current folder in File Explorer

```cmd
explorer .
```

### Open the current project in VS Code

```cmd
code .
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="file-contents"></a>
## 4. 📄 View File Contents

### Display a file

```cmd
type app.py
```

### Display another file

```cmd
type routes\dashboard.py
```

### Display a file one screen at a time

```cmd
type app.py | more
```

### Display only lines containing specific text

```cmd
type app.py | findstr /I "register_blueprint"
```

### Display the complete file with line numbers

```cmd
findstr /N "^" app.py
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="python-environment"></a>
## 5. 🐍 Python Environment Commands

### Check Python version

```cmd
python --version
```

### Check Python executable location

```cmd
where python
```

Useful when multiple Python installations exist.

### Create a virtual environment

```cmd
python -m venv .venv
```

### Activate the virtual environment in CMD

```cmd
.venv\Scripts\activate
```

### Activate the virtual environment in PowerShell

```powershell
.venv\Scripts\Activate.ps1
```

### Deactivate the virtual environment

```cmd
deactivate
```

### Check installed packages

```cmd
pip list
```

### Check a specific package

```cmd
pip show flask
```

### Install project dependencies

```cmd
pip install -r requirements.txt
```

### Generate `requirements.txt`

```cmd
pip freeze > requirements.txt
```

### Check outdated packages

```cmd
pip list --outdated
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="python-debugging"></a>
## 6. ▶️ Run and Debug Python Files

### Run the application

```cmd
python app.py
```

### Check Python syntax without running the application

```cmd
python -m py_compile app.py
```

### Check syntax of another file

```cmd
python -m py_compile routes\dashboard.py
```

### Compile all Python files

```cmd
python -m compileall .
```

Useful for detecting syntax errors across the entire project.

### Test whether a module can be imported

```cmd
python -c "import app"
```

### Test a specific import

```cmd
python -c "from routes.dashboard import dashboard_bp"
```

Useful for debugging import errors.

### Print the Python module search path

```cmd
python -c "import sys; print('\n'.join(sys.path))"
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="flask-debugging"></a>
## 7. 🌐 Flask Debugging Commands

### Run a Flask application

```cmd
flask --app app run
```

### Run Flask in debug mode

```cmd
flask --app app run --debug
```

### Run Flask on all network interfaces

```cmd
flask --app app run --host=0.0.0.0
```

### Run Flask on a specific port

```cmd
flask --app app run --port=5001
```

### Display all registered Flask routes

```cmd
flask --app app routes
```

This is extremely useful when debugging endpoint errors.

### Filter Flask routes

```cmd
flask --app app routes | findstr /I "dashboard"
```

### Search for Blueprint registration

```cmd
findstr /S /N /I "register_blueprint" *.py
```

### Search for a specific endpoint

```cmd
findstr /S /N /I "dashboard.home" *.py *.html
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="python-problems"></a>
## 8. 🛠️ Check for Common Python Project Problems

### Find function definitions

```cmd
findstr /S /N /R /C:"def .*(" *.py
```

### Find all class definitions

```cmd
findstr /S /N /R /C:"class .*" *.py
```

### Find environment variable usage

```cmd
findstr /S /N /I "os.environ getenv" *.py
```

### Find database references

```cmd
findstr /S /N /I "db." *.py
```

### Find SQL queries

```cmd
findstr /S /N /I "SELECT INSERT UPDATE DELETE" *.py
```

### Find exception handling blocks

```cmd
findstr /S /N /I "except" *.py
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="git-history"></a>
## 9. 📜 Git Status and History Commands

### Check repository status

```bash
git status
```

### View changed files

```bash
git diff
```

### View staged changes

```bash
git diff --staged
```

### View commit history

```bash
git log --oneline
```

### View detailed commit history

```bash
git log
```

### View the latest 10 commits

```bash
git log --oneline -10
```

### View commit history as a graph

```bash
git log --oneline --graph --all --decorate
```

### Check the configured Git username

```bash
git config user.name
```

### Check the configured Git email

```bash
git config user.email
```

### Set Git username globally

```bash
git config --global user.name "Your Name"
```

### Set Git email globally

```bash
git config --global user.email "your-email@example.com"
```

### View all Git configuration

```bash
git config --list
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="git-workflow"></a>
## 10. ⬆️ Git Add, Commit, and Push Commands

### Add all changes

```bash
git add .
```

### Add a specific file

```bash
git add app.py
```

### Commit changes

```bash
git commit -m "Fix dashboard routing issue"
```

### Push changes

```bash
git push
```

### Complete common workflow

```bash
git status
git add .
git commit -m "Update project"
git push
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="changed-files"></a>
## 11. 🕒 Find Recently Changed Files

### Using Git

```bash
git status --short
```

Status codes:

- `M` — Modified
- `A` — Added
- `D` — Deleted
- `??` — Untracked

### View files changed in the latest commit

```bash
git show --name-only --oneline HEAD
```

### View files changed between two commits

```bash
git diff --name-only COMMIT1 COMMIT2
```

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="network-ports"></a>
## 12. 🔌 Check Network Ports

### Check whether port 5000 is in use

```cmd
netstat -ano | findstr :5000
```

The last number in the output is the Process ID (PID).

### Find the program using a PID

```cmd
tasklist | findstr 12345
```

### Stop a process using its PID

```cmd
taskkill /PID 12345 /F
```

Use this when Flask reports that the port is already in use.

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="python-cache"></a>
## 13. 🧹 Clear Python Cache Files

### Delete all `__pycache__` folders

```cmd
for /d /r . %d in (__pycache__) do @if exist "%d" rd /s /q "%d"
```

### Delete all `.pyc` files

```cmd
del /S /Q *.pyc
```

Useful when old cached Python files cause unexpected behavior.

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="debugging-workflow"></a>
## 14. 🩺 Useful Combined Debugging Workflow

When a Flask application produces an error, use the following workflow:

```cmd
git status
dir /B
tree /F /A | findstr /V /I ".venv __pycache__"
findstr /S /N /I "error_text" *.py *.html
flask --app app routes
python -m compileall .
python app.py
```

Recommended order:

1. Check changed files using `git status`.
2. Inspect the project structure.
3. Search for the exact error text, endpoint, function, or variable.
4. Check registered Flask routes.
5. Check Python syntax.
6. Run the application again.

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="quick-reference"></a>
## 15. ⚡ Quick Command Reference

| Task | Command |
|---|---|
| Search Python files | `findstr /S /N /I "text" *.py` |
| Search Python and HTML | `findstr /S /N /I "text" *.py *.html` |
| View project tree | `tree /F /A` |
| Hide `.venv` from tree | `tree /F /A \| findstr /V /I ".venv"` |
| List current folder | `dir /B` |
| View a file | `type app.py` |
| Open VS Code | `code .` |
| Check Python version | `python --version` |
| Check Python location | `where python` |
| Check syntax | `python -m compileall .` |
| View Flask routes | `flask --app app routes` |
| Check Git status | `git status` |
| View commit history | `git log --oneline` |
| Check port 5000 | `netstat -ano \| findstr :5000` |

[⬆️ Back to Table of Contents](#table-of-contents)

---

<a id="tips"></a>
## 💡 Tips

- Run commands from the **project root directory**.
- Replace example text such as `"ananya"` or `"dashboard.home"` with the actual text you want to find.
- Use `findstr` before manually opening many files.
- Use `flask --app app routes` whenever Flask reports a `BuildError`.
- Use `git status` before making or committing changes.
- Avoid including `.venv`, `node_modules`, and `__pycache__` when sharing project structures.
- Never commit passwords, API keys, secret keys, or `.env` files to Git.

[⬆️ Back to Table of Contents](#table-of-contents)
