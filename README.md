# taiwanese-animal-quiz
import React, { useState } from 'react';

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
    } else {
      setResult(`你答對了 ${correct}/5 題，再試一次吧！`);
    }
  };

  const resetQuiz = () => {
    setSubmitted(false);
    setResult('');
    setAnswers({
      q1: [],
      q2: '',
      q3: '',
      q4: '',
      q5: ''
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
    <div className="flex flex-col items-center p-4 max-w-4xl mx-auto bg-gray-50 rounded-lg shadow-md">
      <h1 className="text-2xl font-bold text-center mb-6">台語動物教學小遊戲</h1>
      
      <div className="w-full mb-6">
        <div className="relative pt-9 h-64 md:h-96 overflow-hidden rounded-lg">
          <iframe 
            className="absolute top-0 left-0 w-full h-full"
            src="https://www.youtube.com/embed/StTvn9R6VME" 
            title="台語動物教學影片"
            allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
            allowFullScreen
          ></iframe>
        </div>
        <button 
          onClick={handleWatchComplete} 
          className="mt-4 bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded w-full"
        >
          我已經看完影片了
        </button>
      </div>

      {videoWatched && (
        <div className="w-full">
          <h2 className="text-xl font-semibold mb-4">請回答以下問題：</h2>
          
          <div className="space-y-6">
            {/* 問題 1: 多選題 */}
            <div className="p-4 bg-white rounded-lg shadow">
              <label className="block mb-3 font-medium">{questions.q1.question}</label>
              <div className="grid grid-cols-2 gap-2">
                {questions.q1.options.map((option, index) => (
                  <div key={index} className="flex items-center">
                    <input
                      type="checkbox"
                      id={`q1-${index}`}
                      checked={answers.q1.includes(option)}
                      onChange={() => handleCheckboxChange('q1', option)}
                      disabled={submitted}
                      className="mr-2 h-4 w-4"
                    />
                    <label htmlFor={`q1-${index}`}>{option}</label>
                  </div>
                ))}
              </div>
              {submitted && (
                <p className={`text-sm mt-3 ${answers.q1.sort().join(',') === questions.q1.correctAnswer.sort().join(',') ? 'text-green-500' : 'text-red-500'}`}>
                  {answers.q1.sort().join(',') === questions.q1.correctAnswer.sort().join(',') 
                    ? '✓ 正確' 
                    : `✗ 正確答案: ${questions.q1.correctAnswer.join('、')}`}
                </p>
              )}
            </div>
            
            {/* 問題 2-5: 單選題 */}
            {['q2', 'q3', 'q4', 'q5'].map((qId) => (
              <div key={qId} className="p-4 bg-white rounded-lg shadow">
                <label className="block mb-3 font-medium">{questions[qId].question}</label>
                <div className="grid grid-cols-1 gap-2">
                  {questions[qId].options.map((option, index) => (
                    <div key={index} className="flex items-center">
                      <input
                        type="radio"
                        id={`${qId}-${index}`}
                        name={qId}
                        checked={answers[qId] === option}
                        onChange={() => handleRadioChange(qId, option)}
                        disabled={submitted}
                        className="mr-2 h-4 w-4"
                      />
                      <label htmlFor={`${qId}-${index}`}>{option}</label>
                    </div>
                  ))}
                </div>
                {submitted && (
                  <p className={`text-sm mt-3 ${answers[qId] === questions[qId].correctAnswer ? 'text-green-500' : 'text-red-500'}`}>
                    {answers[qId] === questions[qId].correctAnswer
                      ? '✓ 正確'
                      : `✗ 正確答案: ${questions[qId].correctAnswer}`}
                  </p>
                )}
              </div>
            ))}
            
            {!submitted ? (
              <button 
                onClick={handleSubmit} 
                disabled={!isAllAnswered()}
                className={`w-full py-2 px-4 rounded font-bold ${isAllAnswered() 
                  ? 'bg-green-500 hover:bg-green-600 text-white' 
                  : 'bg-gray-300 text-gray-500 cursor-not-allowed'}`}
              >
                {isAllAnswered() ? '提交答案' : '請回答所有問題'}
              </button>
            ) : (
              <div>
                <p className="text-lg font-bold text-center my-4 p-3 bg-blue-50 rounded">{result}</p>
                <button 
                  onClick={resetQuiz}
                  className="bg-purple-500 hover:bg-purple-600 text-white font-bold py-2 px-4 rounded w-full mt-2"
                >
                  再試一次
                </button>
              </div>
            )}
          </div>
        </div>
      )}
    </div>
  );
};

export default TaiwaneseAnimalQuiz;
