<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>台語動物教學小遊戲</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.21.2/babel.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Noto Sans TC', sans-serif;
            background-color: #f8f9fa;
            background-image: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            background-attachment: fixed;
        }
        .quiz-container {
            max-width: 800px;
            margin: 0 auto;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            border-radius: 16px;
            overflow: hidden;
            background: white;
        }
        .quiz-header {
            background: linear-gradient(90deg, #4f46e5, #6366f1);
            color: white;
            padding: 1.5rem;
            text-align: center;
            position: relative;
        }
        .quiz-header::after {
            content: "";
            position: absolute;
            bottom: -10px;
            left: 0;
            right: 0;
            margin: 0 auto;
            width: 0;
            height: 0;
            border-left: 15px solid transparent;
            border-right: 15px solid transparent;
            border-top: 15px solid #6366f1;
        }
        .question-card {
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
            border: 1px solid rgba(0, 0, 0, 0.1);
            transition: transform 0.2s;
        }
        .question-card:hover {
            transform: translateY(-3px);
        }
        .option-label {
            transition: all 0.2s;
            border-radius: 8px;
            padding: 0.5rem 1rem;
        }
        .option-label:hover {
            background-color: #f3f4f6;
        }
        .btn-primary {
            background: linear-gradient(90deg, #4f46e5, #6366f1);
            transition: all 0.3s;
            transform: translateY(0);
            box-shadow: 0 4px 6px rgba(99, 102, 241, 0.3);
        }
        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 10px rgba(99, 102, 241, 0.4);
        }
        .btn-primary:active {
            transform: translateY(1px);
        }
        .btn-secondary {
            background: linear-gradient(90deg, #8b5cf6, #a78bfa);
            transition: all 0.3s;
            transform: translateY(0);
            box-shadow: 0 4px 6px rgba(139, 92, 246, 0.3);
        }
        .btn-secondary:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 10px rgba(139, 92, 246, 0.4);
        }
        .video-container {
            box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1);
            border-radius: 12px;
            overflow: hidden;
        }
        .result-card {
            background: linear-gradient(145deg, #e6f7ff, #f0f9ff);
            border-left: 5px solid #4f46e5;
        }
        input[type="checkbox"], input[type="radio"] {
            transform: scale(1.2);
            margin-right: 10px;
            accent-color: #4f46e5;
        }
        .correct-answer {
            color: #10b981;
            font-weight: 500;
        }
        .incorrect-answer {
            color: #ef4444;
            font-weight: 500;
        }
        .quiz-footer {
            font-size: 0.8rem;
            color: #6b7280;
            text-align: center;
            padding: 1rem;
            background-color: #f9fafb;
            border-top: 1px solid #e5e7eb;
        }
        @media (max-width: 640px) {
            .quiz-container {
                width: 95%;
                margin: 1rem auto;
            }
        }
    </style>
</head>
<body class="py-8">
    <div id="root"></div>

    <script type="text/babel">
        const { useState } = React;

        const TaiwaneseAnimalQuiz = () => {
            const [videoWatched, setVideoWatched] = useState(false);
            const [answers, setAnswers] = useState({
                q1: [],
                q2: '',
                q3: '',
                q4: '',
                q5: ''
            });
            const [submitted, setSubmitted] = useState(false);
            const [result, setResult] = useState('');
            const [showConfetti, setShowConfetti] = useState(false);

            // 問題及選項
            const questions = {
                q1: {
                    question: '請問剛剛有哪些動物的台語教學？（可複選）',
                    options: ['斑馬', '麒麟', '長頸鹿', '猴子', '企鵝', '獅子', '老虎'],
                    type: 'checkbox',
                    correctAnswer: ['長頸鹿', '斑馬', '企鵝']
                },
                q2: {
                    question: '最先教的動物是？',
                    options: ['企鵝', '長頸鹿', '斑馬', '獅子'],
                    type: 'radio',
                    correctAnswer: '長頸鹿'
                },
                q3: {
                    question: '誇讚人深藏不漏的台語怎麼說？',
                    options: ['深藏不露', '大隻雞慢啼', '烏矸仔貯豆油', '金玉其外', '有錢開無路'],
                    type: 'radio',
                    correctAnswer: '烏矸仔貯豆油'
                },
                q4: {
                    question: '豬哥亮哪個動物的字不會寫？',
                    options: ['企鵝', '馬來貘', '斑馬', '麒麟', '長頸鹿'],
                    type: 'radio',
                    correctAnswer: '麒麟'
                },
                q5: {
                    question: '三重大學是什麼意思呢？',
                    options: ['三重的一所社區大學', '很會念書', '沒有讀書的意思', '有很多學問'],
                    type: 'radio',
                    correctAnswer: '沒有讀書的意思'
                }
            };

            const handleWatchComplete = () => {
                setVideoWatched(true);
                window.scrollTo({
                    top: document.getElementById('questions-section').offsetTop - 20,
                    behavior: 'smooth'
                });
            };

            const handleCheckboxChange = (questionId, option) => {
                setAnswers(prev => {
                    const newAnswers = { ...prev };
                    if (newAnswers[questionId].includes(option)) {
                        newAnswers[questionId] = newAnswers[questionId].filter(item => item !== option);
                    } else {
                        newAnswers[questionId] = [...newAnswers[questionId], option];
                    }
                    return newAnswers;
                });
            };

            const handleRadioChange = (questionId, option) => {
                setAnswers(prev => ({
                    ...prev,
                    [questionId]: option
                }));
            };

            const handleSubmit = () => {
                setSubmitted(true);
                
                // Check answers
                let correct = 0;
                
                // 檢查多選題 (q1)
                const q1Answer = answers.q1.sort().join(',');
                const q1Correct = questions.q1.correctAnswer.sort().join(',');
                if (q1Answer === q1Correct) correct++;
                
                // 檢查單選題 (q2-q5)
                ['q2', 'q3', 'q4', 'q5'].forEach(key => {
                    if (answers[key] === questions[key].correctAnswer) correct++;
                });
                
                if (correct === 5) {
                    setResult('恭喜你全部答對！成功過關！');
                    setShowConfetti(true);
                } else {
                    setResult(`你答對了 ${correct}/5 題，再試一次吧！`);
                }

                // 滾動到結果區域
                setTimeout(() => {
                    window.scrollTo({
                        top: document.getElementById('result-section').offsetTop - 20,
                        behavior: 'smooth'
                    });
                }, 300);
            };

            const resetQuiz = () => {
                setSubmitted(false);
                setResult('');
                setShowConfetti(false);
                setAnswers({
                    q1: [],
                    q2: '',
                    q3: '',
                    q4: '',
                    q5: ''
                });
                // 滾動到問題區域
                window.scrollTo({
                    top: document.getElementById('questions-section').offsetTop - 20,
                    behavior: 'smooth'
                });
            };

            // 檢查是否回答完所有問題
            const isAllAnswered = () => {
                return (
                    answers.q1.length > 0 &&
                    answers.q2 !== '' &&
                    answers.q3 !== '' &&
                    answers.q4 !== '' &&
                    answers.q5 !== ''
                );
            };

            return (
                <div className="quiz-container">
                    <div className="quiz-header">
                        <h1 className="text-3xl font-bold">台語動物教學小遊戲</h1>
                        <p className="mt-2 opacity-80">觀看影片，回答問題，測試你的台語能力！</p>
                    </div>
                    
                    <div className="p-6">
                        <div className="mb-8">
                            <h2 className="text-xl font-bold mb-4 text-indigo-700">步驟一：觀看影片</h2>
                            <div className="video-container">
                                <div className="relative pt-9 h-64 md:h-96 overflow-hidden">
                                    <iframe 
                                        className="absolute top-0 left-0 w-full h-full"
                                        src="https://www.youtube.com/embed/StTvn9R6VME" 
                                        title="台語動物教學影片"
                                        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                                        allowFullScreen
                                    ></iframe>
                                </div>
                            </div>
                            <button 
                                onClick={handleWatchComplete} 
                                className="mt-4 btn-primary text-white font-bold py-3 px-6 rounded-full w-full"
                            >
                                我已經看完影片了 →
                            </button>
                        </div>

                        {videoWatched && (
                            <div id="questions-section" className="mt-8 mb-6">
                                <h2 className="text-xl font-bold mb-4 text-indigo-700">步驟二：回答問題</h2>
                                
                                <div className="space-y-6">
                                    {/* 問題 1: 多選題 */}
                                    <div className="question-card p-5 bg-white">
                                        <h3 className="block mb-3 font-bold text-lg border-b pb-2 text-gray-800">
                                            1. {questions.q1.question}
                                        </h3>
                                        <div className="grid grid-cols-1 sm:grid-cols-2 gap-2 mt-3">
                                            {questions.q1.options.map((option, index) => (
                                                <div key={index} className="option-label flex items-center">
                                                    <input
                                                        type="checkbox"
                                                        id={`q1-${index}`}
                                                        checked={answers.q1.includes(option)}
                                                        onChange={() => handleCheckboxChange('q1', option)}
                                                        disabled={submitted}
                                                        className="mr-2 h-4 w-4"
                                                    />
                                                    <label htmlFor={`q1-${index}`} className="cursor-pointer flex-grow">
                                                        {option}
                                                    </label>
                                                </div>
                                            ))}
                                        </div>
                                        {submitted && (
                                            <p className={`mt-3 p-2 rounded ${answers.q1.sort().join(',') === questions.q1.correctAnswer.sort().join(',') ? 'correct-answer' : 'incorrect-answer'}`}>
                                                {answers.q1.sort().join(',') === questions.q1.correctAnswer.sort().join(',') 
                                                    ? '✓ 正確！' 
                                                    : `✗ 正確答案：${questions.q1.correctAnswer.join('、')}`}
                                            </p>
                                        )}
                                    </div>
                                    
                                    {/* 問題 2-5: 單選題 */}
                                    {['q2', 'q3', 'q4', 'q5'].map((qId, qIndex) => (
                                        <div key={qId} className="question-card p-5 bg-white">
                                            <h3 className="block mb-3 font-bold text-lg border-b pb-2 text-gray-800">
                                                {qIndex + 2}. {questions[qId].question}
                                            </h3>
                                            <div className="grid grid-cols-1 gap-2 mt-3">
                                                {questions[qId].options.map((option, index) => (
                                                    <div key={index} className="option-label flex items-center">
                                                        <input
                                                            type="radio"
                                                            id={`${qId}-${index}`}
                                                            name={qId}
                                                            checked={answers[qId] === option}
                                                            onChange={() => handleRadioChange(qId, option)}
                                                            disabled={submitted}
                                                            className="mr-2 h-4 w-4"
                                                        />
                                                        <label htmlFor={`${qId}-${index}`} className="cursor-pointer flex-grow">
                                                            {option}
                                                        </label>
                                                    </div>
                                                ))}
                                            </div>
                                            {submitted && (
                                                <p className={`mt-3 p-2 rounded ${answers[qId] === questions[qId].correctAnswer ? 'correct-answer' : 'incorrect-answer'}`}>
                                                    {answers[qId] === questions[qId].correctAnswer
                                                        ? '✓ 正確！'
                                                        : `✗ 正確答案：${questions[qId].correctAnswer}`}
                                                </p>
                                            )}
                                        </div>
                                    ))}
                                    
                                    <div id="result-section" className="mt-8">
                                        {!submitted ? (
                                            <button 
                                                onClick={handleSubmit} 
                                                disabled={!isAllAnswered()}
                                                className={`w-full py-3 px-6 rounded-full font-bold text-center transform transition-all ${isAllAnswered() 
                                                    ? 'btn-primary text-white' 
                                                    : 'bg-gray-300 text-gray-500 cursor-not-allowed'}`}
                                            >
                                                {isAllAnswered() ? '提交答案' : '請回答所有問題'}
                                            </button>
                                        ) : (
                                            <div className="result-card p-5 rounded-lg my-6">
                                                <p className="text-xl font-bold text-center p-3 rounded">
                                                    {result}
                                                </p>
                                                <button 
                                                    onClick={resetQuiz}
                                                    className="btn-secondary text-white font-bold py-3 px-6 rounded-full w-full mt-4"
                                                >
                                                    再試一次
                                                </button>
                                            </div>
                                        )}
                                    </div>
                                </div>
                            </div>
                        )}
                    </div>
                    
                    <div className="quiz-footer">
                        台語動物教學小遊戲 © 2025
                    </div>
                </div>
            );
        };

        ReactDOM.createRoot(document.getElementById('root')).render(<TaiwaneseAnimalQuiz />);
    </script>
</body>
</html>
