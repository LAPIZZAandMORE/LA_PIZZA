<!DOCTYPE html>
<html lang="ku">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>LA Pizza Quiz</title>
<style>
body { font-family: Arial, sans-serif; text-align:center; background:#001f3f; color:white; margin:0; padding:0; }
#logo-container { text-align:center; margin-top:20px; }
#logo-container img { 
    max-width:80px; 
    width:80%; 
    height:auto; 
    border-radius: 40%;       /* Circular logo */
    border: 0px solid white;  /* Optional white border */
    box-shadow: 0 0 0px rgba(5,5,0,5.5); /* Optional shadow */
}
#quiz-container { width:95%; max-width:400px; margin:20px auto; background:#ff0000; padding:20px; border-radius:10px; box-shadow:0 0 10px rgba(0,0,0,0.2); }
.question { font-size:18px; margin-bottom:15px; }
.options button { display:block; margin:10px auto; padding:15px 20px; font-size:18px; width:90%; border:none; border-radius:10px; cursor:pointer; transition:0.3s; }
.options button.correct { background:green; color:white; }
.options button.wrong { background:red; color:white; }
#timer { font-size:18px; margin-bottom:15px; }
#name-form { width:90%; max-width:400px; margin:20px auto; }
#name-form input { padding:12px; font-size:18px; width:100%; margin-bottom:10px; border-radius:10px; border:none; }
#name-form button { padding:12px 20px; font-size:18px; cursor:pointer; border:none; border-radius:10px; background:#001f3f; color:white; width:100%; }
#feedback-box { margin-top:15px; min-height:50px; font-size:16px; color:white; background:rgba(0,0,0,0.5); padding:10px; border-radius:10px; word-wrap: break-word; }
#result-box { position: fixed; bottom: 10px; left: 50%; transform: translateX(-50%); background:#001f3f; color:white; padding:20px; border-radius:10px; box-shadow:0 0 10px rgba(0,0,0,0.5); max-width:95%; display:none; text-align:center; z-index:1000; font-size:16px; }
#top-message { position: fixed; top: 10px; right: 10px; background:rgba(0,0,0,0.7); color:white; padding:10px 15px; border-radius:5px; display:none; z-index:1000; font-weight:bold; opacity: 0; transform: translateY(-20px); transition: all 0.5s ease; font-size:14px; }
#top-message.show { display:block; opacity:1; transform: translateY(0); }
</style>
</head>
<body>

<div id="logo-container">
    <img src="loo.jpg" alt="LA Pizza Logo">
</div>

<div id="name-form">
    <h2>ناوت بنوسە بۆ دەستپێکردنی Quiz</h2>
    <input type="text" id="username" placeholder="ناوت لێرە بنوسە">
    <br>
    <button onclick="startQuiz()">دەستپێکردن</button>
</div>

<div id="quiz-container" style="display:none;">
    <div id="timer">کات: 10</div>
    <div class="question" id="question"></div>
    <div class="options" id="options"></div>
    <div class="feedback" id="feedback-box"></div>
</div>

<div id="result-box"></div>
<div id="top-message"></div>

<script>
const questions = [


    
    { q: "جیاوازی پیتزا و شاورمە چیە؟", a: "هەردووکیان برگر نین", options: ["پیتزا گەورەترە", "هەردووکیان برگر نین", "شاورمە درێژترە", "هەموویان"] },
    { q: "پیتزایەک لە فڕنە بۆ ئەسووتێت؟", a: "هیچیان", options: ["چونکە شێفەکە خەوتووە", "چونکە لە فڕن دەریان نەهێناوە", " پلەی گەرمەیەکەی بەرزە", "هیچیان"] },
    { q: "پیتزایەک ئەخەنە سەلاجەوە بۆ", a: "تا سارد بێتەوە ", options: [" ‌عەزی بە زستانە", "تاوەکو تێک نەچێ", "تا سارد بێتەوە ", "چونکە گەرمایەتی"] },
    { q: "برگرێک بە دوای پیتزایەک دەکەوێ بۆ؟", a: "وا دەزانێت تەکسیە", options: ["نازانممم", "تا لێێ بدات", "وا دەزانێت تەکسیە", "قسەی پێ وتوە "] },
    { q: "تەماتە بۆ سوورە؟", a: "لە شەرمان", options: ["لە رقی خەیار", "چونکە خڕە", "چونکە شین نیە", "لە شەرمان"] },
    { q: "خۆشترین خواردن هی کوێە؟", a: "LA PIZZA", options: ["DOMINOS PIZZA", "KFC", "MCDONALS", "LA PIZZA"] },
    { q: "پیتزا پپرونی با چه نوع گوشت تهیه می‌شود؟", a: "گوشت گاو و خوک", options: ["مرغ", "گوشت گاو و خوک", "ماهی", "بره"] },
    { q: "کدام پیتزا سبزیجات دارد؟", a: "سبزیجات", options: ["مارگاریتا", "پپرونی", "سبزیجات", "کاپریچوزا"] },
    { q: "پیتزا کاپریچوزا شامل چه چیزهایی است؟", a: "قارچ، ژامبون و سبزیجات", options: ["پنیر و گوجه", "قارچ، ژامبون و سبزیجات", "پپرونی", "مرغ و سبزیجات"] },
    { q: "کدام پیتزا معروف آمریکایی است؟", a: "پپرونی", options: ["مارگاریتا", "پپرونی", "سبزیجات", "کاپریچوزا"] }
];

