<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Quest - مساعد الدردشة الذكي</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap');

        :root {
            --primary-green: #006400; /* Dark Green - Background */
            --card-dark-green: #004d00; /* Even darker green for cards */
            --accent-orange: #ff8c00; /* Dark Orange - for accents */
            --dark-background: #222;  /* Dark Black - for header/footer/card border */
            --light-text: #f4f7f6;
            --card-text-color: #f4f7f6; /* Light text for dark green cards */
            --card-shadow: rgba(0,0,0,0.08);
            --hover-shadow: rgba(0,0,0,0.15);
            --pin-color: #ff8c00; /* Orange color for the pin on dark green cards */
            --icon-color: #ff8c00; /* Orange color for icons */

            /* Chatbot specific colors */
            --chat-bubble-user: #ff8c00; /* Orange for user messages */
            --chat-bubble-ai: #004d00; /* Darker green for AI messages */
            --chat-input-bg: #333; /* Dark background for input */
            --chat-border: #444; /* Border color for chat area */
        }

        body {
            font-family: 'Cairo', sans-serif;
            margin: 0;
            padding: 0;
            background-color: var(--primary-green); /* Dark Green Background */
            color: var(--light-text);
            line-height: 1.6;
            overflow-x: hidden;
            position: relative;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }

        .snowflakes {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: -1;
        }

        .snowflake {
            position: absolute;
            background-color: rgba(255, 255, 255, 0.8);
            border-radius: 50%;
            opacity: 0.8;
            animation: fall linear infinite;
        }

        @keyframes fall {
            0% { transform: translateY(-10vh); opacity: 0; }
            10% { opacity: 0.8; }
            100% { transform: translateY(100vh); opacity: 0; }
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
            position: relative;
            z-index: 1;
        }

        header {
            background-color: var(--dark-background);
            color: var(--light-text);
            padding: 20px 0;
            text-align: center;
            box-shadow: 0 2px 4px rgba(0,0,0,0.2);
            position: relative;
        }

        header h1 {
            margin: 0;
            font-size: 2.5em;
            color: var(--accent-orange);
        }

        header p {
            font-size: 1.2em;
            margin-top: 10px;
            color: var(--light-text);
        }

        .back-button {
            position: absolute;
            top: 50%;
            right: 20px; /* Adjust as needed for RTL */
            transform: translateY(-50%);
            background-color: var(--accent-orange);
            color: var(--dark-background);
            padding: 10px 15px;
            border-radius: 5px;
            text-decoration: none;
            font-weight: bold;
            transition: background-color 0.3s ease;
        }

        .back-button:hover {
            background-color: #e67e00;
        }

        main {
            flex-grow: 1;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 40px 0;
        }

        .chatbot-container {
            background-color: var(--card-dark-green);
            color: var(--card-text-color);
            border-radius: 10px;
            padding: 30px;
            box-shadow: 0 4px 8px var(--card-shadow),
                        inset 0 0 10px rgba(0,0,0,0.3);
            border: 2px solid var(--dark-background);
            max-width: 700px; /* Max width for the chatbot interface */
            width: 90%;
            display: flex;
            flex-direction: column;
            height: 70vh; /* Make chatbot container take a good portion of viewport height */
            background-image:
                linear-gradient(90deg, rgba(0,0,0,0.1) 1px, transparent 1px),
                linear-gradient(rgba(0,0,0,0.1) 1px, transparent 1px);
            background-size: 25% 25%, 25% 25%;
            background-repeat: repeat;
        }

        .chat-header {
            text-align: center;
            margin-bottom: 20px;
            color: var(--accent-orange);
            font-size: 1.8em;
            border-bottom: 2px solid var(--accent-orange);
            padding-bottom: 10px;
        }

        .chat-box {
            flex-grow: 1;
            overflow-y: auto;
            border: 1px solid var(--chat-border);
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 20px;
            background-color: #1a1a1a; /* Darker background for chat messages */
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .chat-message {
            display: flex;
            margin-bottom: 10px;
        }

        .chat-message.user {
            justify-content: flex-start; /* User messages on the right in RTL */
        }

        .chat-message.ai {
            justify-content: flex-end; /* AI messages on the left in RTL */
        }
        
        /* Adjust for RTL: user message bubble on right, AI on left */
        html[dir="rtl"] .chat-message.user {
            justify-content: flex-end;
        }

        html[dir="rtl"] .chat-message.ai {
            justify-content: flex-start;
        }


        .message-bubble {
            max-width: 80%;
            padding: 12px 18px;
            border-radius: 20px;
            line-height: 1.4;
            position: relative;
            word-wrap: break-word; /* Ensure long words wrap */
            white-space: pre-wrap; /* Preserve whitespace and allow wrapping */
        }

        .message-bubble.user {
            background-color: var(--chat-bubble-user);
            color: var(--dark-background);
            align-self: flex-end; /* User bubble to the right */
            border-bottom-left-radius: 5px; /* Adjust corner for pointer */
        }

        .message-bubble.ai {
            background-color: var(--chat-bubble-ai);
            color: var(--light-text);
            align-self: flex-start; /* AI bubble to the left */
            border-bottom-right-radius: 5px; /* Adjust corner for pointer */
        }

        /* Loading indicator */
        .loading-dots span {
            display: inline-block;
            width: 8px;
            height: 8px;
            background-color: var(--accent-orange);
            border-radius: 50%;
            animation: bounce 1.4s infinite ease-in-out both;
            margin: 0 2px;
        }

        .loading-dots span:nth-child(1) { animation-delay: -0.32s; }
        .loading-dots span:nth-child(2) { animation-delay: -0.16s; }
        .loading-dots span:nth-child(3) { animation-delay: 0s; }

        @keyframes bounce {
            0%, 80%, 100% { transform: scale(0); }
            40% { transform: scale(1); }
        }

        .chat-input-area {
            display: flex;
            gap: 10px;
        }

        .chat-input-area textarea {
            flex-grow: 1;
            padding: 12px 15px;
            border: 1px solid var(--chat-border);
            border-radius: 8px;
            background-color: var(--chat-input-bg);
            color: var(--light-text);
            font-family: 'Cairo', sans-serif;
            font-size: 1em;
            resize: none; /* Disable manual resize */
            height: 40px; /* Initial height */
            overflow-y: hidden; /* Hide scrollbar initially */
            transition: height 0.2s ease;
        }

        .chat-input-area textarea:focus {
            outline: none;
            border-color: var(--accent-orange);
            box-shadow: 0 0 0 2px rgba(255, 140, 0, 0.5);
        }

        .chat-input-area button {
            background-color: var(--accent-orange);
            color: var(--dark-background);
            border: none;
            border-radius: 8px;
            padding: 10px 20px;
            font-size: 1.1em;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .chat-input-area button:hover {
            background-color: #e67e00;
            transform: translateY(-2px);
        }

        .chat-input-area button i {
            margin-right: 5px; /* Space between icon and text */
        }

        footer {
            background-color: var(--dark-background);
            color: var(--light-text);
            padding: 40px 0;
            text-align: center;
            margin-top: auto;
        }

        footer .contact-info p {
            margin: 5px 0;
            font-size: 1.1em;
        }

        footer .contact-info a {
            color: var(--light-text);
            text-decoration: none;
        }

        footer .contact-info a:hover {
            text-decoration: underline;
        }

        /* Responsive Design */
        @media (max-width: 768px) {
            header h1 {
                font-size: 2em;
            }

            .back-button {
                top: 20px;
                right: 15px;
                transform: none;
                padding: 8px 12px;
                font-size: 0.9em;
            }

            .chatbot-container {
                padding: 20px;
                height: 80vh; /* Allow more height on smaller screens */
            }

            .chat-header {
                font-size: 1.5em;
            }

            .chat-input-area {
                flex-direction: column; /* Stack input and button vertically */
                gap: 15px;
            }

            .chat-input-area button {
                width: 100%;
                padding: 10px 15px;
                font-size: 1em;
            }
        }
    </style>
</head>
<body>
    <div class="snowflakes"></div>

    <header>
        <div class="container">
            <a href="go:home" class="back-button">العودة</a>
            <h1>AI Quest</h1>
            <p>مساعد الدردشة الذكي</p>
        </div>
    </header>

    <main>
        <section class="chatbot-container">
            <div class="chat-header">
                <i class="fas fa-robot"></i> مساعد AI Quest
            </div>
            <div class="chat-box" id="chatBox">
                <div class="chat-message ai">
                    <div class="message-bubble ai">مرحباً بك! كيف يمكنني مساعدتك اليوم؟</div>
                </div>
            </div>
            <div class="chat-input-area">
                <textarea id="chatInput" placeholder="اكتب رسالتك هنا..." rows="1"></textarea>
                <button id="sendMessageButton"><i class="fas fa-paper-plane"></i> إرسال</button>
            </div>
        </section>
    </main>

    <footer>
        <div class="container">
            <div class="contact-info">
                <p>للتواصل معنا:</p>
                <p><i class="fas fa-phone"></i> الهاتف: <a href="tel:+212667432463">+212 667-432463</a></p>
                <p><i class="fas fa-envelope"></i> البريد الإلكتروني: <a href="mailto:directmatch123@gmail.com">directmatch123@gmail.com</a></p>
            </div>
            <p>&copy; 2023 AI Quest. جميع الحقوق محفوظة.</p>
        </div>
    </footer>

    <script>
        // Script for snowflakes (existing)
        const snowflakesContainer = document.querySelector('.snowflakes');
        const numberOfSnowflakes = 50;

        function createSnowflake() {
            const snowflake = document.createElement('div');
            snowflake.classList.add('snowflake');
            snowflakesContainer.appendChild(snowflake);

            const size = Math.random() * 5 + 5;
            snowflake.style.width = `${size}px`;
            snowflake.style.height = `${size}px`;
            snowflake.style.left = `${Math.random() * 100}vw`;
            snowflake.style.animationDuration = `${Math.random() * 10 + 5}s`;
            snowflake.style.animationDelay = `${Math.random() * 5}s`;
            snowflake.style.opacity = Math.random() * 0.5 + 0.5;
            
            snowflake.addEventListener('animationend', () => {
                snowflake.remove();
                createSnowflake();
            });
        }

        for (let i = 0; i < numberOfSnowflakes; i++) {
            createSnowflake();
        }

        // Chatbot logic (Frontend only - NO API KEY HERE)
        const chatBox = document.getElementById('chatBox');
        const chatInput = document.getElementById('chatInput');
        const sendMessageButton = document.getElementById('sendMessageButton');

        // Adjust textarea height dynamically
        chatInput.addEventListener('input', () => {
            chatInput.style.height = 'auto';
            chatInput.style.height = (chatInput.scrollHeight) + 'px';
            if (chatInput.scrollHeight > chatInput.clientHeight) {
                chatInput.style.overflowY = 'auto';
            } else {
                chatInput.style.overflowY = 'hidden';
            }
        });

        async function sendMessage() {
            const userMessage = chatInput.value.trim();
            if (userMessage === '') return;

            // Display user message
            appendMessage(userMessage, 'user');
            chatInput.value = '';
            chatInput.style.height = '40px'; // Reset height

            // Show loading indicator
            const loadingMessage = appendMessage('', 'ai', true); // Add empty AI bubble with loading

            // Scroll to bottom
            chatBox.scrollTop = chatBox.scrollHeight;

            try {
                // IMPORTANT: In a real application, replace this with a call to your backend server
                // The backend server will then use your actual Google AI Studio API key to call Gemini
                const response = await fetch('/api/chat', { // This is a placeholder endpoint
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({ message: userMessage }),
                });

                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }

                const data = await response.json();
                const aiResponse = data.reply || "عذرًا، حدث خطأ ما ولم أتمكن من معالجة طلبك.";
                
                // Replace loading with actual AI response
                loadingMessage.querySelector('.message-bubble').innerHTML = aiResponse;

            } catch (error) {
                console.error("Error sending message to AI:", error);
                // Replace loading with error message
                loadingMessage.querySelector('.message-bubble').innerHTML = "عذرًا، حدث خطأ أثناء الاتصال بالخادم.";
            } finally {
                // Ensure loading indicator is removed
                if (loadingMessage && loadingMessage.querySelector('.loading-dots')) {
                    loadingMessage.querySelector('.loading-dots').remove();
                }
                chatBox.scrollTop = chatBox.scrollHeight; // Scroll again after message
            }
        }

        function appendMessage(text, sender, isLoading = false) {
            const messageDiv = document.createElement('div');
            messageDiv.classList.add('chat-message', sender);

            const bubbleDiv = document.createElement('div');
            bubbleDiv.classList.add('message-bubble', sender);
            bubbleDiv.innerHTML = text; // Use innerHTML to allow for potential formatting (e.g., bold)

            if (isLoading) {
                bubbleDiv.innerHTML = `
                    <div class="loading-dots">
                        <span></span>
                        <span></span>
                        <span></span>
                    </div>
                `;
            }

            messageDiv.appendChild(bubbleDiv);
            chatBox.appendChild(messageDiv);
            chatBox.scrollTop = chatBox.scrollHeight;
            return messageDiv; // Return the message element to modify it later (e.g., remove loading)
        }

        sendMessageButton.addEventListener('click', sendMessage);
        chatInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) { // Send on Enter, allow Shift+Enter for new line
                e.preventDefault();
                sendMessage();
            }
        });
    </script>
</body>
</html>
