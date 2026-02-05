<!DOCTYPE html>
<html lang="am">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>የኔ ቫለንታይን ትሆኚያለሽ?</title>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap');

        /* --- GLOBAL STYLES --- */
        * {
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            margin: 0;
            padding: 0;
            height: 100vh;
            width: 100vw;
            display: flex;
            align-items: center;
            justify-content: center;
            background: linear-gradient(45deg, #ff9a9e 0%, #fecfef 100%);
            font-family: 'Poppins', sans-serif;
            overflow: hidden;
            transition: background 0.6s ease;
        }

        /* Animated Background Hearts */
        .heart-bg {
            position: absolute;
            color: rgba(255, 255, 255, 0.7);
            filter: drop-shadow(0 0 5px rgba(255,255,255,0.3));
            font-size: 20px;
            animation: floatUp 4s linear infinite;
            z-index: 0;
            pointer-events: none;
        }

        @keyframes floatUp {
            0% { transform: translateY(110vh) rotate(0deg); opacity: 1; }
            100% { transform: translateY(-10vh) rotate(360deg); opacity: 0; }
        }

        @keyframes shake {
            0% { transform: translateX(0); }
            25% { transform: translateX(-8px); }
            50% { transform: translateX(8px); }
            75% { transform: translateX(-8px); }
            100% { transform: translateX(0); }
        }

        /* --- THE CARD (RESPONSIVE) --- */
        .card {
            background: rgba(255, 255, 255, 0.35);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            padding: 30px 20px;
            border-radius: 40px;
            box-shadow: 0 25px 50px rgba(0, 0, 0, 0.15);
            text-align: center;
            z-index: 10;
            position: relative;
            border: 2px solid rgba(255, 255, 255, 0.7);
            
            /* Logic for sizing */
            width: 90%;            /* Wide on mobile */
            max-width: 420px;      /* Fixed size on PC */
            max-height: 95vh;      /* Won't overflow screen height */
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        .video-container {
            width: 100%;
            max-width: 250px;      /* Slightly smaller for mobile safety */
            aspect-ratio: 1 / 1;
            margin: 0 auto 20px;
            background: #fff;
            border-radius: 30px;
            overflow: hidden;
            position: relative;
            border: 4px solid white;
            box-shadow: 0 10px 25px rgba(214, 51, 132, 0.2);
        }

        video { width: 100%; height: 100%; object-fit: cover; }

        h1 {
            color: #d63384;
            font-size: clamp(1.5rem, 5vw, 2.2rem); /* Dynamic font size */
            margin: 0 0 20px;
            text-shadow: 1px 1px 5px rgba(255, 255, 255, 0.8);
        }

        /* --- BUTTONS --- */
        .btn-wrap {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 15px;
            min-height: 180px; /* Space for growing button */
            position: relative;
        }

        button {
            padding: 15px 30px;
            font-size: clamp(1rem, 4vw, 1.3rem);
            font-weight: 700;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            box-shadow: 0 8px 15px rgba(0,0,0,0.1);
            white-space: nowrap;
        }

        #yesBtn { background: linear-gradient(45deg, #ff4d6d, #ff758f); color: white; z-index: 100; }
        #noBtn { background: rgba(255, 255, 255, 0.9); color: #6c757d; }

        #muteBtn {
            display: none;
            position: absolute;
            bottom: 10px;
            right: 10px;
            background: rgba(0,0,0,0.6);
            color: white;
            border: none;
            padding: 8px;
            border-radius: 50%;
            cursor: pointer;
            z-index: 20;
        }

        .success-text {
            animation: pulseText 1.5s infinite alternate;
        }

        @keyframes pulseText {
            from { transform: scale(1); }
            to { transform: scale(1.05); color: #ff0055; }
        }

        /* PC-Specific Tweaks */
        @media (min-width: 768px) {
            .card { padding: 50px 30px; }
            .video-container { max-width: 280px; }
        }
    </style>
</head>
<body>

    <div class="card" id="mainCard">
        <div class="video-container">
            <video id="mainVideo" autoplay loop muted playsinline>
                <source src="first.mp4" type="video/mp4">
            </video>
            <button id="muteBtn" onclick="toggleMute()">🔊</button>
        </div>
        
        <h1 id="heading">የኔ ቫለንታይን ትሆኚያለሽ?</h1>

        <div class="btn-wrap">
            <button id="yesBtn" onclick="celebrate()">አዎ</button>
            <button id="noBtn" onclick="handleNo()">አይ</button>
        </div>
    </div>

    <script>
        const noSound = new Audio('no.mp3');
        const yesSound = new Audio('yes.mp3');

        function createHeart() {
            const heart = document.createElement('div');
            heart.classList.add('heart-bg');
            heart.innerHTML = '❤';
            heart.style.left = Math.random() * 100 + 'vw';
            heart.style.animationDuration = Math.random() * 2 + 3 + 's';
            document.body.appendChild(heart);
            setTimeout(() => heart.remove(), 5000);
        }
        setInterval(createHeart, 300);

        let growthScale = 1;
        let noClicks = 0;
        const yesBtn = document.getElementById('yesBtn');
        const noBtn = document.getElementById('noBtn');
        const card = document.getElementById('mainCard');

        const colors = [
            'linear-gradient(45deg, #ff9a9e 0%, #fecfef 100%)',
            'linear-gradient(45deg, #f093fb 0%, #f5576c 100%)',
            'linear-gradient(45deg, #ff0844 0%, #ffb199 100%)',
            'linear-gradient(45deg, #fa709a 0%, #fee140 100%)',
            'linear-gradient(45deg, #667eea 0%, #764ba2 100%)',
            'linear-gradient(45deg, #232526 0%, #414345 100%)' 
        ];

        function handleNo() {
            noClicks++;
            noSound.currentTime = 0;
            noSound.play().catch(e => {});

            growthScale += 0.8;
            yesBtn.style.transform = `scale(${growthScale})`;

            card.style.animation = 'none';
            card.offsetHeight; 
            card.style.animation = 'shake 0.4s ease';

            document.body.style.background = colors[Math.min(noClicks, colors.length - 1)];

            const messages = ["አይ", "እርግጠኛ ነሽ?", "እባክሽ? 🥺", "እንደገና አስቢበት!", "አትጨክኚብኝ!", "ልቤን አትስበሪው!", "ኧረ ተዪኝ!", "በናትሽ አዎ በይ!", "እምቢ አትበዪ!", "የመጨረሻ ዕድል!"];
            noBtn.innerText = messages[Math.min(noClicks, messages.length - 1)];

            if (noClicks >= 10) { noBtn.style.display = 'none'; }
        }

        function celebrate() {
            yesSound.play().catch(e => {});

            const video = document.getElementById('mainVideo');
            const head = document.getElementById('heading');
            const muteBtn = document.getElementById('muteBtn');
            
            document.body.style.background = 'linear-gradient(45deg, #ff4d6d 0%, #ff758f 100%)';
            
            video.src = "second.mp4";
            video.muted = false; 
            video.load();
            video.play();
            
            muteBtn.style.display = 'block';
            head.innerText = "ስለመረጥሽኝ አመሰግናለሁ! ❤️";
            head.classList.add('success-text');
            
            yesBtn.style.display = 'none';
            noBtn.style.display = 'none';

            const end = Date.now() + (6 * 1000);
            (function frame() {
              confetti({ particleCount: 7, angle: 60, spread: 60, origin: { x: 0 }, colors: ['#ff4d6d', '#ffffff'] });
              confetti({ particleCount: 7, angle: 120, spread: 60, origin: { x: 1 }, colors: ['#ff4d6d', '#ffffff'] });
              if (Date.now() < end) requestAnimationFrame(frame);
            }());
        }

        function toggleMute() {
            const video = document.getElementById('mainVideo');
            const muteBtn = document.getElementById('muteBtn');
            video.muted = !video.muted;
            muteBtn.innerText = video.muted ? "🔇" : "🔊";
        }
    </script>
</body>
</html>