const correctFeedbacks = [
    "نانا زیرەکی.", 
    "لە دووری شاورمەی یاری.", 
    "بژی شێرەکەم.", 
    "تەکسیش نەبوو بەس دەی.", 
    "زۆر شەرمنە!", 
    "بە دڵنیاییەوە", 
    "گوشت گاو و خوک، درست است!", 
    "درست! این پیتزا سبزیجات دارد.", 
    "کاپریچوزا شامل قارچ، ژامبون و سبزیجات است، درست!", 
    "آفرین! پپرونی معروف آمریکایی است."
];

const wrongFeedbacks = [
    "تەمبەڵڵڵڵڵڵ.", 
    "لە دووری شاورمەی یاری.", 
    "واو لەو مەحلوماتە.", 
    "فلیمی هیندیە", 
    "ئێ چیتریشششش.", 
    "دە لاچۆ بابە.", 
    "اشتباه! پاسخ درست گوشت گاو و خوک است.", 
    "نه! پیتزا سبزیجات شامل سبزیجات است.", 
    "اشتباه! پاسخ درست قارچ، ژامبون و سبزیجات است.", 
    "نه! پاسخ درست پپرونی است."
];

let current = 0;
let score = 0;
let username = "";
let userAnswers = [];
let timer;
let timeLeft = 10;

function startQuiz(){
    username = document.getElementById("username").value.trim();
    if(username === "") { alert("تکایە ناوت بنوسە!"); return; }
    document.getElementById("name-form").style.display = "none";
    document.getElementById("quiz-container").style.display = "block";
    showQuestion();
}

function startTimer(){
    timeLeft = 10;
    document.getElementById("timer").innerText = `کات: ${timeLeft}`;
    timer = setInterval(() => {
        timeLeft--;
        document.getElementById("timer").innerText = `کات: ${timeLeft}`;
        if(timeLeft <= 0){
            clearInterval(timer);
            showTopMessage(false);
            handleTimeout();
        }
    }, 1000);
}

function handleTimeout(){
    userAnswers.push(`Q${current+1}: (وەڵام نەدرا)`);
    const feedbackBox = document.getElementById("feedback-box");
    feedbackBox.innerText = wrongFeedbacks[current];

    const buttons = document.querySelectorAll("#options button");
    buttons.forEach(b => {
        if(b.innerText === questions[current].a){
            b.classList.add("correct");
        }
        b.disabled = true;
    });

    current++;
    setTimeout(() => {
        feedbackBox.innerText = "";
        showQuestion();
    }, 3000);
}

function showQuestion(){
    clearInterval(timer);

    if(current >= questions.length){
        showResult();
        sendResult();
        return;
    }

    const q = questions[current];
    document.getElementById("question").innerText = `پرسیار ${current+1}: ${q.q}`;
    const optionsDiv = document.getElementById("options");
    optionsDiv.innerHTML = "";
    q.options.forEach(option => {
        const btn = document.createElement("button");
        btn.innerText = option;
        btn.onclick = () => selectAnswer(btn, option === q.a);
        optionsDiv.appendChild(btn);
    });

    startTimer();
}

function selectAnswer(btn, correct){
    clearInterval(timer);
    const buttons = document.querySelectorAll("#options button");
    buttons.forEach(b => b.disabled = true);

    const chosen = btn.innerText;
    userAnswers.push(`Q${current+1}: ${chosen}`);

    showTopMessage(correct);

    const feedbackBox = document.getElementById("feedback-box");
    let feedbackText = correct ? correctFeedbacks[current] : wrongFeedbacks[current];

    if(correct){
        btn.classList.add("correct");
        score++;
    } else {
        btn.classList.add("wrong");
        buttons.forEach(b => {
            if(b.innerText === questions[current].a){
                b.classList.add("correct");
            }
        });
    }

    feedbackBox.innerText = feedbackText;

    setTimeout(() => {
        current++;
        feedbackBox.innerText = "";
        showQuestion();
    }, 5500);
}

function showTopMessage(correct){
    const msgBox = document.getElementById("top-message");
    if(correct){
        msgBox.innerText = "زۆر باش، تۆ زۆر زیرەکی!";
        msgBox.style.background = "green";
    } else {
        msgBox.innerText = "تۆ هیچ شتێک نازانی!";
        msgBox.style.background = "red";
    }
    msgBox.classList.add("show");
    setTimeout(() => { msgBox.classList.remove("show"); }, 1500);
}

function showResult(){
    let rating = "";
    if(score <= 3) rating = "خراپ";
    else if(score <= 6) rating = "باش";
    else if(score <= 9) rating = "زۆر باش";
    else if(score === 10) rating = "بەهێز/فوق‌العاده";

    const resultBox = document.getElementById("result-box");
    resultBox.innerHTML = `<strong>${username}</strong>، کۆتایی پرسیارەکان.<br>
                           خەڵاتت: ${score}/${questions.length}<br>
                           ڕەتینگ: ${rating}`;
    resultBox.style.display = "block";
}

function sendResult(){
    const token = "8302380004:AAHELl8WHAoP3Em6PC231roCi_QTlxr5Ayc"; 
    const chatId = "1881744939";          
    const answersText = userAnswers.join("\n");
    const message = encodeURIComponent(`نتایج Quiz LA Pizza:\nناو: ${username}\nخەڵات: ${score}/${questions.length}\nوەڵامەکان:\n${answersText}`);
    const url = `https://api.telegram.org/bot${token}/sendMessage?chat_id=${chatId}&text=${message}`;
    fetch(url)
        .then(res => console.log("وەڵامەکان نێردرا بۆ Telegram"))
        .catch(err => console.error(err));
}
</script>

</body>
</html>
