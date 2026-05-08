<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #f9fafb; font-family: sans-serif; }
        #chat { height: calc(100vh - 140px); overflow-y: auto; display: flex; flex-direction: column; gap: 12px; padding: 15px; }
    </style>
</head>
<body class="h-screen flex flex-col">
    <div class="bg-blue-800 p-4 text-white font-bold text-center shadow-lg">INDO GUIDE AI</div>
    <div id="chat">
        <div class="bg-white p-3 rounded-lg border shadow-sm max-w-[85%] text-sm">أهلاً بك! أنا خبير إندونيسيا. كيف يمكنني مساعدتك؟</div>
    </div>
    <div id="loader" class="hidden text-center text-xs text-gray-500 animate-pulse">جاري التفكير...</div>
    <div class="p-4 bg-white border-t flex gap-2">
        <input type="text" id="in" placeholder="اسألني عن جاكرتا أو العقارات.." class="flex-1 border rounded-full px-4 py-2 outline-none text-sm focus:ring-2 focus:ring-blue-500">
        <button onclick="ask()" class="bg-blue-700 text-white px-5 py-2 rounded-full font-bold text-sm hover:bg-blue-800 transition">إرسال</button>
    </div>

    <script>
        async function ask() {
            const input = document.getElementById('in');
            const chat = document.getElementById('chat');
            const loader = document.getElementById('loader');
            const prompt = input.value.trim();
            const KEY = "AlzaSyA1PNjdG8qGCaR_zoOcnzJQnUeS0zW5NBE"; // مفتاحك الفعال

            if (!prompt) return;

            // إظهار رسالة المستخدم
            chat.innerHTML += `<div class="bg-blue-600 text-white p-3 rounded-lg self-end max-w-[85%] text-sm shadow-md">${prompt}</div>`;
            input.value = "";
            loader.classList.remove('hidden');
            chat.scrollTop = chat.scrollHeight;

            try {
                const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${KEY}`, {
                    method: "POST",
                    headers: { "Content-Type": "application/json" },
                    body: JSON.stringify({
                        contents: [{ parts: [{ text: "أنت خبير سياحة وعقارات في إندونيسيا. أجب باختصار وبالعربية: " + prompt }] }]
                    })
                });

                const data = await response.json();
                
                if (data.candidates && data.candidates[0].content) {
                    const aiText = data.candidates[0].content.parts[0].text;
                    chat.innerHTML += `<div class="bg-white p-3 rounded-lg border shadow-sm max-w-[85%] text-sm text-gray-800">${aiText}</div>`;
                } else {
                    throw new Error(data.error ? data.error.message : "خطأ غير معروف");
                }
            } catch (err) {
                console.error(err);
                chat.innerHTML += `<div class="bg-red-50 text-red-600 p-2 rounded text-center text-[10px] border border-red-200">فشل الاتصال: ${err.message}</div>`;
            } finally {
                loader.classList.add('hidden');
                chat.scrollTop = chat.scrollHeight;
            }
        }
    </script>
</body>
</html>
