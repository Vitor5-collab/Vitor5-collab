<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chat Simples</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }

        #register, #chat {
            width: 300px;
            padding: 20px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }

        #chatWindow {
            margin-top: 20px;
        }

        #messages {
            height: 200px;
            border: 1px solid #ccc;
            margin-bottom: 10px;
            overflow-y: auto;
            padding: 10px;
        }

        #contactsList ul {
            list-style-type: none;
            padding: 0;
        }

        #contactsList li {
            cursor: pointer;
            padding: 5px;
            border: 1px solid #ccc;
            margin: 5px 0;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <div id="register">
        <h2>Cadastro</h2>
        <input type="text" id="nameInput" placeholder="Digite seu nome">
        <button onclick="registerUser()">Registrar</button>
    </div>

    <div id="chat" style="display: none;">
        <h2>Adicionar Contato</h2>
        <input type="text" id="contactCode" placeholder="Digite o código do contato">
        <button onclick="addContact()">Adicionar</button>
        
        <div id="contactsList">
            <h3>Contatos</h3>
            <ul id="contacts"></ul>
        </div>
        
        <div id="chatWindow" style="display: none;">
            <h2>Chat</h2>
            <div id="messages"></div>
            <input type="text" id="messageInput" placeholder="Digite sua mensagem">
            <button onclick="sendMessage()">Enviar</button>
        </div>
    </div>

    <script>
        let users = {};
        let currentUser = null;
        let currentChat = null;

        function generateCode() {
            return Math.floor(100000 + Math.random() * 900000).toString();
        }

        function registerUser() {
            const name = document.getElementById('nameInput').value;
            if (name) {
                const code = generateCode();
                users[code] = { name, messages: {} };
                currentUser = code;
                document.getElementById('register').style.display = 'none';
                document.getElementById('chat').style.display = 'block';
                alert(`Seu código é: ${code}`);
                showChatLink(code);
            } else {
                alert('Por favor, insira um nome.');
            }
        }

        function showChatLink(code) {
            const linkContainer = document.createElement('div');
            linkContainer.innerHTML = `Seu código é: ${code}<br><br>`;
            const chatLink = document.createElement('a');
            // Substitua "192.168.0.100" pelo endereço IP do seu servidor local
            chatLink.href = `http://192.168.0.100:8000/chat.html?user=${code}`;
            chatLink.textContent = 'Ir para página de conversas';
            linkContainer.appendChild(chatLink);
            document.body.appendChild(linkContainer);
        }

        function addContact() {
            const code = document.getElementById('contactCode').value;
            if (users[code]) {
                const contactList = document.getElementById('contacts');
                const li = document.createElement('li');
                li.textContent = `${users[code].name} (${code})`;
                li.onclick = () => openChatWindow(code);
                contactList.appendChild(li);
            } else {
                alert('Código inválido.');
            }
        }

        function openChatWindow(code) {
            currentChat = code;
            document.getElementById('chatWindow').style.display = 'block';
            loadMessages();
        }

        function sendMessage() {
            const message = document.getElementById('messageInput').value;
            if (message) {
                if (!users[currentChat].messages[currentUser]) {
                    users[currentChat].messages[currentUser] = [];
                }
                users[currentChat].messages[currentUser].push(message);
                document.getElementById('messageInput').value = '';
                loadMessages();
            }
        }

        function loadMessages() {
            const messageContainer = document.getElementById('messages');
            messageContainer.innerHTML = '';
            const messages = users[currentChat].messages[currentUser] || [];
            messages.forEach(msg => {
                const p = document.createElement('p');
                p.textContent = msg;
                messageContainer.appendChild(p);
            });
        }
    </script>
</body>
</html>
