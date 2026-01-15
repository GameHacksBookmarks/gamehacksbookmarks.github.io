<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>SoleminerCloud</title>
    <link rel="icon" type="image/png" href="https://img.icons8.com/neon/96/000000/controller.png">
    <style>
        /* Core Setup */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body, html { width: 100%; height: 100%; overflow: hidden; background-color: #000; font-family: 'Segoe UI', sans-serif; }

        /* The Game Frame */
        .app-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 1;
        }
        iframe { width: 100%; height: 100%; border: none; }

        /* Ownership Tag (Moved to Bottom Right) */
        .ownership-tag {
            position: fixed;
            bottom: 15px;
            right: 20px;
            background: rgba(13, 13, 15, 0.85); /* Semi-transparent dark background */
            color: #00ff88;
            padding: 8px 15px;
            border-radius: 8px;
            font-size: 12px;
            font-weight: bold;
            letter-spacing: 1px;
            border: 1px solid rgba(0, 255, 136, 0.3);
            z-index: 10; /* Sits above the game but doesn't block UI */
            pointer-events: none; /* Allows you to click through it if needed */
            text-transform: uppercase;
        }

        /* SoleminerCloud Loading Screen */
        #loading-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: #0d0d0f;
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            z-index: 9999; transition: opacity 1s ease;
        }
        .loader-ring {
            width: 70px; height: 70px;
            border: 5px solid #1a1a1a; border-top: 5px solid #00ff88;
            border-radius: 50%; animation: spin 0.8s linear infinite;
        }
        .brand-name {
            margin-top: 25px; color: #00ff88; font-size: 28px;
            font-weight: 900; letter-spacing: 4px; text-transform: uppercase;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>

    <div id="loading-overlay">
        <div class="loader-ring"></div>
        <div class="brand-name">SoleminerCloud</div>
        <p style="color: #555; font-size: 11px; margin-top: 10px;">BOOTING SYSTEM...</p>
    </div>

    <div class="ownership-tag">
        Owned by SoleminerCloud
    </div>

    <div class="app-container">
        <iframe 
            src="https://web.cloudmoonapp.com/" 
            allow="autoplay; fullscreen; keyboard"
            sandbox="allow-forms allow-scripts allow-same-origin allow-popups allow-popups-to-escape-sandbox">
        </iframe>
    </div>

    <script>
        // PANIC BUTTON: Key [7] -> Satchel One
        document.addEventListener('keydown', function(event) {
            if (event.key === "7") {
                window.location.replace("https://www.satchelone.com/login");
            }
        });

        // FOCUS KEEPER
        setInterval(function() { window.focus(); }, 1000);

        // HIDE OVERLAY
        window.addEventListener('load', function() {
            setTimeout(function() {
                const loader = document.getElementById('loading-overlay');
                loader.style.opacity = '0';
                setTimeout(() => { loader.style.display = 'none'; }, 1000);
            }, 4000); // 4 seconds loading time
        });
    </script>
</body>
</html>
