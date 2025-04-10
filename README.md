# wsl
## IMPORTANT*** see [raw version](wsl.txt)
### RECOMENDED see [raw version](wsl.txt)

#  WSL + Docker + Ollama + Open WebUI Setup

---

## 🖥️ Environment Setup for AI Model Deployment with WSL2, Docker, Ollama, and Open WebUI

This guide documents a **complete and battle-tested setup** on Windows using **WSL2 (Ubuntu)**, **Docker**, **Ollama**, and **Open WebUI** for seamless AI and LLM usage.

---

## 🔧 WSL2 Setup (Windows Subsystem for Linux)

```bash
wsl --update
wsl --status
# Ensure version 2 is set as default
wsl --set-version Ubuntu 2

notepad $env:USERPROFILE\.wslconfig
```

**Add the following configuration:**
```ini
[wsl2]
memory=8GB
processors=6
swap=4GB
localhostForwarding=true
```

```bash
wsl --shutdown
wsl -l -v
wsl --list --online
wsl --install Ubuntu
```

**Check Swap**
```bash
free -h
```

If not prompted to create a user:
```bash
adduser ubuntu
```

## 👤 Add sudo privileges
```bash
sudo -i
groups ubuntu
usermod -aG sudo ubuntu
visudo
```
**At the end, add:**
```bash
ubuntu    ALL=(ALL:ALL) ALL
```

Exit editor:
- Ctrl + O
- Enter
- Ctrl + X

```bash
echo 'cd ~' >> ~/.bashrc
exit
echo 'cd ~' >> ~/.bashrc
```

### Reload your shell
```bash
source ~/.bashrc
```

---

## 🐳 Docker Installation (Manual, official method)

👉 **Do NOT use `docker.io` or bundled packages:**
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

### 🔐 Add Docker’s official GPG key and setup repository
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo \"${UBUNTU_CODENAME:-$VERSION_CODENAME}\") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

### 📦 Install Docker
### [Official Site](https://docs.docker.com/engine/install/ubuntu/)
> *********** USE UPDATED COMMANDS FROM ABOVE WEBSITE
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 👥 Add Docker user permissions
```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

Reboot WSL:
```bash
sudo shutdown -r now
wsl --terminate Ubuntu
wsl --shutdown
wsl
```

---

## 🧠 Ollama LLM Deployment

📦 Docker Hub: https://hub.docker.com/r/ollama/ollama
```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
docker exec -it ollama ollama run llama3.2:1b
```

---

## 🌐 Open WebUI for Ollama

Docs: https://docs.openwebui.com/getting-started/quick-start
```bash
docker pull ghcr.io/open-webui/open-webui:main
docker run -d --network=host -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 --name open-webui ghcr.io/open-webui/open-webui:main
```

Access WebUI:
```
http://localhost:8080/
```

---

## 🧰 Useful Docker Commands
```bash
docker ps -a
docker ps
docker start ollama
docker start open-webui
docker exec -it ollama ollama list
docker exec -it ollama ollama
docker restart $(docker ps -aq)
```

### Real-time Resource Usage (RAM, CPU) OF CONTAINERS
```bash
docker stats
```

### 🔁 Check autostart status
```bash
docker inspect -f '{{.Name}} => {{.HostConfig.RestartPolicy.Name}}' $(docker ps -aq)
```

---

## 📁 GUI Tools (Optional but useful)
```bash
sudo apt install nautilus -y
sudo apt install x11-apps -y
```

### 🧭 Google Chrome
```bash
cd /tmp
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install --fix-missing ./google-chrome-stable_current_amd64.deb
google-chrome
```

---

## 🧠 VSCode Remote Development

Install: https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack

```bash
sudo apt-get update
sudo apt-get install wget ca-certificates
code .
```

Install Python extension: https://marketplace.visualstudio.com/items?itemName=ms-python.python

---

## ☢️ Nuke Docker (Full Reset)
```bash
docker stop $(docker ps -q)
docker rm -f $(docker ps -aq)
docker volume rm $(docker volume ls -q)
docker rmi -f $(docker images -q)
docker system df

sudo shutdown -r now
wsl --terminate Ubuntu
wsl --shutdown
```

---

## 📂 Access WSL Files in Explorer
```
\\wsl$
```

---

## 🎁 Extra Tools (Recommended)
```bash
sudo apt update
sudo apt install gnome-text-editor -y
sudo apt install gedit -y
sudo apt install nautilus -y
sudo apt install vlc -y
sudo apt install x11-apps -y
```

Reinstall Chrome if broken:
```bash
cd /tmp
sudo wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
sudo apt install --fix-broken -y
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

Clear temp:
```bash
sudo rm -rf /tmp/*
```

Disk size check:
```bash
df -h
```

---

## 🐍 Pyenv Setup (Python Version Management)

GitHub: https://github.com/pyenv/pyenv
```bash
curl -fsSL https://pyenv.run | bash
```

### 🔁 Add to bashrc
Run:
```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.profile
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.profile
echo 'eval "$(pyenv init - bash)"' >> ~/.profile
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo '[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(pyenv init - bash)"' >> ~/.bash_profile
```

### Restart your shell
```bash
exec "$SHELL"
```

Check versions:
```bash
python --version
python3 --version
pip
sudo apt install python3-pip
pyenv versions
```

Install and set version:
```bash
pyenv install 3.12
pyenv global 3.12
# For local or shell sessions
pyenv shell 3.11
pyenv local 3.11
```

---

✅ **DONE! Your WSL setup is now capable of AI development and model serving.**


## ✅ Status

> 🟢 **Setup Completed Successfully**

You’re now ready to run LLMs with Ollama, interact using Open WebUI, and build/deploy software in WSL with full Docker support and GUI tools.

---

## ✉️ Contact

**Author:** Ankit Tayal
 

<!-- 
c:; cd 'c:\Users\ANKIT\Documents\VScode\wsl'; git add -A; git commit -m "additional commit 2025"; git push;
 -->
