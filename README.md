<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Halit Evin AI - Real Connection</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <!-- Markdown Parser -->
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=VT323&family=Fira+Code:wght@400;600&display=swap');

        body, html {
            margin: 0;
            padding: 0;
            height: 100%;
            overflow: hidden;
            background-color: #000;
            font-family: 'Fira Code', monospace;
        }

        #matrix-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
        }

        .glass-panel {
            background: rgba(0, 15, 0, 0.9);
            backdrop-filter: blur(5px);
            border: 1px solid #00ff00;
            box-shadow: 0 0 15px rgba(0, 255, 0, 0.15), inset 0 0 20px rgba(0, 20, 0, 0.8);
        }

        .neon-text {
            color: #00ff00;
            text-shadow: 0 0 5px #00ff00;
        }

        /* Scrollbar */
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: #001100; }
        ::-webkit-scrollbar-thumb { background: #004400; border-radius: 3px; }
        ::-webkit-scrollbar-thumb:hover { background: #006600; }

        .typing-cursor::after {
            content: '█';
            animation: blink 1s step-start infinite;
            margin-left: 2px;
            color: #00ff00;
        }

        @keyframes blink { 50% { opacity: 0; } }

        .source-badge {
            background: rgba(0, 40, 0, 0.6);
            border: 1px solid #005500;
            border-radius: 4px;
            padding: 4px 8px;
            font-size: 0.7rem;
            margin-right: 6px;
            margin-bottom: 6px;
            color: #00cc00;
            transition: all 0.2s ease;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            gap: 4px;
            text-decoration: none;
        }

        .source-badge:hover {
            background: #00ff00;
            color: #000;
            border-color: #00ff00;
        }

        .loading-text {
            color: #00ff00;
            font-size: 0.8rem;
            font-family: 'VT323', monospace;
        }

        /* Markdown Styles within Chat */
        .markdown-content strong { color: #fff; font-weight: bold; }
        .markdown-content a { color: #4ade80; text-decoration: underline; }
        .markdown-content ul { list-style-type: disc; margin-left: 1.5em; }
        .markdown-content p { margin-bottom: 0.5em; }
    </style>
</head>
<body class="relative flex items-center justify-center">

    <canvas id="matrix-canvas"></canvas>

    <div class="relative z-10 w-full max-w-5xl h-[95vh] flex flex-col p-2 md:p-4">
        
        <!-- Header -->
        <header class="glass-panel p-3 mb-3 rounded-t-lg flex justify-between items-center select-none">
            <div class="flex items-center gap-3">
                <i class="fas fa-network-wired text-xl neon-text"></i>
                <div>
                    <h1 class="text-2xl font-bold neon-text tracking-widest font-['VT323']">HALIT EVIN AI <span class="text-xs border border-green-500 px-1 rounded">PRO</span></h1>
                    <div class="text-[10px] text-green-500 flex items-center gap-1">
                        <span class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></span>
                        GOOGLE GROUNDING: ONLINE
                    </div>
                </div>
            </div>
            <div class="text-right">
                 <button id="settings-btn" class="text-green-700 hover:text-green-400 transition"><i class="fas fa-cog"></i></button>
            </div>
        </header>

        <!-- Chat Area -->
        <div id="chat-container" class="glass-panel flex-1 overflow-y-auto p-4 mb-3 rounded flex flex-col gap-4">
            <div class="flex flex-col gap-1 items-start">
                <div class="flex items-center gap-2 mb-1">
                    <span class="text-xs text-green-500 font-bold">[SYSTEM]</span>
                </div>
                <div class="p-3 rounded-lg bg-green-900/20 border-l-2 border-green-500 text-green-100 max-w-[90%]">
                    <p>Sistem çevrimiçi. Google Gemini 1.5 Flash modeline bağlıyım. İnternet erişimim var. Ne sormak istersin?</p>
                </div>
            </div>
        </div>

        <!-- Input Area -->
        <div class="glass-panel p-3 rounded-b-lg">
            <form id="chat-form" class="flex gap-2">
                <input 
                    type="text" 
                    id="user-input" 
                    class="flex-1 bg-black/60 border border-green-900 text-green-400 p-3 rounded focus:outline-none focus:border-green-500 font-['Fira_Code'] placeholder-green-900/50"
                    placeholder="Sorgu giriniz (Örn: Bugün dolar kuru ne kadar?)"
                    autocomplete="off"
                >
                <button type="submit" class="bg-green-900/20 hover:bg-green-500 hover:text-black border border-green-600 text-green-500 px-6 rounded transition-all font-bold">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </form>
        </div>
    </div>

    <!-- Hidden Settings Modal for Key -->
    <div id="settings-modal" class="hidden fixed inset-0 bg-black/80 z-50 flex items-center justify-center backdrop-blur-sm">
        <div class="glass-panel p-6 rounded-lg w-96 max-w-full">
            <h2 class="text-green-400 text-xl mb-4 font-['VT323']">SYSTEM CONFIG</h2>
            <label class="block text-green-600 text-xs mb-2">API KEY</label>
            <input type="password" id="api-key-input" class="w-full bg-black border border-green-700 text-green-500 p-2 rounded mb-4 focus:outline-none focus:border-green-400">
            <div class="flex justify-end gap-2">
                <button id="save-key-btn" class="bg-green-600 text-black px-4 py-1 rounded hover:bg-green-400 font-bold">SAVE</button>
                <button id="close-modal-btn" class="text-green-600 px-4 py-1 hover:text-white">CLOSE</button>
            </div>
        </div>
    </div>

    <script>
        // --- CONFIGURATION ---
        // GÜVENLİK NOTU: Bu anahtar tarayıcıda çalışır. Public ortamda paylaşmayın.
        let API_KEY = "AIzaSyAZs2Tx0I0cZycadJGHcvSBa2RYXKcU3YU"; 

        // --- MATRIX BACKGROUND ---
        const canvas = document.getElementById('matrix-canvas');
        const ctx = canvas.getContext('2d');
        let width, height;
        const matrixChars = 'HALITEVINAI0123456789XZ';
        
        function resizeCanvas() {
            width = window.innerWidth;
            height = window.innerHeight;
            canvas.width = width;
            canvas.height = height;
        }
        resizeCanvas();
        window.addEventListener('resize', resizeCanvas);

        const columns = Math.floor(width / 20);
        const ypos = Array(columns).fill(0);

        function matrix() {
            ctx.fillStyle = '#00000010'; 
            ctx.fillRect(0, 0, width, height);
            ctx.fillStyle = '#0f0';
            ctx.font = '14pt monospace';

            ypos.forEach((y, index) => {
                const text = matrixChars.charAt(Math.floor(Math.random() * matrixChars.length));
                const x = index * 20;
                ctx.fillText(text, x, y);
                if (y > height && Math.random() > 0.985) ypos[index] = 0;
                else ypos[index] = y + 20;
            });
        }
        setInterval(matrix, 50);

        // --- CHAT LOGIC ---
        const chatForm = document.getElementById('chat-form');
        const userInput = document.getElementById('user-input');
        const chatContainer = document.getElementById('chat-container');
        const apiKeyInput = document.getElementById('api-key-input');
        
        // Load API Key to UI
        apiKeyInput.value = API_KEY;

        // Settings Modal Logic
        const settingsModal = document.getElementById('settings-modal');
        document.getElementById('settings-btn').onclick = () => settingsModal.classList.remove('hidden');
        document.getElementById('close-modal-btn').onclick = () => settingsModal.classList.add('hidden');
        document.getElementById('save-key-btn').onclick = () => {
            API_KEY = apiKeyInput.value;
            settingsModal.classList.add('hidden');
            appendSystemMessage("API Key güncellendi.");
        };

        chatForm.addEventListener('submit', async (e) => {
            e.preventDefault();
            const message = userInput.value.trim();
            if (!message) return;

            appendMessage('user', message);
            userInput.value = '';

            await fetchGeminiResponse(message);
        });

        function appendMessage(sender, text, sources = []) {
            const msgDiv = document.createElement('div');
            msgDiv.className = `flex flex-col gap-1 ${sender === 'user' ? 'items-end' : 'items-start'}`;

            const header = document.createElement('div');
            header.className = 'flex items-center gap-2 mb-1';
            header.innerHTML = sender === 'user' 
                ? `<span class="text-xs text-gray-500">YOU</span> <span class="text-xs text-green-600 font-bold">[OP]</span>`
                : `<span class="text-xs text-green-600 font-bold">[HALIT EVIN AI]</span> <span class="text-xs text-gray-500">LIVE</span>`;

            const contentBox = document.createElement('div');
            contentBox.className = sender === 'user' 
                ? 'p-3 rounded-lg bg-green-900/30 border border-green-800 text-green-100 max-w-[90%]'
                : 'p-3 rounded-lg bg-green-900/20 border-l-2 border-green-500 text-green-100 max-w-[90%] w-full markdown-content';

            // Parse Markdown for AI, Plain text for User
            if (sender === 'ai') {
                contentBox.innerHTML = marked.parse(text);
            } else {
                contentBox.textContent = text;
            }

            // Sources Section
            if (sources && sources.length > 0) {
                const sourceContainer = document.createElement('div');
                sourceContainer.className = 'mt-3 pt-2 border-t border-green-900/40';
                sourceContainer.innerHTML = '<div class="text-[10px] text-gray-400 mb-2 uppercase tracking-wider">Google Grounding Sources:</div>';
                
                sources.forEach(src => {
                    if(!src.title) return;
                    const badge = document.createElement('a');
                    badge.href = src.uri || '#';
                    badge.target = '_blank';
                    badge.className = 'source-badge';
                    badge.innerHTML = `<i class="fas fa-link text-[8px]"></i> ${src.title}`;
                    sourceContainer.appendChild(badge);
                });
                contentBox.appendChild(sourceContainer);
            }

            msgDiv.appendChild(header);
            msgDiv.appendChild(contentBox);
            chatContainer.appendChild(msgDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        function appendSystemMessage(text) {
            const div = document.createElement('div');
            div.className = 'text-center my-2';
            div.innerHTML = `<span class="text-[10px] bg-green-900/50 text-green-400 px-2 py-1 rounded border border-green-800">${text}</span>`;
            chatContainer.appendChild(div);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        async function fetchGeminiResponse(query) {
            // Loading Indicator
            const loadingId = 'loading-' + Date.now();
            const loadingDiv = document.createElement('div');
            loadingDiv.id = loadingId;
            loadingDiv.className = 'ml-2 my-2 loading-text';
            loadingDiv.innerHTML = `
                <div>> Bağlantı kuruluyor: generativelanguage.googleapis.com...</div>
                <div>> Google Arama Araçları: <span class="animate-pulse">AKTİF</span></div>
            `;
            chatContainer.appendChild(loadingDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;

            try {
                const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${API_KEY}`;
                
                const payload = {
                    contents: [{ parts: [{ text: query }] }],
                    tools: [{ google_search: {} }] // Enable Google Search Grounding
                };

                const response = await fetch(url, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error(`API Hatası: ${response.status}`);

                const data = await response.json();
                
                // Remove Loading
                document.getElementById(loadingId).remove();

                // Extract Data
                const candidate = data.candidates?.[0];
                const text = candidate?.content?.parts?.[0]?.text || "Veri alınamadı.";
                
                // Extract Grounding Metadata
                let sources = [];
                const groundingMetadata = candidate?.groundingMetadata;
                
                if (groundingMetadata?.groundingChunks) {
                    sources = groundingMetadata.groundingChunks
                        .filter(chunk => chunk.web)
                        .map(chunk => ({
                            title: chunk.web.title,
                            uri: chunk.web.uri
                        }));
                }

                appendMessage('ai', text, sources);

            } catch (error) {
                document.getElementById(loadingId).remove();
                appendSystemMessage("BAĞLANTI HATASI: " + error.message);
                console.error(error);
            }
        }
    </script>
</body>
</html>

