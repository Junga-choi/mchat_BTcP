<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Digital Partner: PMR 검토 챗봇</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
            padding: 20px;
            box-sizing: border-box;
        }
        .chat-container {
            background-color: #ffffff;
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 800px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        .chat-header {
            background-color: #4a90e2;
            color: white;
            padding: 16px 24px;
            font-size: 1.5rem;
            font-weight: 600;
            border-top-left-radius: 12px;
            border-top-right-radius: 12px;
            text-align: center;
        }
        .chat-messages {
            flex-grow: 1;
            padding: 24px;
            overflow-y: auto;
            max-height: 600px; /* Limit height for scrollability */
            display: flex;
            flex-direction: column;
            gap: 16px;
        }
        .message-bubble {
            max-width: 80%;
            padding: 12px 18px;
            border-radius: 18px;
            word-wrap: break-word;
        }
        .user-message {
            background-color: #e2f0ff;
            align-self: flex-end;
            border-bottom-right-radius: 4px;
        }
        .bot-message {
            background-color: #f1f1f1;
            align-self: flex-start;
            border-bottom-left-radius: 4px;
        }
        .chat-input-area {
            display: flex;
            padding: 16px 24px;
            border-top: 1px solid #e0e0e0;
            gap: 10px;
        }
        .chat-input {
            flex-grow: 1;
            padding: 12px 16px;
            border: 1px solid #ccc;
            border-radius: 25px;
            font-size: 1rem;
            outline: none;
        }
        .send-button {
            background-color: #4a90e2;
            color: white;
            padding: 12px 20px;
            border-radius: 25px;
            border: none;
            cursor: pointer;
            font-size: 1rem;
            font-weight: 500;
            transition: background-color 0.3s ease;
        }
        .send-button:hover {
            background-color: #357ABD;
        }
        .starter-buttons {
            display: flex;
            flex-direction: column;
            gap: 10px;
            padding: 24px;
            border-top: 1px solid #e0e0e0;
        }
        .starter-button {
            background-color: #f0f8ff;
            color: #4a90e2;
            padding: 12px 18px;
            border-radius: 8px;
            border: 1px solid #a8d4ff;
            cursor: pointer;
            font-size: 1rem;
            text-align: left;
            transition: background-color 0.2s ease, border-color 0.2s ease;
        }
        .starter-button:hover {
            background-color: #e0efff;
            border-color: #7abeff;
        }
        .risk-high {
            color: #dc2626; /* Red-700 */
            font-weight: 600;
        }
        .risk-medium {
            color: #f59e0b; /* Amber-500 */
            font-weight: 600;
        }
        .risk-low {
            color: #16a34a; /* Green-600 */
            font-weight: 600;
        }
        .loading-indicator {
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 10px;
        }
        .loading-indicator span {
            display: inline-block;
            width: 10px;
            height: 10px;
            background-color: #4a90e2;
            border-radius: 50%;
            margin: 0 4px;
            animation: bounce 0.6s infinite alternate;
        }
        .loading-indicator span:nth-child(2) {
            animation-delay: 0.2s;
        }
        .loading-indicator span:nth-child(3) {
            animation-delay: 0.4s;
        }
        @keyframes bounce {
            from {
                transform: translateY(0);
            }
            to {
                transform: translateY(-10px);
            }
        }
        @media (max-width: 768px) {
            .chat-container {
                margin: 10px;
            }
            .chat-header {
                font-size: 1.2rem;
                padding: 12px 16px;
            }
            .chat-messages {
                padding: 16px;
            }
            .chat-input-area {
                flex-direction: column;
                padding: 12px 16px;
            }
            .send-button {
                width: 100%;
                padding: 10px 15px;
            }
            .starter-buttons {
                padding: 16px;
            }
            .starter-button {
                padding: 10px 15px;
                font-size: 0.9rem;
            }
        }
    </style>
</head>
<body>
    <div class="chat-container">
        <div class="chat-header">
            Digital Partner: PMR 검토 챗봇
        </div>
        <div class="chat-messages" id="chatMessages">
            <!-- Initial bot message -->
            <div class="message-bubble bot-message">
                안녕하세요, MPH 담당자님. 저는 한국메나리니 Digital팀의 PMR 사전 검토 챗봇입니다. 문구 작성이 막막하실 때, 제가 함께 검토해드릴게요. 😊
            </div>
        </div>
        <div class="loading-indicator hidden" id="loadingIndicator">
            <span></span><span></span><span></span>
        </div>
        <div class="starter-buttons" id="starterButtons">
            <button class="starter-button" onclick="handleStarterClick('1')">
                1️⃣ PMR 전체 검토 요청<br>
                <span class="text-sm text-gray-600">- 전체 자료를 업로드하시면, 주요 claim과 표현들을 정리해드리고 위험도도 함께 평가해드려요.</span>
            </button>
            <button class="starter-button" onclick="handleStarterClick('2')">
                2️⃣ Claim 위험도 평가 받기 ✨<br>
                <span class="text-sm text-gray-600">- 사용하고 싶은 문구 한 줄만 입력해주세요. 법적 리스크 수준과 대체 표현을 함께 안내해드립니다.</span>
            </button>
            <button class="starter-button" onclick="handleStarterClick('3')">
                3️⃣ 허용 가능한 표현 예시 보기<br>
                <span class="text-sm text-gray-600">- 제품명이나 효능을 알려주시면, 사전 승인된 표현 예시를 보여드릴게요.</span>
            </button>
            <button class="starter-button" onclick="handleStarterClick('4')">
                4️⃣ 규정/가이드라인 확인하기 ✨<br>
                <span class="text-sm text-gray-600">- 약사법, 마약류관리법, 식약처 가이드라인 등 핵심 규정을 쉽게 요약해드립니다.</span>
            </button>
        </div>
        <div class="chat-input-area">
            <input type="text" id="userInput" class="chat-input" placeholder="메시지를 입력하세요...">
            <button id="sendButton" class="send-button" onclick="sendMessage()">전송</button>
        </div>
    </div>

    <script type="module">
        // Import necessary Firebase modules
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, collection, query, onSnapshot, orderBy, limit } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Firebase configuration and initialization
        let app;
        let db;
        let auth;
        let userId = 'anonymous'; // Default to anonymous
        let isAuthReady = false;

        // Ensure __app_id and __firebase_config are defined
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};

        // Initialize Firebase and set up auth listener
        if (Object.keys(firebaseConfig).length > 0) {
            app = initializeApp(firebaseConfig);
            db = getFirestore(app);
            auth = getAuth(app);

            onAuthStateChanged(auth, async (user) => {
                if (user) {
                    userId = user.uid;
                    console.log("Authenticated user ID:", userId);
                } else {
                    try {
                        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                            await signInWithCustomToken(auth, __initial_auth_token);
                            userId = auth.currentUser.uid;
                            console.log("Signed in with custom token. User ID:", userId);
                        } else {
                            await signInAnonymously(auth);
                            userId = auth.currentUser.uid;
                            console.log("Signed in anonymously. User ID:", userId);
                        }
                    } catch (error) {
                        console.error("Firebase authentication error:", error);
                        // Fallback to a random UUID if anonymous sign-in also fails
                        userId = crypto.randomUUID();
                        console.log("Using random UUID as user ID due to auth failure:", userId);
                    }
                }
                isAuthReady = true;
                // Once auth is ready, set up Firestore listener for chat history
                setupChatHistoryListener();
            });
        } else {
            console.warn("Firebase config not provided. Running without Firebase features.");
            userId = crypto.randomUUID(); // Use a random UUID if Firebase is not configured
            isAuthReady = true;
        }

        const chatMessages = document.getElementById('chatMessages');
        const userInput = document.getElementById('userInput');
        const sendButton = document.getElementById('sendButton');
        const starterButtons = document.getElementById('starterButtons');
        const loadingIndicator = document.getElementById('loadingIndicator');

        // Function to add a message to the chat display
        function addMessage(text, sender) {
            const messageBubble = document.createElement('div');
            messageBubble.classList.add('message-bubble');
            if (sender === 'user') {
                messageBubble.classList.add('user-message');
            } else {
                messageBubble.classList.add('bot-message');
            }
            messageBubble.innerHTML = text; // Use innerHTML to render line breaks
            chatMessages.appendChild(messageBubble);
            chatMessages.scrollTop = chatMessages.scrollHeight; // Scroll to bottom
        }

        // Function to show/hide loading indicator
        function showLoading(show) {
            if (show) {
                loadingIndicator.classList.remove('hidden');
                sendButton.disabled = true;
                userInput.disabled = true;
            } else {
                loadingIndicator.classList.add('hidden');
                sendButton.disabled = false;
                userInput.disabled = false;
            }
        }

        // LLM API 호출 함수
        async function callGeminiAPI(prompt, schema = null) {
            let chatHistory = [{ role: "user", parts: [{ text: prompt }] }];
            const payload = { contents: chatHistory };

            if (schema) {
                payload.generationConfig = {
                    responseMimeType: "application/json",
                    responseSchema: schema
                };
            }

            // IMPORTANT: Replace "YOUR_GEMINI_API_KEY" with your actual Gemini API Key
            // For production, consider more secure ways to manage API keys (e.g., server-side proxy)
            const apiKey = "YOUR_GEMINI_API_KEY"; 
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                const result = await response.json();

                if (result.candidates && result.candidates.length > 0 &&
                    result.candidates[0].content && result.candidates[0].content.parts &&
                    result.candidates[0].content.parts.length > 0) {
                    const text = result.candidates[0].content.parts[0].text;
                    return schema ? JSON.parse(text) : text;
                } else {
                    console.error("Gemini API response structure unexpected:", result);
                    return "죄송합니다. 응답을 생성하는 데 문제가 발생했습니다.";
                }
            } catch (error) {
                console.error("Error calling Gemini API:", error);
                return "죄송합니다. API 호출 중 오류가 발생했습니다.";
            }
        }

        // Pre-loaded relevant content from documents for LLM context
        const DOC_NARCOTICS_AD_RULES = `
            "제25조(마약 및 향정신성의약품의 광고기준) 법 제14조제3항에 따른 마약 및 향정신성의약품의 광고기준은 다음 각호와 같다. <개정 2020. 5. 22.>\n\n1. 마약 및 향정신성의약품의 명칭, 제조방법, 효능이나 효과에 관하여 허가를 받은 사항 외의 광고를 하여서는 아니된다.\n\n2. 마약 및 향정신성의약품의 효능이나 효과를 광고하는 때에는 다음 각목의 광고를 하여서는 아니된다.\n 가. 우수한 치료효과를 나타낸다는 등으로 그 사용결과를 표시 또는 암시하는 광고\n 나. 적응증상을 서술적 또는 위협적인 표현으로 표시 또는 암시하는 광고\n 다. 마약 및 향정신성의약품의 사용을 직접 또는 간접적으로 강요하는 광고\n\n3. 마약 및 향정신성의약품의 사용에 있어서 이를 오·남용하게 할 염려가 있는 표현의 광고를 하여서는 아니된다.\n\n4. 마약 및 향정신성의약품에 관하여 의사 치과의사 수의사 또는 약사나 기타의 자가 이를 지정·공인·추천 지도 또는 신용하고 있다는 등의 광고를 하여서는 아니된다.\n\n5. 의사·치과의사 수의사 또는 약사가 마약 및 향정신성의약품의 제조방법, 효능이나 효과 등에 관하여 연구 또는 발견한 사실에 대하여 의학 또는 약학상 공인된 사항 이외의 광고를 하여서는 아니된다.\n\n6. 마약 및 향정신성의약품에 관하여 그 사용자의 감사장이나 체험기를 이용하거나 구입 주문쇄도 기타 이와 유사한 뜻을 표현하는 광고를 하여서는 아니된다.\n\n7. 마약 및 향정신성의약품의 제조방법, 효능이나 효과 등에 관하여 광고에 문헌을 인용하는 경우에는 의학 또는 약학상 인정된 문헌에 한하여 인용하되, 인용문헌의 본뜻을 정확하게 전하여야 하며 연구자의 성명, 문헌명과 발표연월일을 명시하여야.\n\n8. 마약 및 향정신성의약품을 광고할 때에는 다른 의약품·마약 또는 향정신성의약품을 비방하거나 비난한다고 의심되는 광고 또는 외국제품과 유사하다거나 보다 우수하다는 내용 등의 모호한 표현의 광고를 하여서는 아니된다.\n\n9. 마약 및 향정신성의약품의 부작용을 부정하는 표현 또는 부당하게 안전성을 강조하는 표현의 광고를 하여서는 아니된다.\n\n10. 마약 및 향정신성의약품을 판매하는 때에는 사은품 또는 현상품을 제공하거나 마약 및 향정신성의약품을 상품으로 제공하는 방법에 의한 광고를 하여서는 아니된다."
        `;

        const DOC_PHARMACIST_ACT_68 = `
            "제68조(과장광고 등의 금지) ① 의약품등의 명칭·제조방법·효능이나 성능에 관하여 거짓광고 또는 과장광고를 하지 못한다.\n\n②의약품등은 그 효능이나 성능에 관하여 의사 치과의사 한의사 수의사 또는 그 밖의 자가 보증한 것으로 오해할 염려가 있는 기사를 사용하지 못한다.\n\n③의약품등은 그 효능이나 성능을 암시하는 기사·사진·도안, 그 밖의 암시적 방법을 사용하여 광고하지 못한다.\n\n④의약품에 관하여 낙태를 암시하는 문서나 도안은 사용하지 못한다.\n\n⑤ 제31조제2항부터 제4항까지 및 제9항 또는 제42조제1항에 따른 허가·변경허가를 받거나 신고·변경신고를 한 후가 아니면 의약품등의 명칭·제조 방법·효능이나 성능에 관하여 광고하지 못한다. <개정 2007. 10. 17., 2018. 12. 11.>\n\n⑥ 다음 각 호의 어느 하나에 해당하는 의약품을 광고하여서는 아니 된다. 다만, 「감염병의 예방 및 관리에 관한 법률」 제2조제2호부터 제12호까지의 감염병의 예방용 의약품을 광고하는 경우와 의학 약학에 관한 전문가 등을 대상으로 하는 의약전문매체에 광고하는 경우 등 총리령으로 정하는 경우에는 그러하지 아니하다. <신설 2017. 10. 24.>\n\n1. 전문의약품\n\n2. 전문의약품과 제형, 투여 경로 및 단위제형당 주성분의 함량이 같은 일반의약품\n\n3. 원료의약품\n\n⑦의약품등의 광고 방법과 그 밖에 필요한 사항은 총리령으로 정한다. <개정 2008. 2. 29., 2010. 1. 18., 2013. 3. 23., 2017. 10. 24.>"
        `;

        const DOC_MFDS_GUIDELINE = `
            "II. 의약품광고 가이드라인\n\n1. 거짓·과장광고\n(1) 의약품 품목허가를 받거나 신고한 사항과 다른 내용의 광고나 과학적·객관적으로 사실여부를 확인·증명할 수 없는 거짓·과장 광고는 소비자가 오인·혼동하여 의약품을 오남용할 우려가 있어 못한다는 취지이다.\n\n2. 의약전문가 추천광고\n(1) 의약전문가 추천광고는 의료인에 대한 사회적 인식의 특성상 소비자로 하여금 의약품에 대한 인식에 미칠 영향이 크다는 것을 감안할 때 오남용을 유발할 우려가 있어 이를 방지하고자 제한한다는 취지이다.\n\n3. 근거문헌 인용 광고\n(2) 품목허가증에 명시되지 않은 제품의 특징, 약리기전, 효능이나 성능을 광고하는 경우 근거문헌 등의 인용을 통해 공인된 객관적 내용임을 입증할 수 있어야 한다.\n\n4. 소비자 오인 우려가 있는 광고\n(1) 광고가 일반 소비자를 대상으로 하는 경우 소비자를 오인시킬 우려가 있는지 여부는 전문가가 아닌 보통의 주의력을 가진 일반 소비자가 당해 광고를 받아 들이는 전체적·궁극적 인상을 기준으로 하여 객관적으로 판단되어야 한다.\n\n10. 보증 및 최고·최상 절대적 표현 광고\n(1) 자사제품이 경쟁사업자의 것보다 현저히 우량 또는 유리하다고 나타내기 위한 배타성을 띤 절대적 표현의 용어를 사용하여 소비자를 오인시킬 우려가 있는 광고행위는 부적절하다는 취지이다.\n\n11. 안전성을 강조하는 광고\n(1) 모든 의약품은 과학적으로 부작용이 발생할 수 있으므로, 부작용이 없다거나 안전성을 보증하는 듯한 표현 등으로 안전성에 대한 사실에 반하는 인식을 주거나 소비자를 오인시킬 우려가 있는 광고는 할 수 없다.\n\nIII. 전문의약품 정보제공 가이드라인\n1. 전문의약품 광고 제한적 허용\n(1) 전문의약품은 의사 또는 치과의사의 전문적인 지식에 따라 사용하는 특성을 고려할 때, 오·남용 우려, 광고비용의 소비자 부담증가 등을 고려하여 대중광고를 금지하고 있으며, 접종률을 고려한 예방용 의약품(예: 독감백신) 및 의학 약학 전문가 대상에 한하여 제한적으로 허용하고 있다."
        `;

        const DOC_KRPIA_GUIDELINE = `
            "제3조 (회원사의 기본의무 및 가이드라인의 해석)\n② 본 가이드라인은 전문의약품 표시·광고에 적용되는 다른 법령, 기준(식품의약품안전처에서 발간한 의약품광고 및 전문의약품 정보제공 가이드라인과 같은 규제기관의 행정해석 등을 의미한다)과 중첩적으로 적용되며, 상호간 차이가 있는 경우 회원사는 상위법 우선 원칙에 저촉하지 않는 범위 내에서 가장 엄격한 내용을 따르기 위하여 노력하여야 한다.\n\n제6조 (미허가 품목 및 미허가 효능·효과 등)\n품목허가를 받지 않은 의약품에 대한 표시, 광고 및 판촉활동은 금지되며, 품목허가를 받은 의약품이라도 허가 받지 않은 명칭·품질·제조방법 용법·용량·효능·효과 또는 성능 등에 관한 표시, 광고 및 판촉활동은 허용되지 아니한다.\n\n제7조 (판촉자료 내용의 사실성)\n① 모든 판촉자료의 내용은 허위나 과장이 없어야 하며, 식품의약품안전처장이 인정하는 공정서 및 의약품집에 실려 있는 내용이나 의·약학적으로 공인된 범위의 임상결과 등 과학적으로 입증된 최신 증거에 근거하여 공인된 객관적 내용임을 입증해야 한다.\n② 근거 문헌을 인용하는 경우에는 출처를 정확히 알 수 있도록 반드시 그 자료의 출처 (예: 연구자의 성명, 문헌명과 발표 연월일 등)를 구체적으로 명기하여야 한다.\n\n제8조 (오인 및 오해성 광고금지)\n① 모든 판촉자료의 내용은 최신의 것이어야 하고, 편중되지 않아야 하며, 잘못 해석될 여지가 있는 애매한 표현을 사용하거나, 인용하는 내용이 직접 또는 간접적으로 생략되어 소비자로 하여금 전체적·궁극적으로 오해를 유발하여서는 아니 된다.\n③ 효능이나 성능에 관하여 객관적 근거 없이 보건의료전문가가 이를 인증한 것으로 오인할 우려가 있는 광고를 하여서는 아니 된다.\n\n제10조 (비교 기술)\n① 의약품의 특징 등에 관하여 비교 기술할 때는 그것이 사실에 근거한 것이고 공정하고 객관적인 확인 절차를 거친 것이어야 하며 식품의약품안전처장이 인정하는 공정서 및 의약품집에 실려 있는 내용이나 의약학적으로 공인된 범위의 임상결과 등 충분한 근거문헌에 의하여 입증될 수 있어야 한다.\n② 비교대상 의약품은 유효 성분의 일반명으로 하되, 유효 성분의 일반명이 동일하여 일반명만으로는 비교가 어려운 제네릭 의약품 또는 동등생물 의약품 등의 경우에도 비교 기술할 때 제품명이 드러나지 않아야 하며, 익명 처리 (예: 일반명 A, 일반명 B)하여 비교 기술 한다."
        `;

        const DOC_MENARINI_SOP = `
            "6.1. ALL INFORMATION IN A SCIENTIFIC PROMOTIONAL MATERIAL MUST:\n\nA. Be in adherence to local industry codes and regulatory requirements;\n\nB. Be consistent with the particulars listed in the last updated SmPC/PI (Summary of Product characteristics/Prescribing Information);\n\nC. Be based on an up-to-date evaluation of all relevant evidence and reflect that evidence clearly. It must not mislead by distortion, exaggeration, undue emphasis, omission or in any other way;\n\nD. Be accurate, balanced, fair, objective and sufficiently complete to enable the recipient to form his/her own opinion of the therapeutic value of the medicinal product;\n\nE. Be capable of substantiation by published articles, which must be promptly provided in response to requests from healthcare professionals (archive all bibliographic references);\n\nF. Contain clearly indicated references to published studies;\n\nG. Publication abstracts published in abstract books of national and international congresses and posters presented at congresses may be used in promotional materials, as long as they are in line with the SmPC and are not older than two years (date of congress being day zero);\n\nH. Contain only faithfully reproduced quotations from medical/scientific literature. The precise sources must be identified;\n\nI. Faithfully reproduce any artwork (graphs, illustrations, photographs and tables) taken from published studies. Precise source(s) should be clearly indicated. Where adaptation or modifications are required (e.g. columns or graphs from tables), it must be clearly stated that the artwork has been adapted and/or modified;\n\nJ. Include study description when presenting graphs or illustrations taken from published studies. At a minimum the following study information should be included; number of subjects, population studied, dose of drug and duration of treatment, study design, primary endpoints, statistical significance, summary of safety;\n\nK. Not contain misleading artwork (for example by using incomplete or statistically irrelevant information or unusual scales);\n\nL. Not contain hanging comparatives (claims that a product is merely "better or stronger");\n\nM. Not contain superlatives (e.g. best, fastest) unless based on substantial scientific evidence;\n\nN. Base any comparison made between different medicinal products on relevant and comparable aspects of the products which should be balanced and reflective of the body of clinical evidence. Comparative advertising must not be misleading or disparaging;\n\nO. Use the word "new" to describe a medicinal product or therapeutic indication only in the first year after market entry;\n\nP. Contain balanced safety information about side-effects that reflects available evidence and are consistent with SmPC; the word 'safe' must not be used, however balanced statements such as 'favourable safety profile' are permitted if supporting evidence can substantiate this claim."
        `;

        const DOC_LEEKO_OPINION_20250414 = `
            "마약성 진통제 광고 관련 규정\n펜타닐 성분의 마약성 진통제는 마약류 관리에 관한 법률(이하 '마약류관리법') 제2조2호의 '마약'에 해당합니다. 마약류관리법 제3조제 12 호는 일반행위 금지 규정으로서 『누구든지 마약류의 취급 등과 관한 정보를 표시·광고의 공정화에 관한 법률」(이하 '표시광고법) 제2조제 2 호에서 정하는 방법으로 타인에게 널리 알리거나 제시하는 행위』를 금지하고 있습니다. 다만, 이 일반행위 금지규정 예외로서 동법 제14조제1항에 의해 마약류 취급자에 한하여 품목허가를 받은 마약 및 향정신성의약품에 대한 광고가 제한적으로 허용』 됩니다.\n\n마약류관리법 제14조제1항은 마약류제조업자 및 수출입업자는 품목허가를 받은 마약을 의학 약학 수의학에 관한 전문가 등을 대상으로 하는 매체 또는 수단에 의한 경우에 한정하여 광고할 수 있다고 하면서, 동조 제2항을 통해 이 경우 허용되는 매체 또는 수단을 '의약약학 수의학에 관한 사항을 전문적으로 취급하는 신문 또는 잡지', '설명내용에 부작용 등 사용시 주의사항에 관한 정보가 포함된 제품설명회'에 한정되는 것으로 정하고 있습니다. 따라서 마약성 진통제에 대한 광고는 의학 약학 수의학에 관한 전문가 등을 대상으로 특정 매체를 통해서만 가능합니다.\n\n만일 마약류관리법 제 14 조를 위반하여 마약을 광고한 경우, 마약류관리법 제44조제1항제1호 카목 및 동법 시행규칙 제43조 [별표 2] 행정처분 기준에 따라, 1차 위반시 1개월의 영업정지 처분을 받을 수 있으며, 동법 제64조 제10호에 의해 1년 이하의 징역 또는 1천만원 이하의 벌금에 처해질 수 있습니다.\n\n마약류관리법상 마약에 해당하는 마약성 진통제는 마약류관리법이 우선적으로 적용되나, 약사법상 전문의약품의 광고 제한 규정과 마약류관리법상 마약 및 향정신성의약품의 광고 제한 규정이 일부 유사하고 사용되는 용어가 동일하므로, 약사법상 의약품 광고 범위에 관한 규정 해석 및 의약품 광고 가이드라인은 마약류관리법 제 14 조의 해석에 참고가 될 수 있을 것으로 사료됩니다."

            "마약류관리법상 '광고'로 해석되는 내용의 범위\n이처럼 마약류관리법상 '마약'에 대한 광고는 그 대상, 매체 및 수단에 대하여 제한적 규정이 존재합니다. 이에 따라, '마약'과 관련된 정보 제공 행위가 모두 광고로 해석되어 이러한 제한이 적용되는지, 그렇지 않다면 어느 범위까지 광고로 간주되어 제한이 적용되는지를 검토할 필요가 있습니다. 이하에서 살펴봅니다.\n\n(1) 마약류관리법 제3조 제12호의 일반행위 금지규정이 인용하는 「표시·광고의 공정화에 관한 법률」(이하 '표시광고법') 제2조 제2호는 '광고'를 "사업자 등이 상품 등에 관한 자기 또는 다른 사업자 등에 관한 사항, 또는 자기 또는 다른 사업자 등의 상품 등의 내용, 거래 조건, 그 밖에 그 거래에 관한 사항을 특정 매체 또는 수단을 통해 소비자에게 널리 알리거나 제시하는 행위"로 정의하고 있습니다.\n\n(3) 이를 종합하면, 특정 의약품인 '앱스트랄'에 대한 정보를 직접적으로 제공하는 행위는, 그 자체로 의약품 광고에 해당할 가능성이 높은 것으로 판단됩니다.\n\n반면, '앱스트랄'이라는 제품명을 언급하지 않고, 돌발성 암성 통증의 관리에 관한 객관적이고 중립적인 의약학적 정보를 제공하는 수준의 질환 정보 제공 활동은 광고로 판단될 가능성이 상대적으로 낮은 것으로 보입니다.\n\n다만, 이 경우에도 해당 자료의 전체적인 문맥, 서술 방식 및 정보 구성 등을 고려하였을 때, 일반 수요자가 '앱스트랄'이라는 특정 제품을 자연스럽게 유추할 수 있고, 결과적으로 해당 제품에 대한 인식을 제고하거나 사용을 유도하는 효과를 초래할 수 있는 경우, 실질적으로는 '앱스트랄'에 관한 광고에 해당한다고 판단될 여지가 존재합니다."

            "의료진 대상 교육자료의 형태 및 배포 채널에 따른 허용 범위\n(3) 2018 년 식약처의 유권해석과 마약류관리법 제14조의 개정 경위 및 입법과정에서의 논의 내용을 종합적으로 고려할 때, 마약성 진통제를 포함한 마약류에 대한 광고는 '의학·약학·수의학에 관한 사항을 전문적으로 취급하는 신문 또는 잡지' 또는 '부작용 등 사용상 주의사항을 포함한 제품설명회'라는 두 가지 수단으로만 엄격히 제한되고, 전단 팸플릿 등 그 외의 매체 또는 수단은 허용되지 않는다고 보는 것이 타당합니다.\n\n그러므로 '앱스트랄'의 광고로 판단될 수 있는 내용을 포함하는 자료를 핸드북, PDF, QR 등 전자 형태 또는 책받침 형태로 의료진에게 배포하는 것은, 그 배포의 채널이 학회 부스 또는 회사 주최 심포지엄에서의 배포, 영업사원을 통하여 의료진에 대한 직접 배포, 퀴즈 이벤트를 통한 배포인지 여부를 불문하고, 마약류관리법 제 14조제2항에서 정한 매체 또는 수단 이외의 방식으로써 마약류관리법 제14조제2항 위반에 해당될 가능성이 있다고 판단됩니다."

            "환자 및 일반인을 대상으로 하는 교육자료 배포 가능 여부 및 방법\n앞서 본 바와 같이 마약류관리법 제14조제1항 및 제2항에 따라 마약의 광고는 의약학 전문가를 대상으로만 제한적 매체 또는 수단을 통해서 가능합니다. 따라서 '앱스트랄'의 광고에 해당하는 내용을 포함하는 교육자료를 어떠한 방식으로도 환자 및 일반인에게 배포하는 것은 허용되지 않습니다."
        `;

        const DOC_LEEKO_EMAIL_OPINION = `
            "1. 의료진용 교육자료 (앱스트랄의 제품 정보가 제외된 경우)를 활용한 기부 활동 가능여부\n[LK] 퀴즈를 맞춘 의료진 대신 기부하는 것이 아니라 퀴즈 참가자수에 따라 기부금을 결정할 경우 경제적 이익 제공 의 주체를 특정할 수 없고, 앱스트랄의 제품 정보가 제외된 의료진용 교육자료의 내용은 질환정보의 제공으로써 이를 이용한 의료진 대상 캠페인 등의 활동은 판매 촉진 활동으로 판단될 가능성은 낮을 것으로 사료되는바, 말씀하신 방식의 기부는 약사법상 금지되는 경제적 이익제공으로 판단되지 않을 가능성이 높을 것으로 보입니다.\n\n2. 의료진용 '돌발성 암성 통증 진단 알고리즘'\n[LK] 귀사가 제공하는 의료진용 돌발성 암성 통증 평가 알고리즘이 앱스트랄에 대한 광고로 판단될 여지를 줄이기 위해서는 의견서에서 말씀드린 바와 같이 그 내용이 객관적으로 공인된 자료에 의해 뒷받침될 뿐만 아니라 균형적인 정보를 포함하고 있어야 합니다. 만약 신뢰할 만한 근거에 의해 뒷받침되더라도 앱스트랄이 포함된 속효성 마약성 진 통제, 그 중 경점막 펜타닐 제제에 관한 정보에만 집중하는 경우 앱스트랄에 대한 정보제공으로 평가될 여지가 있다 고 사료됩니다.\n\n3. 환자/보호자용 교육자료\n[LK] 식약처의 의약품광고 및 전문의약품 정보제공 가이드라인(2022. 11.)(이하 '의약품 광고 가이드라인'에 따르면, 식약처의 위해성 관리 계획에 따라 적정하게 마련된 환자용 설명서 및 관련 프로그램은 일반적으로 광고로 사료되지 않습니다. 그러나 복약 지도에 도움이 되는 환자용 사용설명서와 관련된 전문의약품 정보제공은, 약사법 제68조 제6 항 단서조항에 따라 의학·약학에 관한 전문가 등을 대상으로 하는 의약 전문매체에 광고하거나 의학·약학에 관한 전 문가 등을 대상으로 하는 학술적 성격의 매체 또는 수단을 이용하여 광고하는 것으로 보며, 전문의약품의 복약지도는 의·약사 등 전문가가 해당의약품을 처방받은 환자에게 개별적으로 시행하여야 하므로, 제작자는 추가 정보제공의 내 용 및 배부방식을 해당 의약품을 취급하는 의약사가 사전에 인지할 수 있도록 조치(예: 해당 자료는 의약사를 통해 직 접 제공하거나, 완제품의 용기나 포장 안에 포함되어 있는 경우는 해당 사실을 공급업소에 알려 의약사가 사전에 알 수 있도록 함)할 것이 권장됩니다.\n\n따라서, 약사법령 및 식약처에 의해 대상이 지정되고 계획과 이행에 관하여 식약처 보고가 필요한 RMP에 따라 마련 된 환자용 사용설명서의 제공과, RMP 대상이 아닌 전문의약품의 환자용 사용설명서 제공은 다르게 취급되어야 할 것으로 생각됩니다. 이에 비추어 볼 때 RMP의 일환인 환자용 사용설명서 제공이 의약품 광고로 판단되지 않는다는 기준을 RMP 대상이 아닌 앱스트랄에 그대로 적용하는 것에는 리스크가 있을 것으로 사료되는바, 기존 의견서의 내 용과 마찬가지로 환자용 복약설명서 제공은 지양하시는 것이 보다 안전할 것으로 판단됩니다."
        `;


        // Function to send a message (user input or starter button)
        async function sendMessage(messageText = null) {
            let text = messageText || userInput.value.trim();
            if (!text) return;

            addMessage(text, 'user');
            userInput.value = '';
            starterButtons.style.display = 'none'; // Hide starter buttons after first interaction
            showLoading(true);

            // Save user message to Firestore
            if (isAuthReady && db) {
                try {
                    const messagesCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/messages`);
                    await setDoc(doc(messagesCollectionRef), {
                        text: text,
                        sender: 'user',
                        timestamp: new Date()
                    });
                } catch (e) {
                    console.error("Error adding user document: ", e);
                }
            }

            let botResponse = '';
            let currentMode = ''; // To track if we are in a specific query mode

            // Check if the user is asking for a claim risk assessment
            if (text === '2' || text.includes('Claim 위험도 평가')) {
                botResponse = `
                    MPH 담당자님, 문구 위험도 평가를 요청하셨군요! 검토하고 싶은 문구 한 줄을 입력해 주세요. 제가 법적 리스크 수준과 대체 표현을 함께 안내해 드릴게요.
                    <br><br>
                    예시: "앱스트랄은 중독 걱정 없이 빠르게 통증을 없애줍니다."
                `;
                currentMode = 'claim_assessment_request';
            } else if (text === '4' || text.includes('규정/가이드라인 확인')) {
                botResponse = `
                    MPH 담당자님, 규정/가이드라인 확인을 요청하셨군요! 어떤 법령이나 가이드라인에 대해 궁금하신가요? 약사법, 마약류관리법, 식약처 가이드라인, KRPIA 가이드라인, 사내 SOP 중 궁금한 내용을 구체적으로 말씀해 주세요.
                    <br><br>
                    예시: "마약류관리법 시행규칙 제25조 요약해줘" 또는 "KRPIA 가이드라인에서 비교 광고에 대해 알려줘"
                `;
                currentMode = 'guideline_summary_request';
            } else if (text === '1' || text.includes('PMR 전체 검토')) {
                botResponse = `
                    MPH 담당자님, PMR 전체 검토를 요청하셨군요! 현재는 텍스트 업로드 기능이 구현되어 있지 않지만, 향후 이 기능을 추가하여 전체 자료를 분석하고 주요 클레임을 자동으로 추출, 위험도를 평가해 드릴 수 있도록 개발하겠습니다.
                    <br><br>
                    지금은 문구 한 줄을 입력하시면 위험도를 평가해 드릴 수 있습니다. 어떤 문구를 검토해 드릴까요?
                `;
            } else if (text.includes('앱스트랄')) {
                 botResponse = `
                    MPH 담당자님, '앱스트랄'에 대한 허용 가능한 표현 예시를 요청하셨군요.
                    <br><br>
                    '앱스트랄'은 펜타닐 성분의 마약성 진통제이며, 돌발성 암성 통증 관리에 사용됩니다.
                    <br><br>
                    **허용 가능한 표현 예시:**
                    - "앱스트랄은 돌발성 암성 통증 완화를 위한 경점막 펜타닐 제제입니다." (SmPC 기반)
                    - "돌발성 암성 통증 환자에게 속효성 진통제가 권고되며, 앱스트랄은 이러한 상황에서 고려될 수 있습니다." (ESMO Clinical Practice Guidelines, 2018 등 공인된 문헌 인용 시)
                    - "앱스트랄의 용법·용량은 개별 환자의 통증 조절에 따라 적정되어야 합니다." (SmPC 기반)
                    <br><br>
                    **주의할 표현 (High Risk):
                    - "가장 빠른 효과를 보이는 앱스트랄" (최상급 표현)
                    - "중독 걱정 없는 앱스트랄" (오남용 암시, 안전성 과장)
                    - "의사도 추천하는 앱스트랄" (전문가 보증)
                    <br><br>
                    앱스트랄과 관련하여 더 구체적인 시나리오나 문구가 있으시면 말씀해주세요.
                `;
            }
            else if (text.includes('돌발성 암성 통증')) {
                botResponse = `
                    MPH 담당자님, '돌발성 암성 통증'에 대한 허용 가능한 표현 예시를 요청하셨군요.
                    <br><br>
                    돌발성 암성 통증은 암 환자에서 배경 통증이 조절되고 있음에도 불구하고 갑자기 발생하는 일시적인 통증 악화를 의미합니다.
                    <br><br>
                    **허용 가능한 표현 예시:**
                    - "돌발성 암성 통증은 예측 불가능하게 발생할 수 있으며, 환자의 삶의 질에 큰 영향을 미칩니다." (질환 정보 제공)
                    - "돌발성 암성 통증 관리를 위해 속효성 마약성 진통제가 사용될 수 있습니다." (일반적인 치료법 설명)
                    - "ESMO 가이드라인에 따르면, 돌발성 암성 통증에는 경점막 펜타닐 제제가 고려될 수 있습니다." (공인된 문헌 인용 시)
                    <br><br>
                    **주의할 표현 (High Risk):**
                    - "돌발성 암성 통증, 이제 앱스트랄로 완벽하게 해결하세요!" (과장 광고, 특정 제품 유도)
                    - "돌발성 암성 통증은 더 이상 고통스럽지 않습니다!" (부작용 부정, 과도한 안전성 강조)
                    <br><br>
                    더 궁금한 점이 있으시면 언제든지 말씀해주세요.
                `;
            } else if (text.includes('마약류관리법 시행규칙 제25조 요약') || text.includes('마약류관리법 시행규칙 제25조')) {
                const prompt = `다음 한국 법규의 핵심 내용을 요약해주세요. 마케팅 자료(PMR)와 관련된 의약품 광고 규제에 중점을 두세요.
                    \n\n법규 내용:\n${DOC_NARCOTICS_AD_RULES}`;
                botResponse = await callGeminiAPI(prompt);
            } else if (text.includes('약사법 제68조 요약') || text.includes('약사법 제68조')) {
                const prompt = `다음 한국 법규의 핵심 내용을 요약해주세요. 마케팅 자료(PMR)와 관련된 의약품 광고 규제에 중점을 두세요.
                    \n\n법규 내용:\n${DOC_PHARMACIST_ACT_68}`;
                botResponse = await callGeminiAPI(prompt);
            } else if (text.includes('식약처 가이드라인 요약') || text.includes('식약처 가이드라인')) {
                const prompt = `다음 한국 가이드라인의 핵심 내용을 요약해주세요. 마케팅 자료(PMR)와 관련된 의약품 광고 규제에 중점을 두세요.
                    \n\n가이드라인 내용:\n${DOC_MFDS_GUIDELINE}`;
                botResponse = await callGeminiAPI(prompt);
            } else if (text.includes('KRPIA 가이드라인 요약') || text.includes('KRPIA 가이드라인')) {
                const prompt = `다음 한국 가이드라인의 핵심 내용을 요약해주세요. 마케팅 자료(PMR)와 관련된 의약품 광고 규제에 중점을 두세요.
                    \n\n가이드라인 내용:\n${DOC_KRPIA_GUIDELINE}`;
                botResponse = await callGeminiAPI(prompt);
            } else if (text.includes('사내 SOP 요약') || text.includes('Menarini Regional SOP')) {
                const prompt = `다음 사내 SOP의 핵심 내용을 요약해주세요. 마케팅 자료(PMR)와 관련된 의약품 광고 규제에 중점을 두세요.
                    \n\nSOP 내용:\n${DOC_MENARINI_SOP}`;
                botResponse = await callGeminiAPI(prompt);
            } else if (text.includes('법무법인 광장 자문 의견서 요약') || text.includes('법무법인 광장')) {
                const prompt = `다음 법무법인 광장 자문 의견서의 핵심 내용을 요약해주세요. 마케팅 자료(PMR)와 관련된 의약품 광고 규제에 중점을 두세요. 특히 마약성 진통제 '앱스트랄'에 대한 광고 및 배포 제한에 대한 내용을 포함해주세요.
                    \n\n의견서 내용:\n${DOC_LEEKO_OPINION_20250414}\n${DOC_LEEKO_EMAIL_OPINION}`;
                botResponse = await callGeminiAPI(prompt);
            }
            else {
                // Default LLM call for claim assessment if no specific command is detected
                const claimAssessmentSchema = {
                    type: "OBJECT",
                    properties: {
                        claim: { "type": "STRING" },
                        problem: { "type": "STRING" },
                        legalBasis: { "type": "STRING" },
                        riskLevel: { "type": "STRING", "enum": ["High", "Medium", "Low"] },
                        maReviewNeeded: { "type": "BOOLEAN" },
                        alternativeSuggestion: { "type": "STRING" }
                    },
                    required: ["claim", "problem", "legalBasis", "riskLevel", "maReviewNeeded", "alternativeSuggestion"]
                };

                const prompt = `한국메나리니 마케팅팀(MPH 담당자)을 대상으로 하는 마약성 진통제(예: 앱스트랄, 팩펜트) 마케팅 문구에 대한 법적 위험도를 평가해주세요. 다음 한국의 의약품 광고 법규(약사법, 마약류관리법, 식약처 가이드라인, KRPIA 가이드라인, 메나리니 사내 SOP)를 기반으로 위험도 수준(High, Medium, Low)을 판단하고, 문제점, 법적 근거, MA 검토 필요 여부, 그리고 대체 문구를 제안해주세요. 응답은 한국어로 JSON 형식으로 제공해주세요.

                마케팅 문구: '${text}'

                다음 규정 및 자문 의견을 참고하세요:
                ---
                마약류관리법 시행규칙 제25조 (광고 기준):
                ${DOC_NARCOTICS_AD_RULES}
                ---
                약사법 제68조 (과장광고 등의 금지):
                ${DOC_PHARMACIST_ACT_68}
                ---
                식약처 의약품광고 및 전문의약품 정보제공 가이드라인 (2022) - 주요 광고 기준:
                ${DOC_MFDS_GUIDELINE}
                ---
                KRPIA 의약품 표시·광고 가이드라인 (2023) - 주요 광고 기준:
                ${DOC_KRPIA_GUIDELINE}
                ---
                Menarini Regional SOP RGSOP-MM-00801 (Section 6.1):
                ${DOC_MENARINI_SOP}
                ---
                법무법인 광장 자문 의견서 (2025.4.14) 및 추가 문의 건 이메일 답변:
                ${DOC_LEEKO_OPINION_20250414}
                ${DOC_LEEKO_EMAIL_OPINION}
                ---
                `;

                try {
                    const llmResponse = await callGeminiAPI(prompt, claimAssessmentSchema);
                    if (typeof llmResponse === 'object' && llmResponse.riskLevel) {
                        let riskClass = '';
                        let maReviewText = llmResponse.maReviewNeeded ? 'MA 집중 검토 필요' : '자동 승인 대상';
                        let riskEmoji = '';

                        switch (llmResponse.riskLevel) {
                            case 'High':
                                riskClass = 'risk-high';
                                riskEmoji = '🔴';
                                break;
                            case 'Medium':
                                riskClass = 'risk-medium';
                                riskEmoji = '🟠';
                                break;
                            case 'Low':
                                riskClass = 'risk-low';
                                riskEmoji = '🟢';
                                break;
                        }

                        botResponse = `
                            [검토결과]<br>
                            - 문구: "${llmResponse.claim}"<br>
                            - 문제점: ${llmResponse.problem}<br>
                            - 위반 근거: ${llmResponse.legalBasis}<br><br>
                            [RISK LEVEL]<br>
                            <span class="${riskClass}">${riskEmoji} ${llmResponse.riskLevel} Risk → ${maReviewText}</span><br><br>
                            [대체 제안]<br>
                            - ${llmResponse.alternativeSuggestion}
                        `;
                    } else {
                        botResponse = `죄송합니다. 문구 검토 결과를 가져오는 데 문제가 발생했습니다. 다시 시도해 주시거나 다른 질문을 해주세요.`;
                        console.error("LLM response was not in expected JSON format:", llmResponse);
                    }
                } catch (error) {
                    console.error("Error during LLM claim assessment:", error);
                    botResponse = `죄송합니다. 문구 검토 중 오류가 발생했습니다. 다시 시도해 주시거나 다른 질문을 해주세요.`;
                }
            }


            setTimeout(async () => {
                addMessage(botResponse, 'bot');
                showLoading(false);

                // Save bot message to Firestore
                if (isAuthReady && db) {
                    try {
                        const messagesCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/messages`);
                        await setDoc(doc(messagesCollectionRef), {
                            text: botResponse,
                            sender: 'bot',
                            timestamp: new Date()
                        });
                    } catch (e) {
                        console.error("Error adding bot document: ", e);
                    }
                }
            }, 1000); // Simulate network delay
        }

        // Handle starter button clicks
        function handleStarterClick(option) {
            let message = '';
            switch (option) {
                case '1':
                    message = '1️⃣ PMR 전체 검토 요청';
                    break;
                case '2':
                    message = '2️⃣ Claim 위험도 평가 받기';
                    break;
                case '3':
                    message = '3️⃣ 허용 가능한 표현 예시 보기';
                    break;
                case '4':
                    message = '4️⃣ 규정/가이드라인 확인하기';
                    break;
            }
            sendMessage(message);
        }

        // Listen for Enter key press in the input field
        userInput.addEventListener('keypress', function(event) {
            if (event.key === 'Enter') {
                sendMessage();
            }
        });

        // Setup Firestore listener for chat history
        function setupChatHistoryListener() {
            if (!db || !isAuthReady) {
                console.warn("Firestore not ready or user not authenticated for history listener.");
                return;
            }

            const messagesCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/messages`);
            const q = query(messagesCollectionRef, orderBy("timestamp", "asc"), limit(20)); // Limit to last 20 messages

            onSnapshot(q, (snapshot) => {
                // Clear existing messages before loading history to prevent duplicates
                chatMessages.innerHTML = '';
                // Add the initial bot message if it's the first load
                addMessage('안녕하세요, MPH 담당자님. 저는 한국메나리니 Digital팀의 PMR 사전 검토 챗봇입니다. 문구 작성이 막막하실 때, 제가 함께 검토해드릴게요. 😊', 'bot');

                snapshot.docChanges().forEach((change) => {
                    if (change.type === "added") {
                        const messageData = change.doc.data();
                        // Only add messages that are not the initial bot message
                        if (!(messageData.sender === 'bot' && messageData.text.includes('안녕하세요, MPH 담당자님.'))) {
                            addMessage(messageData.text, messageData.sender);
                        }
                    }
                });
                // After loading history, hide starter buttons if there's any user interaction
                if (snapshot.docs.some(doc => doc.data().sender === 'user')) {
                    starterButtons.style.display = 'none';
                } else {
                    starterButtons.style.display = 'flex'; // Show if no user messages
                }
            }, (error) => {
                console.error("Error listening to messages: ", error);
            });
        }

    </script>
</body>
</html>
