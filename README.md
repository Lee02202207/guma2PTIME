<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>패턴 타이머 제작자</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f0f0f0;
            padding: 20px;
        }
        .container {
            background: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            max-width: 500px;
            margin: 0 auto;
        }
        #timer {
            font-size: 3em;
            margin: 20px 0;
            color: #333;
        }
        #message {
            font-size: 1.8em;
            height: 50px;
            font-weight: bold;
            margin: 15px 0;
        }
        .땅개 { color: #6a0dad; }
        .권능 { color: #8b0000; }
        button {
            background: #3498db;
            color: white;
            border: none;
            padding: 10px 20px;
            margin: 5px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
        }
        button:hover { background: #2980b9; }
        input {
            padding: 10px;
            font-size: 1em;
            width: 100px;
            text-align: center;
        }
        #patternInfo {
            text-align: left;
            margin-top: 20px;
            background: #f9f9f9;
            padding: 15px;
            border-radius: 5px;
        }
        .pattern-item { margin: 8px 0; padding: 5px; border-left: 4px solid #3498db; }
        #nextPattern { font-size: 1.2em; margin-top: 15px; padding: 10px; background: #eaf7ff; border-radius: 5px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>검마2페 패턴 타이머</h1>
        <div>
            <label for="startTime">시작 시간: </label>
            <input type="text" id="startTime" placeholder="2500" value="2500">
            <button id="startBtn">시작</button>
        </div>
        <div id="timer">25:00</div>
        <div id="message"></div>
        <div id="nextPattern">다음 패턴: -</div>
        <div id="patternInfo"><h3>예정된 패턴 :</h3></div>
    </div>

    <script>
        const timerDisplay = document.getElementById('timer');
        const messageDisplay = document.getElementById('message');
        const startBtn = document.getElementById('startBtn');
        const startTimeInput = document.getElementById('startTime');
        const patternInfo = document.getElementById('patternInfo');
        const nextPatternDisplay = document.getElementById('nextPattern');

        let timer;
        let totalSeconds = 0;
        let elapsedSeconds = 0;
        let patternIndex = 0;
        let 땅개횟수 = 0;
        let 권능횟수 = 0;

        const patterns = [
            [40, "땅개", "땅개"],
            [80, "권능", "권능"],
            [90, "땅개", "땅개"],
            [140, "땅개", "땅개"],
            [160, "권능", "권능"],
            [190, "땅개", "땅개"],
            [240, "땅개", "땅개"],
            [240, "권능", "권능"],
            [290, "땅개", "땅개"],
            [320, "권능", "권능"]
        ].sort((a, b) => a[0] - b[0]);

        startBtn.addEventListener('click', startTimer);

        function parseTime(input) {
            let timeStr = input.replace(/\D/g, '');
            if (timeStr.length === 3) timeStr = '0' + timeStr;
            if (timeStr.length === 4) {
                const mins = parseInt(timeStr.substring(0, 2));
                const secs = parseInt(timeStr.substring(2, 4));
                return mins * 60 + secs - 10;
            }
            return 1500 - 10;
        }

        function startTimer() {
            const inputTime = startTimeInput.value;
            totalSeconds = parseTime(inputTime);
            elapsedSeconds = 0;
            patternIndex = 0;
            땅개횟수 = 0;
            권능횟수 = 0;
            
            clearInterval(timer);
            messageDisplay.textContent = '';
            messageDisplay.className = '';
            
            updatePatternList(totalSeconds + 10);
            updateNextPattern();

            const initialMins = Math.floor((totalSeconds + 10) / 60);
            const initialSecs = (totalSeconds + 10) % 60;
            timerDisplay.textContent = `${initialMins}:${initialSecs < 10 ? '0' + initialSecs : initialSecs}`;

            timer = setInterval(updateTimer, 1000);
        }

        function updateTimer() {
            elapsedSeconds++;
            const remainingSeconds = totalSeconds - elapsedSeconds;
            
            if (remainingSeconds < 0) {
                clearInterval(timer);
                timerDisplay.textContent = "00:00";
                messageDisplay.textContent = "타이머 종료!";
                nextPatternDisplay.textContent = "다음 패턴: -";
                return;
            }

            const mins = Math.floor(remainingSeconds / 60);
            const secs = remainingSeconds % 60;
            timerDisplay.textContent = `${mins}:${secs < 10 ? '0' + secs : secs}`;

            if (patternIndex < patterns.length && (elapsedSeconds + 10) === patterns[patternIndex][0]) {
                const [_, msg, cls] = patterns[patternIndex];
                messageDisplay.textContent = msg;
                messageDisplay.className = cls;
                
                if (msg === "땅개") 땅개횟수++;
                if (msg === "권능") 권능횟수++;
                
                setTimeout(() => {
                    messageDisplay.textContent = '';
                    messageDisplay.className = '';
                }, 2000);
                patternIndex++;
                updateNextPattern();
            } else {
                updateNextPattern();
            }
        }

        function updateNextPattern() {
            if (patternIndex < patterns.length) {
                const nextTime = patterns[patternIndex][0];
                const remainingTime = nextTime - (elapsedSeconds + 10);
                const nextMsg = patterns[patternIndex][1];
                
                let countText = '';
                if (nextMsg === "땅개") countText = `${땅개횟수 + 1}번째 `;
                if (nextMsg === "권능") countText = `${권능횟수 + 1}번째 `;
                
                nextPatternDisplay.textContent = `다음 패턴: ${countText}${nextMsg} (${remainingTime}초 후)`;
            } else {
                nextPatternDisplay.textContent = "다음 패턴: -";
            }
        }

        function updatePatternList(startSeconds) {
            patternInfo.innerHTML = '<h3>예정된 패턴 (6분 내):</h3>';
            let temp땅개 = 0;
            let temp권능 = 0;
            
            patterns.forEach(pattern => {
                const [time, msg, cls] = pattern;
                if (time <= 360) {
                    const timeLeft = startSeconds - time;
                    if (timeLeft >= 0) {
                        const mins = Math.floor(timeLeft / 60);
                        const secs = timeLeft % 60;
                        const timeStr = `${mins}:${secs < 10 ? '0' + secs : secs}`;
                        
                        let countText = '';
                        if (msg === "땅개") countText = `${++temp땅개}번째 `;
                        if (msg === "권능") countText = `${++temp권능}번째 `;
                        
                        const item = document.createElement('div');
                        item.className = 'pattern-item';
                        item.innerHTML = `
                            <span class="${cls}">${countText}${msg}</span> - 
                            <strong>${timeStr}</strong>
                        `;
                        patternInfo.appendChild(item);
                    }
                }
            });
        }
    </script>
</body>
</html>
        }
    </script>
</body>
</html>

