<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>互動單字測驗遊戲</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 20px;
        }
        #word {
            font-size: 24px;
            margin: 20px;
        }
        #score, #currentQuestion {
            font-size: 18px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            margin: 10px;
        }
        #definition {
            margin: 20px;
            font-size: 18px;
            color: red;
        }
        .option {
            display: block;
            margin: 10px auto;
            width: 200px;
        }
        #options {
            display: flex;
            flex-direction: column;
            align-items: center; /* 中央對齊選項 */
        }
        #result {
            display: none;
            margin-top: 20px;
        }
    </style>
</head>
<body>

    <h1>互動單字測驗遊戲</h1>
    <button id="startButton" onclick="startGame()">開始遊戲</button>
    
    <div id="word" style="display:none;"></div>
    <div id="options" style="display:none;"></div>
    <button id="endGameButton" onclick="endGame()" style="display:none;">結束遊戲</button>
    <button id="quitButton" onclick="quitGame()" style="display:none;">隨時結束遊戲</button> <!-- 新增結束遊戲按鈕 -->
    <div id="score" style="display:none;">分數: 0</div>
    <div id="currentQuestion" style="display:none;">問題: 1/50</div>
    <div id="definition" style="display:none;"></div>
    <div id="result">
        <h2>遊戲結束</h2>
        <h3>答對的單字:</h3>
        <ul id="correctWordsList"></ul>
        <h3>答錯的單字:</h3>
        <ul id="incorrectWordsList"></ul>
    </div>

    <script>
         const vocabulary = [
            { word: "abandon", meanings: ["放棄", "接受", "提升"], explanation: "To give up or leave behind." },
            { word: "befit", meanings: ["合適", "讚美", "轉變"], explanation: "To be appropriate or suitable for." },
            { word: "catastrophe", meanings: ["災難", "成功", "重生"], explanation: "A sudden and widespread disaster." },
            { word: "debase", meanings: ["貶低", "升值", "學習"], explanation: "To reduce in quality or value." },
            { word: "eccentric", meanings: ["古怪的", "友善的", "可愛的"], explanation: "Unconventional and slightly strange." },
            { word: "forefront", meanings: ["前線", "後方", "旁邊"], explanation: "The leading or most important position." },
            { word: "heterogeneous", meanings: ["異質的", "同質的", "單一的"], explanation: "Diverse in character or content." },
            { word: "illegitimate", meanings: ["非法的", "合法的", "合理的"], explanation: "Not authorized by law; not in accordance with accepted standards." },
            { word: "macrocosm", meanings: ["宇宙", "星星", "小世界"], explanation: "The whole universe; a large, complex system." },
            { word: "neolithic", meanings: ["新石器時代", "現代", "未來"], explanation: "Relating to the later part of the Stone Age." },
            { word: "obese", meanings: ["肥胖的", "瘦弱的", "健康的"], explanation: "Extremely overweight." },
            { word: "abolish", meanings: ["廢除", "建立", "增長"], explanation: "To formally put an end to a system, practice, or institution." },
            { word: "bemoan", meanings: ["哀嘆", "開心", "欣喜"], explanation: "To express deep grief or distress." },
            { word: "catholic", meanings: ["廣泛的", "狹隘的", "偏執的"], explanation: "Universal; including a wide variety of things." },
            { word: "debilitate", meanings: ["使衰弱", "增強", "康復"], explanation: "To weaken or reduce strength." },
            { word: "eclipse", meanings: ["日食或月食", "光亮", "生長"], explanation: "An obscuring of light from one celestial body by the passage of another." },
            { word: "foregoing", meanings: ["先前的", "接下來的", "更新的"], explanation: "Just mentioned or stated; preceding." },
            { word: "homogeneous", meanings: ["同質的", "異質的", "多樣的"], explanation: "Of the same or a similar kind or nature." },
            { word: "illicit", meanings: ["非法的", "合法的", "受歡迎的"], explanation: "Forbidden by law, rules, or custom." },
            { word: "malevolent", meanings: ["惡意的", "友善的", "無害的"], explanation: "Having or showing a wish to do evil to others." },
            { word: "nonchalant", meanings: ["冷漠的", "關心的", "專注的"], explanation: "Feeling or appearing casually calm and relaxed." },
            { word: "obituary", meanings: ["訃告", "喜訊", "讚美"], explanation: "A notice of a death, especially in a newspaper." },
            { word: "aboriginal", meanings: ["原住民的", "外來的", "文明的"], explanation: "Relating to the original inhabitants of a place." },
            { word: "berate", meanings: ["責罵", "讚美", "忽略"], explanation: "To scold or criticize someone angrily." },
            { word: "circuit", meanings: ["電路", "路徑", "單位"], explanation: "A closed path through which an electric current flows." },
            { word: "decant", meanings: ["倒出", "混合", "攪拌"], explanation: "To pour a liquid from one container to another." },
            { word: "efface", meanings: ["抹去", "增強", "重複"], explanation: "To erase or wipe out something." },
            { word: "forerunner", meanings: ["先驅", "跟隨者", "對手"], explanation: "A person or thing that precedes the coming or development of someone or something else." },
            { word: "homosexual", meanings: ["同性戀的", "異性戀的", "雙性戀的"], explanation: "Attracted to the same sex." },
            { word: "illiterate", meanings: ["文盲的", "聰明的", "教育程度高的"], explanation: "Unable to read or write." },
            { word: "malfunction", meanings: ["故障", "正常運作", "運行"], explanation: "Fail to function normally." },
            { word: "nonconformist", meanings: ["不墨守成規者", "跟隨者", "創新者"], explanation: "A person whose behavior or views do not conform to prevailing ideas or practices." },
            { word: "oblique", meanings: ["斜的", "直的", "平行的"], explanation: "Not explicit or done in a direct way." },
            { word: "abortion", meanings: ["墮胎", "懷孕", "出生"], explanation: "The termination of a pregnancy." },
            { word: "besiege", meanings: ["圍攻", "保護", "撤離"], explanation: "To surround a place with armed forces in order to capture it." },
            { word: "circumference", meanings: ["周長", "面積", "體積"], explanation: "The distance around a circle." },
            { word: "deceased", meanings: ["已故的", "健在的", "活著的"], explanation: "No longer living; dead." },
            { word: "ejaculate", meanings: ["射出", "吸入", "擠壓"], explanation: "To eject semen from the male reproductive system." },
            { word: "foresight", meanings: ["先見之明", "無知", "短視"], explanation: "The ability to predict or plan for the future." },
            { word: "hostage", meanings: ["人質", "保護者", "囚犯"], explanation: "A person seized or held as security for the fulfillment of a condition." },
            { word: "illuminate", meanings: ["照亮", "暗淡", "模糊"], explanation: "To light up or make something clear." },
            { word: "malign", meanings: ["誹謗", "讚美", "支持"], explanation: "To speak about someone in a spitefully critical manner." },
            { word: "nonviolence", meanings: ["非暴力", "暴力", "抗議"], explanation: "The use of peaceful means to bring about social or political change." },
            { word: "oblivion", meanings: ["被遺忘", "知名", "意識"], explanation: "The state of being unaware or unconscious." },
            { word: "abrasive", meanings: ["磨蝕的", "平滑的", "柔軟的"], explanation: "Capable of polishing or cleaning a surface by rubbing." },
            { word: "bewitch", meanings: ["施魔法", "吸引", "驅邪"], explanation: "To enchant or cast a spell over someone." },
            { word: "circumlocution", meanings: ["迂迴的表達", "直接的表達", "簡單的表達"], explanation: "The use of many words where fewer would do, especially in a deliberate attempt to be vague." },
            { word: "deciduous", meanings: ["落葉的", "常綠的", "耐寒的"], explanation: "Trees or shrubs that shed their leaves annually." },
            { word: "elaborate", meanings: ["精緻的", "簡單的", "粗糙的"], explanation: "Involving many careful details; intricate." },
            { word: "forestall", meanings: ["預防", "促進", "延遲"], explanation: "To prevent or obstruct an anticipated event." },
            { word: "hypersensitive", meanings: ["過敏的", "鈍感的", "冷漠的"], explanation: "Excessively sensitive to emotional or physical stimuli." },
            { word: "illustrate", meanings: ["闡明", "隱藏", "簡化"], explanation: "To explain or make something clear by using examples or pictures." },
            { word: "malnutrition", meanings: ["營養不良", "健康", "增重"], explanation: "Lack of proper nutrition, caused by not having enough to eat, not eating enough of the right things, or being unable to use the food that one eats." },
            { word: "obnoxious", meanings: ["令人厭惡的", "友好的", "吸引的"], explanation: "Extremely unpleasant or offensive." },
            { word: "neoclassic", meanings: ["新古典的", "現代的", "古老的"], explanation: "Relating to a revival of classical style in literature or art." },
            { word: "abrupt", meanings: ["突然的", "緩慢的", "平穩的"], explanation: "Sudden and unexpected." },
            { word: "biannual", meanings: ["每年的", "每月的", "每兩年的"], explanation: "Occurring twice a year." },
            { word: "circumnavigate", meanings: ["環繞航行", "直航", "冒險"], explanation: "To travel all the way around something." },
            { word: "decipher", meanings: ["解碼", "編碼", "隱藏"], explanation: "To convert (a text written in code, or a coded signal) into normal language." },
            { word: "elapse", meanings: ["經過", "停留", "延續"], explanation: "To pass or go by (time)." },
            { word: "forfeit", meanings: ["喪失", "獲得", "賺取"], explanation: "To lose or give up something as a penalty for a mistake or failure." },
            { word: "hypertension", meanings: ["高血壓", "低血壓", "正常血壓"], explanation: "A condition in which the force of the blood against the artery walls is too high." },
            { word: "immerse", meanings: ["浸泡", "浮出水面", "退水"], explanation: "To dip or submerge in a liquid." },
            { word: "metamorphosis", meanings: ["變形", "穩定", "簡單"], explanation: "A transformation or dramatic change." },
            { word: "obscene", meanings: ["猥褻的", "文雅的", "純潔的"], explanation: "Offensive or disgusting by accepted standards of morality and decency." },
            { word: "neocolonialism", meanings: ["新殖民主義", "反殖民主義", "經濟獨立"], explanation: "The practice of using capitalism, globalization, and cultural imperialism to influence a country." },
            { word: "accelerate", meanings: ["加速", "減速", "延遲"], explanation: "To increase in speed or to cause to happen sooner." },
            { word: "bilingual", meanings: ["雙語的", "單語的", "多語的"], explanation: "Able to speak two languages fluently." },
            { word: "circumscribe", meanings: ["限制", "擴大", "自由"], explanation: "To restrict something within limits." },
            { word: "declaim", meanings: ["高聲演講", "低聲私語", "輕聲細語"], explanation: "To speak rhetorically or passionately." },
            { word: "elicit", meanings: ["引出", "隱藏", "消除"], explanation: "To draw out a response or reaction." },
            { word: "forsake", meanings: ["拋棄", "擁有", "保留"], explanation: "To abandon or give up something valued." },
            { word: "immigrate", meanings: ["移民", "出境", "旅遊"], explanation: "To come to live permanently in a foreign country." },
            { word: "metaphor", meanings: ["隱喻", "明喻", "比較"], explanation: "A figure of speech that implies a comparison between two unlike things." },
            { word: "obsess", meanings: ["著迷", "忽略", "關心"], explanation: "To preoccupy or fill the mind of someone continually." },
            { word: "heterophobia", meanings: ["異性戀恐懼", "同性戀恐懼", "無性戀"], explanation: "An irrational fear of people of the opposite sex." },
            { word: "nonflammable", meanings: ["不易燃的", "易燃的", "可燃的"], explanation: "Not capable of catching fire easily." },
            { word: "accessory", meanings: ["配件", "主要部分", "獨立的"], explanation: "An additional item that enhances or complements something." },
            { word: "binary", meanings: ["二元的", "單一的", "多元的"], explanation: "Consisting of two parts or things." },
            { word: "circumvent", meanings: ["迴避", "直接", "面對"], explanation: "To find a way around (an obstacle or rule)." },
            { word: "decode", meanings: ["解碼", "編碼", "加密"], explanation: "To convert (a coded message) into intelligible language." },
            { word: "eligible", meanings: ["有資格的", "無資格的", "排除的"], explanation: "Having the right to do or obtain something." },
            { word: "imminent", meanings: ["即將來臨的", "遙遠的", "不確定的"], explanation: "About to happen; impending." },
            { word: "meteor", meanings: ["流星", "星星", "衛星"], explanation: "A small body of matter from outer space that enters the Earth's atmosphere." },
            { word: "omnivorous", meanings: ["雜食的", "肉食的", "素食的"], explanation: "Feeding on both plants and animals." },
            { word: "famish", meanings: ["饑餓", "飽滿", "滿足"], explanation: "To suffer from extreme hunger." },
            { word: "heterosexual", meanings: ["異性戀的", "同性戀的", "無性戀的"], explanation: "Attracted to people of the opposite sex." },
            { word: "nonplus", meanings: ["使困惑", "使清楚", "使安心"], explanation: "To surprise and confuse someone." },
        ];


        let currentScore = 0;
        let currentQuestionIndex = 0;
        let correctWords = [];
        let incorrectWords = [];
        let isIncorrect = false; // 新增狀態變數來追蹤是否答錯

        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
        }

        function startGame() {
            currentScore = 0;
            currentQuestionIndex = 0;
            correctWords = [];
            incorrectWords = [];
            isIncorrect = false; // 重置狀態
            shuffle(vocabulary); // 隨機打亂單字順序
            document.getElementById("score").style.display = "block";
            document.getElementById("currentQuestion").style.display = "block";
            document.getElementById("definition").style.display = "none";
            document.getElementById("endGameButton").style.display = "inline";
            document.getElementById("quitButton").style.display = "inline"; // 顯示結束遊戲按鈕
            document.getElementById("startButton").style.display = "none"; // 隱藏開始按鈕
            showQuestion();
        }

        function showQuestion() {
            if (currentQuestionIndex < vocabulary.length) {
                const currentWord = vocabulary[currentQuestionIndex];
                document.getElementById("word").textContent = currentWord.word;
                document.getElementById("word").style.display = "block";
                
                const optionsContainer = document.getElementById("options");
                optionsContainer.innerHTML = "";
                
                currentWord.meanings.forEach((meaning, index) => {
                    const button = document.createElement("button");
                    button.textContent = meaning;
                    button.classList.add("option");
                    button.onclick = () => checkAnswer(index);
                    optionsContainer.appendChild(button);
                });

                optionsContainer.style.display = "flex"; // 顯示選項
                document.getElementById("score").textContent = `分數: ${currentScore}`;
                document.getElementById("currentQuestion").textContent = `問題: ${currentQuestionIndex + 1}/${vocabulary.length}`;
            } else {
                endGame();
            }
        }

        function checkAnswer(selectedIndex) {
            const currentWord = vocabulary[currentQuestionIndex];
            if (selectedIndex === 0) { // 正確答案為第一個選項
                currentScore++;
                correctWords.push(currentWord.word); // 將答對的單字加入列表
                //alert("正確!");
                currentQuestionIndex++; // 增加題目索引
                showQuestion(); // 顯示下一題
            } else {
                incorrectWords.push(currentWord.word); // 將答錯的單字加入列表
                document.getElementById("definition").textContent = `正確答案是: ${currentWord.explanation}`;
                document.getElementById("definition").style.display = "block";
                isIncorrect = true; // 設定為答錯狀態
                // 提供選擇重新回答
                offerRetry();
            }
        }

        function offerRetry() {
            const optionsContainer = document.getElementById("options");
            optionsContainer.innerHTML = ""; // 清空選項
            const retryButton = document.createElement("button");
            retryButton.textContent = "重新選擇答案";
            retryButton.onclick = () => {
                document.getElementById("definition").style.display = "none"; // 隱藏解釋
                showQuestion(); // 重新顯示問題
            };
            optionsContainer.appendChild(retryButton); // 增加重新選擇按鈕
        }

        function endGame() {
            document.getElementById("word").style.display = "none";
            document.getElementById("options").style.display = "none";
            document.getElementById("definition").style.display = "none";
            document.getElementById("endGameButton").style.display = "none";
            document.getElementById("quitButton").style.display = "none"; // 隱藏結束遊戲按鈕

            // 顯示答對和答錯的單字列表
            document.getElementById("correctWordsList").innerHTML = correctWords.map(word => `<li>${word}</li>`).join("");
            document.getElementById("incorrectWordsList").innerHTML = incorrectWords.map(word => `<li>${word}</li>`).join("");
            document.getElementById("result").style.display = "block";

            alert(`遊戲結束! 你的分數是: ${currentScore}`);
            document.getElementById("score").style.display = "none";
            document.getElementById("currentQuestion").style.display = "none";
            document.getElementById("startButton").style.display = "inline"; // 重新顯示開始按鈕
        }

        function quitGame() {
            const confirmQuit = confirm("確定要結束遊戲嗎？");
            if (confirmQuit) {
                endGame(); // 結束遊戲
            }
        }
    </script>

</body>
</html>
