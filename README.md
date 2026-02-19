<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Purchasing Assistant</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background: linear-gradient(135deg, #fef2f2 0%, #fee2e2 100%);
            min-height: 100vh;
            padding: 40px 20px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .container {
            max-width: 650px;
            width: 100%;
            margin: 0 auto;
            background: white;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
            overflow: hidden;
            display: flex;
            flex-direction: column;
            height: 85vh;
            min-height: 500px;
            max-height: 800px;
        }

        .header {
            background: linear-gradient(135deg, #dc2626 0%, #b91c1c 100%);
            color: white;
            padding: 24px 36px;
            flex-shrink: 0;
        }

        .header h1 {
            font-size: 22px;
            font-weight: 600;
            letter-spacing: -0.5px;
        }

        .header p {
            font-size: 13px;
            opacity: 0.8;
            margin-top: 4px;
        }

        .status-dot {
            display: inline-block;
            width: 8px;
            height: 8px;
            background: #86efac;
            border-radius: 50%;
            margin-right: 6px;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.4; }
        }

        /* Messages area */
        .messages {
            flex: 1;
            overflow-y: auto;
            padding: 24px 28px;
            display: flex;
            flex-direction: column;
            gap: 16px;
        }

        .messages::-webkit-scrollbar {
            width: 4px;
        }

        .messages::-webkit-scrollbar-track {
            background: #f9fafb;
        }

        .messages::-webkit-scrollbar-thumb {
            background: #e5e7eb;
            border-radius: 2px;
        }

        /* Message bubbles */
        .message-row {
            display: flex;
            gap: 10px;
            align-items: flex-end;
        }

        .message-row.user {
            flex-direction: row-reverse;
        }

        .avatar {
            width: 30px;
            height: 30px;
            border-radius: 50%;
            flex-shrink: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 12px;
            font-weight: 700;
            letter-spacing: 0;
        }

        .avatar.agent {
            background: linear-gradient(135deg, #dc2626 0%, #b91c1c 100%);
            color: white;
        }

        .avatar.user {
            background: #1f2937;
            color: white;
        }

        .bubble {
            max-width: 78%;
            padding: 12px 16px;
            border-radius: 12px;
            font-size: 14px;
            line-height: 1.6;
            color: #1f2937;
        }

        .bubble.agent {
            background: #f9fafb;
            border: 2px solid #f3f4f6;
            border-bottom-left-radius: 4px;
        }

        .bubble.user {
            background: linear-gradient(135deg, #dc2626 0%, #b91c1c 100%);
            color: white;
            border-bottom-right-radius: 4px;
        }

        .bubble .timestamp {
            font-size: 11px;
            opacity: 0.5;
            margin-top: 6px;
            display: block;
        }

        /* Warning/alert styling inside bubbles */
        .bubble .alert {
            background: #fef2f2;
            border: 2px solid #fca5a5;
            border-left: 4px solid #dc2626;
            border-radius: 6px;
            padding: 10px 12px;
            margin-top: 10px;
            font-size: 13px;
            color: #991b1b;
        }

        /* Typing indicator */
        .typing-indicator {
            display: none;
            align-items: flex-end;
            gap: 10px;
        }

        .typing-indicator.visible {
            display: flex;
        }

        .typing-bubble {
            background: #f9fafb;
            border: 2px solid #f3f4f6;
            border-radius: 12px;
            border-bottom-left-radius: 4px;
            padding: 14px 18px;
            display: flex;
            gap: 5px;
            align-items: center;
        }

        .typing-bubble span {
            width: 7px;
            height: 7px;
            background: #9ca3af;
            border-radius: 50%;
            animation: typing 1.2s infinite;
        }

        .typing-bubble span:nth-child(2) { animation-delay: 0.2s; }
        .typing-bubble span:nth-child(3) { animation-delay: 0.4s; }

        @keyframes typing {
            0%, 100% { transform: translateY(0); opacity: 0.4; }
            50% { transform: translateY(-4px); opacity: 1; }
        }

        /* Suggested prompts */
        .suggestions {
            padding: 0 28px 16px;
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            flex-shrink: 0;
        }

        .suggestion-chip {
            background: #fef2f2;
            border: 2px solid #fecaca;
            color: #991b1b;
            padding: 6px 12px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.15s ease;
            white-space: nowrap;
        }

        .suggestion-chip:hover {
            background: #fee2e2;
            border-color: #dc2626;
        }

        /* Input area */
        .input-area {
            padding: 16px 28px 24px;
            border-top: 2px solid #f3f4f6;
            flex-shrink: 0;
        }

        .input-row {
            display: flex;
            gap: 10px;
            align-items: flex-end;
        }

        .input-wrapper {
            flex: 1;
            position: relative;
        }

        textarea#userInput {
            width: 100%;
            padding: 12px 16px;
            border: 2px solid #e5e7eb;
            border-radius: 8px;
            font-size: 14px;
            font-family: inherit;
            resize: none;
            min-height: 46px;
            max-height: 120px;
            line-height: 1.5;
            transition: all 0.15s ease;
            overflow-y: hidden;
        }

        textarea#userInput:hover {
            border-color: #d1d5db;
        }

        textarea#userInput:focus {
            outline: none;
            border-color: #dc2626;
            box-shadow: 0 0 0 4px rgba(220, 38, 38, 0.08);
        }

        .send-btn {
            background: linear-gradient(135deg, #dc2626 0%, #b91c1c 100%);
            color: white;
            border: none;
            width: 46px;
            height: 46px;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.2s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-shrink: 0;
            box-shadow: 0 2px 4px rgba(220, 38, 38, 0.2);
        }

        .send-btn:hover {
            background: linear-gradient(135deg, #b91c1c 0%, #991b1b 100%);
            box-shadow: 0 4px 8px rgba(220, 38, 38, 0.3);
            transform: translateY(-1px);
        }

        .send-btn:active {
            transform: translateY(0);
        }

        .send-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none;
        }

        .send-btn svg {
            width: 18px;
            height: 18px;
        }

        /* Welcome message */
        .welcome {
            text-align: center;
            padding: 20px;
            color: #6b7280;
        }

        .welcome .icon {
            width: 48px;
            height: 48px;
            background: linear-gradient(135deg, #dc2626 0%, #b91c1c 100%);
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 auto 12px;
        }

        .welcome .icon svg {
            width: 24px;
            height: 24px;
            color: white;
        }

        .welcome h3 {
            font-size: 15px;
            font-weight: 600;
            color: #1f2937;
            margin-bottom: 6px;
        }

        .welcome p {
            font-size: 13px;
            line-height: 1.6;
        }

        /* Error message */
        .error-msg {
            background: #fee2e2;
            color: #991b1b;
            border: 2px solid #fca5a5;
            padding: 10px 14px;
            border-radius: 8px;
            font-size: 13px;
            font-weight: 500;
            margin-top: 8px;
            display: none;
        }

        /* Formatted response tables */
        .bubble table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
            font-size: 13px;
        }

        .bubble th {
            background: #f3f4f6;
            padding: 8px 10px;
            text-align: left;
            font-weight: 600;
            font-size: 11px;
            text-transform: uppercase;
            letter-spacing: 0.3px;
            color: #374151;
        }

        .bubble td {
            padding: 8px 10px;
            border-bottom: 1px solid #f3f4f6;
            color: #374151;
        }

        @media (max-width: 640px) {
            body {
                padding: 0;
                align-items: flex-start;
            }

            .container {
                border-radius: 0;
                box-shadow: none;
                height: 100vh;
                max-height: none;
            }

            .header {
                padding: 20px 20px;
            }

            .messages {
                padding: 16px 16px;
            }

            .suggestions {
                padding: 0 16px 12px;
            }

            .input-area {
                padding: 12px 16px 16px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Purchasing Assistant</h1>
            <p><span class="status-dot"></span>Online — SKU data loaded</p>
        </div>

        <div class="messages" id="messages">
            <div class="welcome">
                <div class="icon">
                    <svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                              d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"/>
                    </svg>
                </div>
                <h3>Ready to assist</h3>
                <p>Ask me about stock levels, purchase orders,<br>holds, or reorder suggestions.</p>
            </div>
        </div>

        <div class="typing-indicator" id="typingIndicator">
            <div class="avatar agent">PA</div>
            <div class="typing-bubble">
                <span></span><span></span><span></span>
            </div>
        </div>

        <div class="suggestions" id="suggestions">
            <button class="suggestion-chip" onclick="sendSuggestion(this)">Stock position of...</button>
            <button class="suggestion-chip" onclick="sendSuggestion(this)">Show Active Purchase Holds</button>
        </div>

        <div class="input-area">
            <div id="errorMsg" class="error-msg"></div>
            <div class="input-row">
                <div class="input-wrapper">
                    <textarea 
                        id="userInput" 
                        placeholder="Ask about stock..." 
                        rows="1"
                    ></textarea>
                </div>
                <button class="send-btn" id="sendBtn" onclick="sendMessage()">
                    <svg fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                              d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"/>
                    </svg>
                </button>
            </div>
        </div>
    </div>

    <script>
        // ── CONFIGURATION ──────────────────────────────────────────────
        const WEBHOOK_URL = 'https://spexoscar.app.n8n.cloud/webhook/d51ab97f-a54b-4fc5-a668-6a1c6204b91a/chat';
        // Replace with your N8N chat webhook URL
        // e.g. 'https://spexoscar.app.n8n.cloud/webhook/your-chat-id'
        // ──────────────────────────────────────────────────────────────

        const messagesEl = document.getElementById('messages');
        const inputEl = document.getElementById('userInput');
        const sendBtn = document.getElementById('sendBtn');
        const typingIndicator = document.getElementById('typingIndicator');
        const suggestionsEl = document.getElementById('suggestions');
        const errorMsg = document.getElementById('errorMsg');

        // Auto-resize textarea
        inputEl.addEventListener('input', function() {
            this.style.height = 'auto';
            this.style.height = Math.min(this.scrollHeight, 120) + 'px';
        });

        // Send on Enter (Shift+Enter for new line)
        inputEl.addEventListener('keydown', function(e) {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                sendMessage();
            }
        });

        function sendSuggestion(btn) {
            inputEl.value = btn.textContent;
            sendMessage();
        }

        function getTime() {
            return new Date().toLocaleTimeString('en-NZ', { 
                hour: '2-digit', minute: '2-digit' 
            });
        }

        function appendMessage(text, role) {
            // Hide welcome message on first real message
            const welcome = messagesEl.querySelector('.welcome');
            if (welcome) welcome.remove();

            // Hide suggestions after first use
            suggestionsEl.style.display = 'none';

            const row = document.createElement('div');
            row.className = `message-row ${role}`;

            const avatar = document.createElement('div');
            avatar.className = `avatar ${role}`;
            avatar.textContent = role === 'agent' ? 'PA' : 'ME';

            const bubble = document.createElement('div');
            bubble.className = `bubble ${role}`;

            // Render basic markdown-like formatting
            const formatted = text
                .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
                .replace(/⚠️/g, '<span style="font-size:15px">⚠️</span>')
                .replace(/\n/g, '<br>');

            bubble.innerHTML = `${formatted}<span class="timestamp">${getTime()}</span>`;

            row.appendChild(avatar);
            row.appendChild(bubble);
            messagesEl.appendChild(row);
            scrollToBottom();
        }

        function showTyping() {
            typingIndicator.classList.add('visible');
            scrollToBottom();
        }

        function hideTyping() {
            typingIndicator.classList.remove('visible');
        }

        function scrollToBottom() {
            messagesEl.scrollTop = messagesEl.scrollHeight;
        }

        function showError(msg) {
            errorMsg.textContent = msg;
            errorMsg.style.display = 'block';
            setTimeout(() => errorMsg.style.display = 'none', 5000);
        }

        async function sendMessage() {
            const text = inputEl.value.trim();
            if (!text) return;

            appendMessage(text, 'user');
            inputEl.value = '';
            inputEl.style.height = 'auto';
            sendBtn.disabled = true;
            showTyping();

            try {
                const response = await fetch(WEBHOOK_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        action: 'sendMessage',
                        chatInput: text,
                        sessionId: getSessionId()
                    })
                });

                if (!response.ok) throw new Error(`HTTP ${response.status}`);

                const data = await response.json();

                // N8N chat webhook returns { output: "..." } or { text: "..." }
                const reply = data.output || data.text || data.message || 
                              JSON.stringify(data);

                hideTyping();
                appendMessage(reply, 'agent');

            } catch (err) {
                hideTyping();
                showError(`Connection error: ${err.message}`);
                console.error(err);
            } finally {
                sendBtn.disabled = false;
                inputEl.focus();
            }
        }

        // Simple session ID so N8N memory node tracks the conversation
        function getSessionId() {
            if (!sessionStorage.getItem('chatSessionId')) {
                sessionStorage.setItem('chatSessionId', 
                    'session-' + Date.now() + '-' + Math.random().toString(36).substr(2, 9));
            }
            return sessionStorage.getItem('chatSessionId');
        }
    </script>
</body>
</html>
