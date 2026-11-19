# Tutorial: PySide6 no GitHub Codespaces

Guia prático e direto para configurar e rodar janelas gráficas no navegador usando o GitHub Codespaces.

---

## 1. Preparar os Arquivos do Repositório
Certifique-se de que o seu repositório tenha estes três arquivos organizados na raiz:
* `app1.py` — O arquivo com o código Python da sua janela.
* `.devcontainer/devcontainer.json` — A pasta oculta com a configuração do ambiente.
* `.gitignore` — Um arquivo de texto contendo apenas `self.pem` para não enviar arquivos temporários ao GitHub.

---

## 2. Instalar as Dependências (Executar apenas 1 vez)
Atualize a lista de programas do Linux e instale o servidor gráfico virtual, o transmissor web e todas as bibliotecas visuais exigidas pelo PySide6:

    sudo apt-get update && sudo apt-get install -y xvfb x11vnc fluxbox websockify novnc libgl1 libegl1 libdbus-1-3 libxkbcommon0 libxkbcommon-x11-0 libxcb-cursor0 libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-randr0 libxcb-render-util0 libxcb-shape0 libxcb-xfixes0 libxcb-xinerama0 libfontconfig1 libxrender1

Instale a biblioteca **PySide6** no Python do ambiente:

    python -m pip install PySide6

---

## 3. Ligar a Tela Virtual e Criar as Portas
Inicie o servidor gráfico invisível e o gerenciador de janelas. Esse comando prepara a tela virtual onde o programa vai rodar:

    Xvfb :99 -screen 0 1024x768x16 & export DISPLAY=:99 && fluxbox & x11vnc -display :99 -forever -shared -rfbport 5900 &

Agora, gere a chave de segurança local e abra a transmissão web na porta **6080** para conseguir acessar a tela pelo navegador:

    openssl req -new -x509 -days 365 -nodes -out self.pem -keyout self.pem -subj "/CN=localhost" && websockify --web=/usr/share/novnc/ --cert=self.pem 6080 localhost:5900 &

---

## 4. Abrir a Porta no Navegador
Com os comandos acima rodando, a porta **6080** será criada automaticamente. Siga estes passos para entrar na tela virtual:

1. No painel inferior do Codespaces, clique na aba **Portas** (ao lado de *Terminal*).
2. Localize a linha da porta **6080**, passe o mouse sobre o link em *Endereço Encaminhado* e clique no **ícone do globo** (Abrir no Navegador).
3. Se aparecer um aviso de segurança no navegador, clique em **Avançado** e depois em **Ir para o site**.
4. Na lista de arquivos que abrir, clique em **`vnc.html`** e aperte o botão **Conectar**.

---

## 5. Executar o Aplicativo
Com a tela virtual aberta e conectada na outra aba, volte ao terminal do Codespaces e inicie o seu código Python:

    export DISPLAY=:99 && python app1.py

Volte para a aba do navegador para visualizar e testar a janela do seu aplicativo.

---

## 6. Reiniciar o Ambiente (Caso a tela trave)
Se alguma porta ficar presa ou a tela parar de responder, rode este comando para encerrar todos os processos gráficos e recomeçar a partir do **Passo 3**:

    killall Xvfb x11vnc websockify fluxbox 2>/dev/null
