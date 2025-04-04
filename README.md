# wsl
### RECOMENDED see [raw version](wsl.txt)

# WSL & Docker Setup with Ollama and Open WebUI

This guide provides a comprehensive walkthrough for setting up Windows Subsystem for Linux (WSL), Docker, Ollama, Open WebUI, and useful development tools for a streamlined AI/ML and software development environment.

---

## 📌 Table of Contents

1. [WSL Installation and Configuration](#wsl-installation-and-configuration)
2. [Ubuntu Setup and User Configuration](#ubuntu-setup-and-user-configuration)
3. [Docker Engine Installation](#docker-engine-installation)
4. [Ollama Setup](#ollama-setup)
5. [Open WebUI Integration](#open-webui-integration)
6. [Post-Setup Docker Commands](#post-setup-docker-commands)
7. [GUI and Dev Tools Installation](#gui-and-dev-tools-installation)
8. [Python Version Management with Pyenv](#python-version-management-with-pyenv)
9. [Cleanup and Miscellaneous](#cleanup-and-miscellaneous)

---

## ✅ WSL Installation and Configuration

```bash
wsl --update
wsl --status
wsl --set-version Ubuntu 2
notepad $env:USERPROFILE\.wslconfig
```

Add to `.wslconfig`:

```
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

## 👤 Ubuntu Setup and User Configuration

```bash
adduser ubuntu
sudo -i
groups ubuntu
usermod -aG sudo ubuntu
visudo
```

Append to visudo (save with Ctrl+O, Ctrl+X):

```
ubuntu    ALL=(ALL:ALL) ALL
```

```bash
exit
nano ~/.bashrc
# Add at end:
cd ~
source ~/.bashrc
```

## 🐳 Docker Engine Installation

Follow the official [Docker Engine Install Guide](https://docs.docker.com/engine/install/ubuntu/)

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
sudo shutdown -r now
wsl --terminate Ubuntu
wsl --shutdown
```

## 🦙 Ollama Setup

[Docker Hub: Ollama](https://hub.docker.com/r/ollama/ollama)

```bash
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
docker exec -it ollama ollama run llama3.2:1b
```

## 🌐 Open WebUI Integration

[Quick Start Guide](https://docs.openwebui.com/getting-started/quick-start)

```bash
docker pull ghcr.io/open-webui/open-webui:main
docker run -d --network=host -v open-webui:/app/backend/data \
  -e OLLAMA_BASE_URL=http://127.0.0.1:11434 \
  --name open-webui ghcr.io/open-webui/open-webui:main
```

Visit: `http://localhost:8080/`

## 🛠️ Post-Setup Docker Commands

```bash
docker ps -a
docker ps
docker start ollama
docker start open-webui
docker exec -it ollama ollama list
docker exec -it ollama ollama
docker restart $(docker ps -aq)
docker inspect -f '{{.Name}} => {{.HostConfig.RestartPolicy.Name}}' $(docker ps -aq)
```

## 🧰 GUI and Dev Tools Installation

```bash
sudo apt install nautilus x11-apps gnome-text-editor gedit vlc -y
cd /tmp
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install --fix-missing ./google-chrome-stable_current_amd64.deb
```

Launch apps using:

```bash
gedit
nautilus
vlc
google-chrome
xcalc / xclock / xeyes
```

Install VS Code Extensions:

- [Remote WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

```bash
code .
sudo apt-get update
sudo apt-get install wget ca-certificates
```

## 🐍 Python Version Management with Pyenv

[Pyenv GitHub](https://github.com/pyenv/pyenv)

```bash
curl -fsSL https://pyenv.run | bash
```

Add to `~/.bashrc`, `~/.profile`, and `~/.bash_profile`:

```bash
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init - bash)"
eval "$(pyenv virtualenv-init -)"
```

```bash
exec "$SHELL"
pyenv install 3.12
pyenv global 3.12
pyenv versions
```

To set Python versions:

```bash
pyenv shell 3.11
pyenv local 3.11
pyenv global 3.11
```

## 🧹 Cleanup and Miscellaneous

- Remove all Docker data:

```bash
docker stop $(docker ps -q)
docker rm -f $(docker ps -aq)
docker volume rm $(docker volume ls -q)
docker rmi -f $(docker images -q)
docker system df
```

- Clean temp directory:

```bash
sudo rm -rf /tmp/*
```

- Check disk space:

```bash
df -h
```

- Access WSL files via Explorer:

```
\\wsl$
```

---

## ✅ Status

> 🟢 **Setup Completed Successfully**

You’re now ready to run LLMs with Ollama, interact using Open WebUI, and build/deploy software in WSL with full Docker support and GUI tools.

---

## ✉️ Contact

**Author:** Ankit Tayal\
 

<!-- 
c:; cd 'c:\Users\ANKIT\Documents\VScode\wsl'; git add -A; git commit -m "additional commit 2025"; git push;
 -->
