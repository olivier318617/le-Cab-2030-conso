<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Le Cab 2030</title>
    <link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Inter:wght@300;400;500&display=swap" rel="stylesheet">
    <!-- Bibliothèques pour lire les documents -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mammoth/1.6.0/mammoth.browser.min.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            margin: 0;
            padding: 20px;
            background: linear-gradient(135deg, #f5f5dc 0%, #e8f5e9 100%);
            min-height: 100vh;
        }
        
        .chat-container {
            max-width: 700px;
            margin: 0 auto;
            background-color: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.08);
            overflow: hidden;
            backdrop-filter: blur(10px);
        }
        
        .chat-header {
            background: linear-gradient(135deg, #c5c89d 0%, #a8c8a2 100%);
            color: #2c3e2c;
            padding: 25px;
            text-align: center;
            position: relative;
        }
        
        .chat-title {
            font-family: 'Bebas Neue', cursive;
            font-size: 42px;
            margin: 0;
            letter-spacing: 3px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.1);
            background: linear-gradient(45deg, #4a5c4a, #6b8b65);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        
        .chat-subtitle {
            font-size: 14px;
            color: #5a6b5a;
            margin-top: 5px;
            font-weight: 300;
        }
        
        .chat-messages {
            height: 450px;
            overflow-y: auto;
            padding: 25px;
            background-color: #fafaf8;
        }
        
        .message {
            margin-bottom: 20px;
            padding: 12px 18px;
            border-radius: 18px;
            max-width: 70%;
            animation: fadeIn 0.3s ease-in;
            word-wrap: break-word;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .user-message {
            background: linear-gradient(135deg, #a8c8a2 0%, #8fb589 100%);
            color: white;
            margin-left: auto;
            text-align: right;
            box-shadow: 0 3px 10px rgba(140, 180, 140, 0.3);
        }
        
        .bot-message {
            background-color: #f0f0e6;
            color: #3e4e3e;
            border: 1px solid #e0e0d0;
        }
        
        .document-message {
            background-color: #e8f0e8;
            border: 2px solid #a8c8a2;
            padding: 15px;
            margin: 10px 0;
            border-radius: 12px;
        }
        
        .document-header {
            font-weight: 500;
            color: #4a5c4a;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .document-content {
            font-size: 14px;
            line-height: 1.6;
            color: #5a6b5a;
            max-height: 200px;
            overflow-y: auto;
            padding: 10px;
            background-color: rgba(255, 255, 255, 0.5);
            border-radius: 8px;
            margin-top: 10px;
        }
        
        .chat-input-container {
            background-color: #f8f8f5;
            border-top: 1px solid #e0e0d0;
            padding: 20px;
        }
        
        .input-wrapper {
            display: flex;
            gap: 10px;
            align-items: center;
        }
        
        #messageInput {
            flex: 1;
            padding: 12px 18px;
            border: 2px solid #d0d0c0;
            border-radius: 25px;
            font-size: 15px;
            background-color: white;
            transition: border-color 0.3s;
        }
        
        #messageInput:focus {
            outline: none;
            border-color: #8fb589;
        }
        
        .button-group {
            display: flex;
            gap: 8px;
        }
        
        .btn {
            padding: 12px 20px;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            font-size: 15px;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        #sendButton {
            background: linear-gradient(135deg, #8fb589 0%, #7aa574 100%);
            color: white;
        }
        
        #sendButton:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(140, 180, 140, 0.3);
        }
        
        #fileButton {
            background: linear-gradient(135deg, #d4d4c4 0%, #c0c0b0 100%);
            color: #4a5c4a;
            position: relative;
        }
        
        #fileButton:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(192, 192, 176, 0.3);
        }
        
        .btn:disabled {
            background: #e0e0d0;
            cursor: not-allowed;
            transform: none;
        }
        
        #fileInput {
            display: none;
        }
        
        .typing-indicator {
            display: none;
            padding: 15px;
            font-style: italic;
            color: #7a8a7a;
            text-align: center;
            background-color: #f5f5f0;
        }
        
        .file-preview {
            background-color: #f0f0e6;
            border: 1px dashed #c0c0b0;
            border-radius: 10px;
            padding: 10px;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            font-size: 14px;
            color: #5a6b5a;
        }
        
        .file-info {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .file-status {
            font-size: 12px;
            color: #8fb589;
            font-style: italic;
        }
        
        .file-error {
            color: #d9534f;
        }
        
        .file-remove {
            cursor: pointer;
            color: #d9534f;
            font-weight: bold;
            padding: 5px;
        }
        
        .file-icon {
            font-size: 20px;
        }

        /* Icônes */
        .icon {
            width: 20px;
            height: 20px;
            display: inline-block;
            vertical-align: middle;
        }

        /* Scrollbar personnalisée */
        .chat-messages::-webkit-scrollbar {
            width: 8px;
        }

        .chat-messages::-webkit-scrollbar-track {
            background: #f0f0e6;
            border-radius: 10px;
        }

        .chat-messages::-webkit-scrollbar-thumb {
            background: #c0c0b0;
            border-radius: 10px;
        }

        .chat-messages::-webkit-scrollbar-thumb:hover {
            background: #a0a090;
        }
        
        /* Indicateur de chargement */
        .loading-spinner {
            display: inline-block;
            width: 14px;
            height: 14px;
            border: 2px solid rgba(0,0,0,.1);
            border-radius: 50%;
            border-top-color: #8fb589;
            animation: spin 0.8s linear infinite;
        }
        
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
        
        /* Message d'information */
        .info-message {
            background-color: #e3f2e3;
            border-left: 4px solid #8fb589;
            padding: 12px 16px;
            margin: 10px 0;
            border-radius: 8px;
            font-size: 14px;
            color: #4a5c4a;
        }
    </style>
</head>
<body>
    <div class="chat-container">
        <div class="chat-header">
            <h1 class="chat-title">Le Cab 2030</h1>
            <p class="chat-subtitle">Votre assistant intelligent du futur</p>
        </div>
        
        <div class="chat-messages" id="chatMessages">
            <div class="message bot-message">
                Bonjour ! Je suis votre assistant du Cab 2030. Je peux vous aider avec vos documents PDF, Word et DOCX. Comment puis-je vous aider aujourd'hui ?
            </div>
        </div>
        
        <div class="typing-indicator" id="typingIndicator">
            L'assistant est en train d'écrire...
        </div>
        
        <div class="chat-input-container">
            <div id="filePreview"></div>
            <div class="input-wrapper">
                <input 
                    type="text" 
                    id="messageInput" 
                    placeholder="Tapez votre message..."
                    onkeypress="handleKeyPress(event)"
                />
                <div class="button-group">
                    <button id="fileButton" class="btn" onclick="document.getElementById('fileInput').click()">
                        <svg class="icon" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M14,2H6A2,2 0 0,0 4,4V20A2,2 0 0,0 6,22H18A2,2 0 0,0 20,20V8L14,2M18,20H6V4H13V9H18V20Z"/>
                        </svg>
                        Fichier
                    </button>
                    <input type="file" id="fileInput" onchange="handleFileSelect(event)" accept=".pdf,.doc,.docx" multiple>
                    <button id="sendButton" class="btn" onclick="sendMessage()">
                        <svg class="icon" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M2.01 21L23 12 2.01 3 2 10l15 2-15 2z"/>
                        </svg>
                        Envoyer
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Configuration de PDF.js
        pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
        
        const webhookUrl = "https://n8n.srv773091.hstgr.cloud/webhook/31115b30-9e38-4096-b685-676c60fad227/chat";
        let selectedFiles = [];
        let extractedTexts = {};
        
        function handleKeyPress(event) {
            if (event.key === 'Enter') {
                sendMessage();
            }
        }
        
        function getFileIcon(filename) {
            const extension = filename.split('.').pop().toLowerCase();
            switch(extension) {
                case 'pdf':
                    return '📄';
                case 'doc':
                case 'docx':
                    return '📝';
                default:
                    return '📎';
            }
        }
        
        async function handleFileSelect(event) {
            const files = Array.from(event.target.files);
            const validFiles = files.filter(file => {
                const extension = file.name.split('.').pop().toLowerCase();
                return ['pdf', 'doc', 'docx'].includes(extension);
            });
            
            if (validFiles.length < files.length) {
                addMessage("⚠️ Certains fichiers ont été ignorés. Seuls les formats PDF, DOC et DOCX sont acceptés.", 'bot');
            }
            
            for (const file of validFiles) {
                selectedFiles.push(file);
                await extractTextFromFile(file);
            }
            
            displayFilePreview();
        }
        
        async function extractTextFromFile(file) {
            const extension = file.name.split('.').pop().toLowerCase();
            const fileId = `file_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
            
            try {
                let text = '';
                
                if (extension === 'pdf') {
                    text = await extractTextFromPDF(file);
                } else if (extension === 'doc' || extension === 'docx') {
                    text = await extractTextFromWord(file);
                }
                
                extractedTexts[fileId] = {
                    filename: file.name,
                    content: text,
                    type: extension
                };
                
                file.extractedId = fileId;
                
            } catch (error) {
                console.error('Erreur lors de l\'extraction du texte:', error);
                addMessage(`❌ Erreur lors de la lecture du fichier ${file.name}`, 'bot');
            }
        }
        
        async function extractTextFromPDF(file) {
            const arrayBuffer = await file.arrayBuffer();
            const pdf = await pdfjsLib.getDocument(arrayBuffer).promise;
            let fullText = '';
            
            for (let i = 1; i <= pdf.numPages; i++) {
                const page = await pdf.getPage(i);
                const textContent = await page.getTextContent();
                const pageText = textContent.items.map(item => item.str).join(' ');
                fullText += pageText + '\n';
            }
            
            return fullText;
        }
        
        async function extractTextFromWord(file) {
            const arrayBuffer = await file.arrayBuffer();
            const result = await mammoth.extractRawText({ arrayBuffer: arrayBuffer });
            return result.value;
        }
        
        function displayFilePreview() {
            const previewContainer = document.getElementById('filePreview');
            previewContainer.innerHTML = '';
            
            selectedFiles.forEach((file, index) => {
                const fileDiv = document.createElement('div');
                fileDiv.className = 'file-preview';
                
                const extractedText = file.extractedId ? extractedTexts[file.extractedId] : null;
                const status = extractedText ? 
                    `<span class="file-status">✓ Prêt à envoyer</span>` : 
                    `<span class="file-status"><span class="loading-spinner"></span> Lecture...</span>`;
                
                fileDiv.innerHTML = `
                    <div class="file-info">
                        <span class="file-icon">${getFileIcon(file.name)}</span>
                        <div>
                            <div>${file.name} (${formatFileSize(file.size)})</div>
                            ${status}
                        </div>
                    </div>
                    <span class="file-remove" onclick="removeFile(${index})">✕</span>
                `;
                previewContainer.appendChild(fileDiv);
            });
        }
        
        function removeFile(index) {
            const file = selectedFiles[index];
            if (file.extractedId) {
                delete extractedTexts[file.extractedId];
            }
            selectedFiles.splice(index, 1);
            displayFilePreview();
        }
        
        function formatFileSize(bytes) {
            if (bytes === 0) return '0 Bytes';
            const k = 1024;
            const sizes = ['Bytes', 'KB', 'MB', 'GB'];
            const i = Math.floor(Math.log(bytes) / Math.log(k));
            return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
        }
        
        async function sendMessage() {
            const input = document.getElementById('messageInput');
            const message = input.value.trim();
            
            if (!message && selectedFiles.length === 0) return;
            
            // Désactiver l'input pendant l'envoi
            input.disabled = true;
            document.getElementById('sendButton').disabled = true;
            document.getElementById('fileButton').disabled = true;
            
            // Ajouter le message de l'utilisateur
            if (message) {
                addMessage(message, 'user');
            }
            
            // Traiter et afficher les fichiers
            if (selectedFiles.length > 0) {
                for (const file of selectedFiles) {
                    if (file.extractedId && extractedTexts[file.extractedId]) {
                        const extracted = extractedTexts[file.extractedId];
                        addDocumentMessage(extracted.filename, extracted.content, extracted.type);
                    }
                }
            }
            
            // Vider l'input
            input.value = '';
            
            // Afficher l'indicateur de frappe
            document.getElementById('typingIndicator').style.display = 'block';
            
            try {
                // Préparer les données à envoyer
                const payload = {
                    message: message,
                    timestamp: new Date().toISOString(),
                    documents: []
                };
                
                // Ajouter les contenus extraits des documents
                for (const file of selectedFiles) {
                    if (file.extractedId && extractedTexts[file.extractedId]) {
                        const extracted = extractedTexts[file.extractedId];
                        payload.documents.push({
                            filename: extracted.filename,
                            type: extracted.type,
                            content: extracted.content
                        });
                    }
                }
                
                // Envoyer la requête au webhook N8N
                const response = await fetch(webhookUrl, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(payload)
                });
                
                if (!response.ok) {
                    throw new Error('Erreur de connexion');
                }
                
                const responseText = await response.text();
                console.log('Réponse brute du webhook:', responseText);
                
                let data;
                try {
                    data = JSON.parse(responseText);
                } catch (e) {
                    addMessage(responseText, 'bot');
                    return;
                }
                
                let botResponse;
                if (typeof data === 'string') {
                    botResponse = data;
                } else if (typeof data === 'object') {
                    botResponse = data.message || 
                                 data.response || 
                                 data.text || 
                                 data.output ||
                                 data.answer ||
                                 data.reply ||
                                 data.content ||
                                 data.body ||
                                 JSON.stringify(data);
                }
                
                addMessage(botResponse, 'bot');
                
            } catch (error) {
                console.error('Erreur:', error);
                addMessage('Désolé, une erreur s\'est produite. Veuillez réessayer.', 'bot');
            } finally {
                // Nettoyer
                selectedFiles = [];
                extractedTexts = {};
                displayFilePreview();
                
                document.getElementById('typingIndicator').style.display = 'none';
                input.disabled = false;
                document.getElementById('sendButton').disabled = false;
                document.getElementById('fileButton').disabled = false;
                input.focus();
            }
        }
        
        function addMessage(text, sender) {
            const messagesContainer = document.getElementById('chatMessages');
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${sender}-message`;
            messageDiv.textContent = text;
            messagesContainer.appendChild(messageDiv);
            
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }
        
        function addDocumentMessage(filename, content, type) {
            const messagesContainer = document.getElementById('chatMessages');
            const messageDiv = document.createElement('div');
            messageDiv.className = 'document-message';
            
            const icon = getFileIcon(filename);
            const preview = content.substring(0, 500) + (content.length > 500 ? '...' : '');
            
            messageDiv.innerHTML = `
                <div class="document-header">
                    ${icon} Document envoyé: ${filename}
                </div>
                <div class="document-content">
                    ${preview.replace(/\n/g, '<br>')}
                </div>
            `;
            
            messagesContainer.appendChild(messageDiv);
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }
    </script>
</body>
</html>
