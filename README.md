## UV Package Manager

- powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
- pip install uv
- uv init
- uv venv
- .venv\Scripts\activate
- uv add -r requirements.txt
- uv add ipykernel


## Conda

- conda create --name myenv python=3.6
- activate myenv
####
- conda remove --name myenv --all
- conda env remove --name myenv


## Git

- git clone https://github.com/AnanyaKumar2001/__________.git
- cd <YOUR_PROJECT_NAME>
- npm i
- npm run dev
####
- git pull origin main
####
- git stash
- git pull origin main
- git stash pop   # if you want to re-apply your local changes after pulling
####
- git init
- git add .
- git commit -m "commit"
- git remote add origin https://__________.git
- git push -u origin master
