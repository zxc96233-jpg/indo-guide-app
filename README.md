<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background: #f0f2f5; font-family: system-ui, -apple-system, sans-serif; }
        .chat-container { height: calc(100vh - 160px); overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 15px; }
        .user-msg { background: #1d4ed8; color: white; align-self: flex-end; padding: 12px 18px; border-radius: 20px 20px 0 20px; max-width: 80%; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .ai-msg { background: white; color: #1f2937; align-self: flex-start; padding: 12px 18px; border-radius: 20px 20px 20px 0; max-width: 80%; border: 1px solid #e5e7eb; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="bg-blue-800 text-white p-5 text-center font-bold shadow-md">
        INDO GUIDE AI 🇮🇩
    </div>

    <div id="chat" class="chat-container">
        <div class="ai-msg">مرحباً بك! نظام Indo Guide AI جاهز الآن. اسألني عن السياحة أو العقارات في إندونيسيا.</div>
    </div>

    <div id="loading" class="hidden px-5 text-xs text-blue-600 font-bold animate-pulse">جاري الاتصال بـ Gemini...</div>

    <div class="p-4 bg-white border-t flex gap-2 shadow-inner">
        <input type="text" id="input" placeholder="اكتب سؤالك هنا..." class="flex-1 border-2 border-gray-200 rounded-full px-5 py-3 outline-none focus:border-blue-600 transition-all">
        <button onclick="sendRequest()" class="bg-blue-700 hover:bg-blue-800 text-white w-12 h-12 rounded-full flex items-center justify-center transition-transform active:scale-90">
            <svg viewBox="0 0 24 24" class="w-6 h-6 fill-current"><path d="M2.01 21L23 12 2.01 3 2 10l15 2-15 2z"></path></svg>
        </button>
    </div>

    <script>
        // مفتاحك الذي يظهر في الصور
        const KEY = "AlzaSyA1PNjdG8qGCaR_zoOcnzJQnUeS0zW5NBE"; 

        async function sendRequest() {
            const input = document.getElementById('input');
            const chat = document.getElementById('chat');
            const loading = document.getElementById('loading');
            const query = input.value.trim();

            if (!query) return;

            // إضافة رسالة المستخدم
            chat.innerHTML += `<div class="user-msg">${query}</div>`;
            input.value = "";
            loading.classList.remove('hidden');
            chat.scrollTop = chat.scrollHeight;

            try {
                // استخدام رابط v1 المباشر والأكثر استقراراً
                const url = `https://generativelanguage.googleapis.com/v1/models/gemini-1.5-flash:generateContent?key=${KEY}`;
                
                const response = await fetch(url, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{ parts: [{ text: "أنت خبير سياحة وعقارات في إندونيسيا. أجب بالعربية: " + query }] }]
                    })
                });

                const data = await response.json();

                if (data.candidates && data.candidates[0].content) {
                    const reply = data.candidates[0].content.parts[0].text;
                    chat.innerHTML += `<div class="ai-msg">${reply}</div>`;
                } else {
                    // إذا أرجع جوجل خطأ
                    let errorMsg = data.error ? data.error.message : "خطأ غير معروف";
                    chat.innerHTML += `<div class="p-2 text-red-500 text-xs text-center border border-red-200 rounded">تنبيه: ${errorMsg}</div>`;
                }
            } catch (err) {
                chat.innerHTML += `<div class="p-2 text-red-500 text-xs text-center border border-red-200 rounded">فشل الاتصال بالخادم. يرجى إعادة المحاولة.</div>`;
            } finally {
                loading.classList.add('hidden');
                chat.scrollTop = chat.scrollHeight;
            }
        }
    </script>
</body>
</html>
