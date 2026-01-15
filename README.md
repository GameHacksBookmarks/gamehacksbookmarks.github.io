<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>SoleminerCloud</title>
    <style>
        /* Removes all margins and scrollbars for a full-screen app feel */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body, html {
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #000;
            font-family: 'Segoe UI', sans-serif;
        }

        /* Container that forces the iframe to fill the entire window */
        .viewport-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 1;
        }

        iframe {
            width: 100%;
            height: 100%;
            border: none;
            display: block;
        }

        /* Small status indicator (Hidden by default, shows on hover) */
        .info-overlay {
            position: fixed;
            top: 5px;
            right: 10px;
            color: rgba(255, 255, 255, 0.2);
            font-size: 10px;
            z-index: 100;
            pointer-events: none;
        }
    </style>
</head>
<body>

    <div class="info-overlay">SoleminerCloud | Panic: 7</div>

    <div class="viewport-container">
        <iframe 
            src="https://web.cloudmoonapp.com/" 
            allow="autoplay; fullscreen; keyboard"
            sandbox="allow-forms allow-scripts allow-same-origin allow-popups allow-popups-to-escape-sandbox">
        </iframe>
    </div>

    <script>
        /**
         * PANIC BUTTON TRIGGER
         * Instantly redirects the current tab to Google Classroom 
         * when the number "7" key is pressed.
         */
        document.addEventListener('keydown', function(event) {
            // Check if the pressed key is the number 7
            if (event.key === "7") {
                window.location.replace("https://classroom.google.com");
            }
        });

        // Ensure the window has focus to detect the keypress
        window.focus();
    </script>
</body>
</html>
