<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Telegram Chat</title>
    <style>
        :root {
            --bg: #f8f9fa;
            --surface: #ffffff;
            --surface-alt: #f1f3f5;
            --text-primary: #212529;
            --text-secondary: #6c757d;
            --border: #dee2e6;
            --accent: #212529;
            --accent-hover: #495057;
            --message-sent: #212529;
            --message-received: #f1f3f5;
            --danger: #c92a2a;
            --success: #2b8a3e;
            --radius: 12px;
            --radius-sm: 8px;
            --radius-lg: 20px;
            --shadow-sm: 0 1px 3px rgba(0,0,0,0.06);
            --shadow-md: 0 4px 20px rgba(0,0,0,0.08);
            --font: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Inter', Roboto, sans-serif;
        }

        @media (prefers-color-scheme: dark) {
            :root {
                --bg: #1a1a1a;
                --surface: #242424;
                --surface-alt: #2d2d2d;
                --text-primary: #e9ecef;
                --text-secondary: #adb5bd;
                --border: #3d3d3d;
                --accent: #e9ecef;
                --accent-hover: #ced4da;
                --message-sent: #e9ecef;
                --message-received: #2d2d2d;
                --danger: #ff6b6b;
                --success: #69db7c;
                --shadow-sm: 0 1px 3px rgba(0,0,0,0.2);
                --shadow-md: 0 4px 20px rgba(0,0,0,0.3);
            }
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: var(--font);
            background-color: var(--bg);
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            padding: 20px;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }

        .chat-container {
            width: 100%;
            max-width: 480px;
            height: 90vh;
            max-height: 780px;
            background-color: var(--surface);
            border-radius: var(--radius-lg);
            display: flex;
            flex-direction: column;
            box-shadow: var(--shadow-md);
            overflow: hidden;
            border: 1px solid var(--border);
        }

        .chat-header {
            padding: 20px 24px 16px;
            border-bottom: 1px solid var(--border);
            background-color: var(--surface);
        }

        .chat-header h2 {
            font-size: 15px;
            font-weight: 600;
            color: var(--text-primary);
            letter-spacing: -0.01em;
        }

        .status-row {
            display: flex;
            align-items: center;
            gap: 7px;
            margin-top: 6px;
        }

        .status-dot {
            width: 6px;
            height: 6px;
            border-radius: 50%;
            background-color: #adb5bd;
            flex-shrink: 0;
            transition: background-color 0.3s ease;
        }

        .status-dot.online {
            background-color: var(--success);
        }

        .status-dot.connecting {
            background-color: #f59f00;
            animation: pulse 1.2s ease-in-out infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.4; }
        }

        .status-text {
            font-size: 12px;
            color: var(--text-secondary);
            letter-spacing: -0.01em;
        }

        .login-panel {
            padding: 20px 24px;
            border-bottom: 1px solid var(--border);
            background-color: var(--surface);
            display: flex;
            flex-direction: column;
            gap: 10px;
            transition: all 0.25s ease;
        }

        .login-panel.hidden {
            display: none;
        }

        .login-panel label {
            font-size: 11px;
            font-weight: 500;
            color: var(--text-secondary);
            text-transform: uppercase;
            letter-spacing: 0.04em;
            margin-bottom: -6px;
        }

        .login-panel input {
            padding: 10px 14px;
            border-radius: var(--radius-sm);
            border: 1px solid var(--border);
            background-color: var(--surface-alt);
            color: var(--text-primary);
            font-size: 13px;
            font-family: 'SF Mono', 'Fira Code', 'Cascadia Code', monospace;
            outline: none;
            transition: border-color 0.2s ease, box-shadow 0.2s ease;
        }

        .login-panel input:focus {
            border-color: var(--accent);
            box-shadow: 0 0 0 3px rgba(0,0,0,0.05);
        }

        .login-panel button {
            padding: 10px 16px;
            border-radius: var(--radius-sm);
            border: 1px solid var(--accent);
            background-color: var(--accent);
            color: var(--bg);
            font-weight: 500;
            font-size: 13px;
            cursor: pointer;
            transition: all 0.2s ease;
            letter-spacing: -0.01em;
            font-family: var(--font);
        }

        .login-panel button:hover {
            background-color: var(--accent-hover);
            border-color: var(--accent-hover);
        }

        .error-message {
            color: var(--danger);
            font-size: 12px;
            display: none;
            letter-spacing: -0.01em;
        }

        .messages-area {
            flex: 1;
            overflow-y: auto;
            padding: 20px 24px;
            display: flex;
            flex-direction: column;
            gap: 8px;
            scroll-behavior: smooth;
            background-color: var(--surface);
        }

        .messages-area::-webkit-scrollbar {
            width: 4px;
        }

        .messages-area::-webkit-scrollbar-track {
            background: transparent;
        }

        .messages-area::-webkit-scrollbar-thumb {
            background-color: var(--border);
            border-radius: 2px;
        }

        .message {
            max-width: 78%;
            padding: 10px 14px;
            border-radius: var(--radius);
            word-wrap: break-word;
            animation: fadeIn 0.25s ease-out;
            font-size: 13.5px;
            line-height: 1.5;
            letter-spacing: -0.01em;
            position: relative;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: translateY(6px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .message.received {
            align-self: flex-start;
            background-color: var(--message-received);
            color: var(--text-primary);
            border-bottom-left-radius: 4px;
            border: 1px solid var(--border);
        }

        .message.sent {
            align-self: flex-end;
            background-color: var(--message-sent);
            color: var(--bg);
            border-bottom-right-radius: 4px;
        }

        .message .time {
            font-size: 10px;
            opacity: 0.55;
            margin-top: 4px;
            display: block;
            text-align: right;
            letter-spacing: 0.02em;
        }

        .message.sent .time {
            opacity: 0.6;
        }

        .typing-indicator {
            display: none;
            align-self: flex-start;
            padding: 10px 14px;
            border-radius: var(--radius);
            border-bottom-left-radius: 4px;
            background-color: var(--message-received);
            border: 1px solid var(--border);
        }

        .typing-indicator span {
            display: inline-block;
            width: 5px;
            height: 5px;
            border-radius: 50%;
            background-color: var(--text-secondary);
            margin: 0 2px;
            animation: typingBounce 1.4s infinite ease-in-out;
        }

        .typing-indicator span:nth-child(2) { animation-delay: 0.2s; }
        .typing-indicator span:nth-child(3) { animation-delay: 0.4s; }

        @keyframes typingBounce {
            0%, 60%, 100% { transform: translateY(0); opacity: 0.3; }
            30% { transform: translateY(-6px); opacity: 0.8; }
        }

        .input-area {
            display: flex;
            padding: 14px 20px 18px;
            gap: 10px;
            border-top: 1px solid var(--border);
            background-color: var(--surface);
        }

        .input-area input {
            flex: 1;
            padding: 10px 16px;
            border-radius: 22px;
            border: 1px solid var(--border);
            background-color: var(--surface-alt);
            color: var(--text-primary);
            font-size: 13.5px;
            font-family: var(--font);
            outline: none;
            transition: border-color 0.2s ease, box-shadow 0.2s ease;
            letter-spacing: -0.01em;
        }

        .input-area input:focus {
            border-color: var(--accent);
            box-shadow: 0 0 0 3px rgba(0,0,0,0.04);
        }

        .input-area input:disabled {
            opacity: 0.45;
            cursor: not-allowed;
        }

        .input-area button {
            padding: 10px 18px;
            border-radius: 22px;
            border: 1px solid var(--accent);
            background-color: var(--accent);
            color: var(--bg);
            font-weight: 500;
            font-size: 13px;
            cursor: pointer;
            transition: all 0.2s ease;
            font-family: var(--font);
            letter-spacing: -0.01em;
            white-space: nowrap;
        }

        .input-area button:hover:not(:disabled) {
            background-color: var(--accent-hover);
            border-color: var(--accent-hover);
        }

        .input-area button:disabled {
            opacity: 0.45;
            cursor: not-allowed;
        }

        .empty-state {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100%;
            color: var(--text-secondary);
            font-size: 13px;
            text-align: center;
            gap: 8px;
            letter-spacing: -0.01em;
        }

        .empty-state .divider {
            width: 24px;
            height: 1px;
            background-color: var(--border);
            margin: 4px 0;
        }
    </style>
</head>
<body>

    <div class="chat-container">
        <div class="chat-header">
            <h2>Telegram</h2>
            <div class="status-row">
                <span class="status-dot" id="statusDot"></span>
                <span class="status-text" id="statusText">Not connected</span>
            </div>
        </div>

        <div class="login-panel" id="loginPanel">
            <label for="botToken">Bot token</label>
            <input type="text" id="botToken" placeholder="1234567890:ABCdefGHIjklMNOpqrsTUVwxyz">
            <label for="chatId">Chat ID</label>
            <input type="text" id="chatId" placeholder="123456789">
            <button onclick="connectBot()">Connect</button>
            <div class="error-message" id="loginError"></div>
        </div>

        <div class="messages-area" id="messagesArea">
            <div class="empty-state">
                <span>No messages yet</span>
                <span class="divider"></span>
                <span>Enter credentials to start</span>
            </div>
        </div>

        <div class="typing-indicator" id="typingIndicator">
            <span></span><span></span><span></span>
        </div>

        <div class="input-area">
            <input type="text" id="messageInput" placeholder="Type a message..." disabled>
            <button id="sendButton" onclick="sendMessage()" disabled>Send</button>
        </div>
    </div>

    <script>
        let botToken = '';
        let chatId = '';
        let lastUpdateId = 0;
        let pollingInterval = null;
        let isFirstMessage = true;

        const loginPanel = document.getElementById('loginPanel');
        const botTokenInput = document.getElementById('botToken');
        const chatIdInput = document.getElementById('chatId');
        const loginError = document.getElementById('loginError');
        const messagesArea = document.getElementById('messagesArea');
        const messageInput = document.getElementById('messageInput');
        const sendButton = document.getElementById('sendButton');
        const statusDot = document.getElementById('statusDot');
        const statusText = document.getElementById('statusText');
        const typingIndicator = document.getElementById('typingIndicator');

        async function connectBot() {
            const token = botTokenInput.value.trim();
            const cid = chatIdInput.value.trim();

            if (!token || !cid) {
                showError('Both fields are required');
                return;
            }

            if (!token.includes(':')) {
                showError('Invalid token format');
                return;
            }

            loginError.style.display = 'none';
            setConnectionStatus('connecting');

            try {
                const response = await fetch(`https://api.telegram.org/bot${token}/getMe`);
                const data = await response.json();

                if (!data.ok) {
                    showError('Invalid token. Please check and try again.');
                    setConnectionStatus('offline');
                    return;
                }

                botToken = token;
                chatId = cid;

                loginPanel.classList.add('hidden');
                messageInput.disabled = false;
                sendButton.disabled = false;
                messageInput.focus();

                setConnectionStatus('online');
                
                if (isFirstMessage) {
                    messagesArea.innerHTML = '';
                    isFirstMessage = false;
                }
                
                addMessage('received', `Connected as @${data.result.username}. You can start messaging.`);

                startPolling();

            } catch (error) {
                showError('Connection failed. Check your internet connection.');
                setConnectionStatus('offline');
                console.error('Connection error:', error);
            }
        }

        function setConnectionStatus(status) {
            statusDot.className = 'status-dot';
            switch (status) {
                case 'online':
                    statusDot.classList.add('online');
                    statusText.textContent = 'Connected';
                    break;
                case 'connecting':
                    statusDot.classList.add('connecting');
                    statusText.textContent = 'Connecting...';
                    break;
                case 'offline':
                    statusText.textContent = 'Not connected';
                    break;
            }
        }

        function showError(message) {
            loginError.textContent = message;
            loginError.style.display = 'block';
            setTimeout(() => {
                loginError.style.display = 'none';
            }, 4000);
        }

        function addMessage(type, text, time = new Date()) {
            if (isFirstMessage) {
                messagesArea.innerHTML = '';
                isFirstMessage = false;
            }

            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${type}`;
            
            const timeString = time.toLocaleTimeString('ru-RU', { 
                hour: '2-digit', 
                minute: '2-digit' 
            });
            
            messageDiv.innerHTML = `
                ${escapeHtml(text)}
                <span class="time">${timeString}</span>
            `;
            
            messagesArea.appendChild(messageDiv);
            scrollToBottom();
        }

        function escapeHtml(text) {
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }

        function scrollToBottom() {
            messagesArea.scrollTop = messagesArea.scrollHeight;
        }

        function showTyping(show) {
            typingIndicator.style.display = show ? 'block' : 'none';
            if (show) scrollToBottom();
        }

        async function sendMessage() {
            const text = messageInput.value.trim();
            if (!text || !botToken || !chatId) return;

            messageInput.value = '';
            sendButton.disabled = true;
            messageInput.disabled = true;

            addMessage('sent', text);

            try {
                const response = await fetch(
                    `https://api.telegram.org/bot${botToken}/sendMessage`,
                    {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                        },
                        body: JSON.stringify({
                            chat_id: chatId,
                            text: text,
                        }),
                    }
                );

                const data = await response.json();

                if (!data.ok) {
                    addMessage('received', `Send failed: ${data.description || 'Unknown error'}`);
                }
            } catch (error) {
                addMessage('received', 'Network error. Message may not have been sent.');
                console.error('Send error:', error);
            } finally {
                sendButton.disabled = false;
                messageInput.disabled = false;
                messageInput.focus();
            }
        }

        async function getUpdates() {
            if (!botToken) return;

            try {
                const response = await fetch(
                    `https://api.telegram.org/bot${botToken}/getUpdates?offset=${lastUpdateId + 1}&timeout=30`
                );
                const data = await response.json();

                if (data.ok && data.result.length > 0) {
                    for (const update of data.result) {
                        lastUpdateId = update.update_id;

                        if (update.message && update.message.chat.id.toString() === chatId) {
                            const msg = update.message;
                            
                            if (msg.from && !msg.from.is_bot) {
                                addMessage('received', msg.text || '[Media/File]', new Date(msg.date * 1000));
                            }
                        }
                    }
                }
            } catch (error) {
                console.error('Polling error:', error);
            }
        }

        function startPolling() {
            if (pollingInterval) clearInterval(pollingInterval);
            
            getUpdates();
            pollingInterval = setInterval(getUpdates, 3000);
        }

        messageInput.addEventListener('keypress', function(e) {
            if (e.key === 'Enter' && !sendButton.disabled) {
                sendMessage();
            }
        });

        sendButton.addEventListener('click', sendMessage);
    </script>
</body>
</html>
