# Complete Source Code Collection - Write or Die

## 📁 File: src/components/CompletionScreen.jsx

```jsx
import React, { useState } from 'react';

const CompletionScreen = ({ sessionState, onNewSession }) => {
  const [refinedText, setRefinedText] = useState('');
  const [isRefining, setIsRefining] = useState(false);
  const [assessmentData, setAssessmentData] = useState(null);
  const [isAssessing, setIsAssessing] = useState(false);
  const [suggestions, setSuggestions] = useState(null);
  const [isGeneratingSuggestions, setIsGeneratingSuggestions] = useState(false);

  const {
    text,
    wordCount,
    totalDuration,
    actualWritingTime,
    currentSpeed,
    averageSpeed,
    topic
  } = sessionState;

  // Format duration for display
  const formatDuration = (seconds) => {
    const mins = Math.floor(seconds / 60);
    const secs = seconds % 60;
    if (mins === 0) return `${secs}s`;
    return `${mins}m ${secs}s`;
  };

  // Handle text refinement
  const handleRefineText = async () => {
    if (!text.trim()) {
      alert('No text to refine!');
      return;
    }

    setIsRefining(true);
    try {
      const apiKey = localStorage.getItem('openai_api_key');
      if (!apiKey) {
        alert('Please set your OpenAI API key in the settings first.');
        return;
      }

      const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
          model: 'gpt-3.5-turbo',
          messages: [
            {
              role: 'system',
              content: 'You are a professional writing assistant. Improve the grammar, clarity, and flow of the given text while preserving the author\'s voice and meaning. Return only the improved text without any explanations or comments.'
            },
            {
              role: 'user',
              content: text
            }
          ],
          max_tokens: 2000,
          temperature: 0.3
        })
      });

      if (!response.ok) {
        throw new Error(`API request failed: ${response.status}`);
      }

      const data = await response.json();
      setRefinedText(data.choices[0].message.content);
    } catch (error) {
      console.error('Error refining text:', error);
      alert('Failed to refine text. Please check your API key and try again.');
    } finally {
      setIsRefining(false);
    }
  };

  // Handle writing assessment
  const handleGetAssessment = async () => {
    if (!text.trim()) {
      alert('No text to assess!');
      return;
    }

    setIsAssessing(true);
    try {
      const apiKey = localStorage.getItem('openai_api_key');
      if (!apiKey) {
        alert('Please set your OpenAI API key in the settings first.');
        return;
      }

      const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
          model: 'gpt-3.5-turbo',
          messages: [
            {
              role: 'system',
              content: `You are a professional writing assessor. Analyze the given text and provide scores (0-100) for these four categories:
              1. Grammar & Mechanics (spelling, punctuation, sentence structure)
              2. Clarity & Coherence (logical flow, readability, organization)
              3. Creativity & Engagement (originality, voice, reader interest)
              4. Structure & Organization (paragraph structure, transitions, overall layout)
              
              Also provide:
              - Overall score (average of the four)
              - 2-3 specific strengths
              - 2-3 areas for improvement
              
              Return your response in this exact JSON format:
              {
                "overall_score": 75,
                "grammar_score": 80,
                "clarity_score": 70,
                "creativity_score": 75,
                "structure_score": 75,
                "strengths": ["strength 1", "strength 2"],
                "improvements": ["improvement 1", "improvement 2"]
              }`
            },
            {
              role: 'user',
              content: text
            }
          ],
          max_tokens: 1000,
          temperature: 0.2
        })
      });

      if (!response.ok) {
        throw new Error(`API request failed: ${response.status}`);
      }

      const data = await response.json();
      const assessmentText = data.choices[0].message.content;
      
      // Try to parse JSON from the response
      try {
        const jsonMatch = assessmentText.match(/\{[\s\S]*\}/);
        if (jsonMatch) {
          const assessment = JSON.parse(jsonMatch[0]);
          setAssessmentData(assessment);
        } else {
          throw new Error('No JSON found in response');
        }
      } catch (parseError) {
        console.error('Error parsing assessment:', parseError);
        alert('Failed to parse assessment data. Please try again.');
      }
    } catch (error) {
      console.error('Error getting assessment:', error);
      alert('Failed to get writing assessment. Please check your API key and try again.');
    } finally {
      setIsAssessing(false);
    }
  };

  // Handle improvement suggestions
  const handleGetSuggestions = async () => {
    if (!text.trim()) {
      alert('No text to analyze!');
      return;
    }

    setIsGeneratingSuggestions(true);
    try {
      const apiKey = localStorage.getItem('openai_api_key');
      if (!apiKey) {
        alert('Please set your OpenAI API key in the settings first.');
        return;
      }

      const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
          model: 'gpt-3.5-turbo',
          messages: [
            {
              role: 'system',
              content: `You are a professional writing coach. Analyze the given text and provide specific, actionable improvement suggestions. Focus on:
              
              1. Specific suggestions (2-3 concrete recommendations)
              2. Focus areas (1-2 main skills to develop)
              3. Next steps (1-2 immediate actions to take)
              
              Return your response in this exact JSON format:
              {
                "specific_suggestions": ["suggestion 1", "suggestion 2"],
                "focus_areas": ["Grammar & Mechanics", "Clarity & Focus"],
                "next_steps": ["step 1", "step 2"]
              }`
            },
            {
              role: 'user',
              content: text
            }
          ],
          max_tokens: 800,
          temperature: 0.3
        })
      });

      if (!response.ok) {
        throw new Error(`API request failed: ${response.status}`);
      }

      const data = await response.json();
      const suggestionsText = data.choices[0].message.content;
      
      // Try to parse JSON from the response
      try {
        const jsonMatch = suggestionsText.match(/\{[\s\S]*\}/);
        if (jsonMatch) {
          const suggestionsData = JSON.parse(jsonMatch[0]);
          setSuggestions(suggestionsData);
        } else {
          throw new Error('No JSON found in response');
        }
      } catch (parseError) {
        console.error('Error parsing suggestions:', parseError);
        alert('Failed to parse suggestions data. Please try again.');
      }
    } catch (error) {
      console.error('Error getting suggestions:', error);
      alert('Failed to get improvement suggestions. Please check your API key and try again.');
    } finally {
      setIsGeneratingSuggestions(false);
    }
  };

  // Copy text to clipboard
  const copyToClipboard = (textToCopy) => {
    navigator.clipboard.writeText(textToCopy).then(() => {
      alert('Text copied to clipboard!');
    }).catch(() => {
      alert('Failed to copy text to clipboard.');
    });
  };

  return (
    <div className="max-w-4xl mx-auto space-y-8">
      {/* Celebration Header */}
      <div className="text-center py-8">
        <div className="text-6xl mb-4">🎉</div>
        <h1 className="text-3xl font-bold text-green-600 mb-2">Session Complete!</h1>
        <p className="text-gray-600">Great job maintaining your writing momentum!</p>
      </div>

      {/* Session Summary */}
      <div className="bg-white rounded-lg shadow-lg p-6">
        <h2 className="text-xl font-semibold mb-4">Session Summary</h2>
        
        <div className="grid grid-cols-2 md:grid-cols-4 gap-4 mb-6">
          <div className="text-center p-4 bg-blue-50 rounded-lg">
            <div className="text-2xl font-bold text-blue-600">{wordCount}</div>
            <div className="text-sm text-gray-600">Words Written</div>
          </div>
          
          <div className="text-center p-4 bg-green-50 rounded-lg">
            <div className="text-2xl font-bold text-green-600">{averageSpeed}</div>
            <div className="text-sm text-gray-600">Average WPM</div>
          </div>
          
          <div className="text-center p-4 bg-purple-50 rounded-lg">
            <div className="text-2xl font-bold text-purple-600">{formatDuration(actualWritingTime)}</div>
            <div className="text-sm text-gray-600">Duration</div>
          </div>
          
          <div className="text-center p-4 bg-orange-50 rounded-lg">
            <div className="text-2xl font-bold text-orange-600">
              {Math.round((wordCount / (actualWritingTime / 60 || 1)) * 100) / 100}
            </div>
            <div className="text-sm text-gray-600">Words/Min</div>
          </div>
        </div>

        {topic && (
          <div className="mb-4">
            <strong>Topic:</strong> {topic}
          </div>
        )}
      </div>

      {/* Your Writing */}
      <div className="bg-white rounded-lg shadow-lg p-6">
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-xl font-semibold">Your Writing</h2>
          <div className="space-x-2">
            <button
              onClick={() => copyToClipboard(text)}
              className="px-4 py-2 bg-gray-500 text-white rounded hover:bg-gray-600"
            >
              📋 Copy
            </button>
            <button
              onClick={() => {
                const blob = new Blob([text], { type: 'text/plain' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `writing-session-${new Date().toISOString().split('T')[0]}.txt`;
                a.click();
                URL.revokeObjectURL(url);
              }}
              className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
            >
              📁 Export
            </button>
          </div>
        </div>
        
        <div className="bg-gray-50 p-4 rounded border max-h-64 overflow-y-auto">
          <p className="whitespace-pre-wrap">{text}</p>
        </div>
      </div>

      {/* AI Text Refinement */}
      <div className="bg-white rounded-lg shadow-lg p-6">
        <h2 className="text-xl font-semibold mb-4">AI Text Refinement</h2>
        
        <div className="grid md:grid-cols-2 gap-6">
          <div>
            <h3 className="font-medium mb-2">📝 Original Text</h3>
            <div className="bg-gray-50 p-4 rounded border h-48 overflow-y-auto">
              <p className="whitespace-pre-wrap text-sm">{text}</p>
            </div>
          </div>
          
          <div>
            <h3 className="font-medium mb-2">✨ AI-Refined Text</h3>
            <div className="bg-blue-50 p-4 rounded border h-48 overflow-y-auto">
              {refinedText ? (
                <p className="whitespace-pre-wrap text-sm">{refinedText}</p>
              ) : (
                <p className="text-gray-500 text-sm italic">Click "Refine Text" to see AI improvements</p>
              )}
            </div>
          </div>
        </div>
        
        <div className="mt-4 flex justify-between items-center">
          <button
            onClick={handleRefineText}
            disabled={isRefining}
            className="px-6 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 disabled:opacity-50"
          >
            {isRefining ? '🤖 Refining...' : '✨ Refine Text'}
          </button>
          
          {refinedText && (
            <div className="space-x-2">
              <span className="text-sm text-gray-600">💡 AI improvements: Grammar, clarity, and flow enhanced while preserving your voice</span>
              <button
                onClick={() => copyToClipboard(refinedText)}
                className="px-4 py-2 bg-gray-500 text-white rounded hover:bg-gray-600"
              >
                📋 Copy Refined
              </button>
            </div>
          )}
        </div>
      </div>

      {/* AI Writing Assessment */}
      <div className="bg-white rounded-lg shadow-lg p-6">
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-xl font-semibold">AI Writing Assessment</h2>
          <button
            onClick={handleGetAssessment}
            disabled={isAssessing}
            className="px-6 py-2 bg-purple-500 text-white rounded hover:bg-purple-600 disabled:opacity-50"
          >
            {isAssessing ? '🤖 Analyzing...' : '🎯 Get AI Score'}
          </button>
        </div>

        {assessmentData ? (
          <div>
            {/* Overall Score */}
            <div className="text-center mb-6">
              <div className="text-4xl font-bold text-purple-600 mb-2">
                {assessmentData.overall_score}/100
              </div>
              <div className="text-gray-600">Overall Writing Score</div>
            </div>

            {/* Category Scores */}
            <div className="grid md:grid-cols-4 gap-4 mb-6">
              <div className="text-center p-4 bg-blue-50 rounded-lg">
                <div className="text-2xl font-bold text-blue-600">{assessmentData.grammar_score}/100</div>
                <div className="text-sm text-gray-600">Grammar & Mechanics</div>
              </div>
              
              <div className="text-center p-4 bg-green-50 rounded-lg">
                <div className="text-2xl font-bold text-green-600">{assessmentData.clarity_score}/100</div>
                <div className="text-sm text-gray-600">Clarity & Coherence</div>
              </div>
              
              <div className="text-center p-4 bg-purple-50 rounded-lg">
                <div className="text-2xl font-bold text-purple-600">{assessmentData.creativity_score}/100</div>
                <div className="text-sm text-gray-600">Creativity & Engagement</div>
              </div>
              
              <div className="text-center p-4 bg-orange-50 rounded-lg">
                <div className="text-2xl font-bold text-orange-600">{assessmentData.structure_score}/100</div>
                <div className="text-sm text-gray-600">Structure & Organization</div>
              </div>
            </div>

            {/* Strengths and Improvements */}
            <div className="grid md:grid-cols-2 gap-6">
              <div className="bg-green-50 p-4 rounded-lg">
                <h3 className="font-semibold text-green-800 mb-2">✅ Strengths</h3>
                <ul className="space-y-1">
                  {assessmentData.strengths.map((strength, index) => (
                    <li key={index} className="text-green-700">• {strength}</li>
                  ))}
                </ul>
              </div>
              
              <div className="bg-amber-50 p-4 rounded-lg">
                <h3 className="font-semibold text-amber-800 mb-2">🎯 Areas for Growth</h3>
                <ul className="space-y-1">
                  {assessmentData.improvements.map((improvement, index) => (
                    <li key={index} className="text-amber-700">• {improvement}</li>
                  ))}
                </ul>
              </div>
            </div>
          </div>
        ) : (
          <div className="text-center py-8">
            <div className="text-4xl mb-4">📊</div>
            <p className="text-gray-600 mb-4">AI-powered writing assessment is ready!</p>
            <p className="text-sm text-gray-500">
              Get detailed scores on grammar, clarity, creativity, and structure based on credible writing criteria.
            </p>
          </div>
        )}
      </div>

      {/* AI Improvement Suggestions */}
      <div className="bg-white rounded-lg shadow-lg p-6">
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-xl font-semibold">AI Improvement Suggestions</h2>
          <button
            onClick={handleGetSuggestions}
            disabled={isGeneratingSuggestions}
            className="px-6 py-2 bg-green-500 text-white rounded hover:bg-green-600 disabled:opacity-50"
          >
            {isGeneratingSuggestions ? '🤖 Generating...' : '💡 Get Suggestions'}
          </button>
        </div>

        {suggestions ? (
          <div className="space-y-6">
            {/* Specific Suggestions */}
            <div className="bg-green-50 p-4 rounded-lg">
              <h3 className="font-semibold text-green-800 mb-3">📝 Specific Suggestions</h3>
              <ul className="space-y-2">
                {suggestions.specific_suggestions.map((suggestion, index) => (
                  <li key={index} className="text-green-700">• {suggestion}</li>
                ))}
              </ul>
            </div>

            {/* Focus Areas */}
            <div className="bg-blue-50 p-4 rounded-lg">
              <h3 className="font-semibold text-blue-800 mb-3">🎯 Focus Areas</h3>
              <div className="flex flex-wrap gap-2">
                {suggestions.focus_areas.map((area, index) => (
                  <span key={index} className="px-3 py-1 bg-blue-200 text-blue-800 rounded-full text-sm">
                    {area}
                  </span>
                ))}
              </div>
            </div>

            {/* Next Steps */}
            <div className="bg-purple-50 p-4 rounded-lg">
              <h3 className="font-semibold text-purple-800 mb-3">🚀 Next Steps</h3>
              <ul className="space-y-2">
                {suggestions.next_steps.map((step, index) => (
                  <li key={index} className="text-purple-700">• {step}</li>
                ))}
              </ul>
            </div>
          </div>
        ) : (
          <div className="text-center py-8">
            <div className="text-4xl mb-4">💡</div>
            <p className="text-gray-600 mb-4">AI-powered improvement suggestions are ready!</p>
            <p className="text-sm text-gray-500">
              Get specific, actionable advice to enhance your writing skills and develop your unique voice.
            </p>
          </div>
        )}
      </div>

      {/* Action Buttons */}
      <div className="flex justify-center space-x-4 pt-6">
        <button
          onClick={onNewSession}
          className="px-8 py-3 bg-blue-500 text-white rounded-lg font-semibold hover:bg-blue-600"
        >
          📝 Start New Session
        </button>
        
        <button
          onClick={() => {
            const exportData = {
              session: {
                wordCount,
                duration: formatDuration(actualWritingTime),
                averageWPM: averageSpeed,
                topic
              },
              originalText: text,
              refinedText: refinedText || null,
              assessment: assessmentData,
              suggestions: suggestions,
              timestamp: new Date().toISOString()
            };
            
            const blob = new Blob([JSON.stringify(exportData, null, 2)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `writing-session-complete-${new Date().toISOString().split('T')[0]}.json`;
            a.click();
            URL.revokeObjectURL(url);
          }}
          className="px-8 py-3 bg-green-500 text-white rounded-lg font-semibold hover:bg-green-600"
        >
          📊 Export Results
        </button>
      </div>
    </div>
  );
};

export default CompletionScreen;
```

## 📁 File: src/components/SessionSetup.jsx

```jsx
import React, { useState } from 'react';

const SessionSetup = ({ onStartSession }) => {
  const [duration, setDuration] = useState(15);
  const [warningLevel, setWarningLevel] = useState('moderate');
  const [topic, setTopic] = useState('');
  const [isGeneratingTopic, setIsGeneratingTopic] = useState(false);

  const warningLevels = [
    { value: 'none', label: 'None (Zen Mode)', description: 'No interruptions, pure focus' },
    { value: 'gentle', label: 'Gentle', description: 'Subtle visual cues' },
    { value: 'moderate', label: 'Moderate', description: 'Balanced feedback' },
    { value: 'strict', label: 'Strict', description: 'Active monitoring' }
  ];

  const generateAITopic = async () => {
    setIsGeneratingTopic(true);
    try {
      const apiKey = localStorage.getItem('openai_api_key');
      if (!apiKey) {
        alert('Please set your OpenAI API key first. You can get one from https://platform.openai.com/api-keys');
        setIsGeneratingTopic(false);
        return;
      }

      const response = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${apiKey}`
        },
        body: JSON.stringify({
          model: 'gpt-3.5-turbo',
          messages: [
            {
              role: 'system',
              content: 'You are a creative writing prompt generator. Generate an interesting, specific writing prompt that will inspire creative writing. The prompt should be engaging, clear, and suitable for a timed writing session. Return only the prompt text, nothing else.'
            },
            {
              role: 'user',
              content: 'Generate a creative writing prompt for a timed writing session.'
            }
          ],
          max_tokens: 100,
          temperature: 0.8
        })
      });

      if (!response.ok) {
        throw new Error(`API request failed: ${response.status}`);
      }

      const data = await response.json();
      const generatedTopic = data.choices[0].message.content.trim();
      setTopic(generatedTopic);
    } catch (error) {
      console.error('Error generating topic:', error);
      alert('Failed to generate topic. Please check your API key and try again.');
    } finally {
      setIsGeneratingTopic(false);
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    onStartSession({
      duration,
      warningLevel,
      topic: topic.trim()
    });
  };

  const handleAPIKeySetup = () => {
    const apiKey = prompt('Enter your OpenAI API key:');
    if (apiKey) {
      localStorage.setItem('openai_api_key', apiKey.trim());
      alert('API key saved! You can now use AI features.');
    }
  };

  const hasAPIKey = !!localStorage.getItem('openai_api_key');

  return (
    <div className="max-w-2xl mx-auto">
      <div className="bg-white rounded-lg shadow-lg p-8">
        <div className="text-center mb-8">
          <h1 className="text-3xl font-bold mb-2">⏰ Write or Die</h1>
          <p className="text-gray-600">
            Overcome writer's block with timed writing sessions and AI-powered assistance
          </p>
        </div>

        <form onSubmit={handleSubmit} className="space-y-6">
          {/* Duration Selection */}
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-2">
              Session Duration
            </label>
            <div className="grid grid-cols-3 md:grid-cols-6 gap-2">
              {[1, 3, 5, 10, 15, 20, 25, 30, 45, 60].map((mins) => (
                <button
                  key={mins}
                  type="button"
                  onClick={() => setDuration(mins)}
                  className={`p-3 text-sm font-medium rounded-lg border ${
                    duration === mins
                      ? 'bg-blue-500 text-white border-blue-500'
                      : 'bg-white text-gray-700 border-gray-300 hover:bg-gray-50'
                  }`}
                >
                  {mins}m
                </button>
              ))}
            </div>
            <div className="mt-2">
              <input
                type="range"
                min="1"
                max="60"
                value={duration}
                onChange={(e) => setDuration(parseInt(e.target.value))}
                className="w-full"
              />
              <div className="text-center text-sm text-gray-600 mt-1">
                {duration} minute{duration !== 1 ? 's' : ''}
              </div>
            </div>
          </div>

          {/* Warning Level */}
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-2">
              Warning Level
            </label>
            <div className="space-y-2">
              {warningLevels.map((level) => (
                <label key={level.value} className="flex items-center">
                  <input
                    type="radio"
                    name="warningLevel"
                    value={level.value}
                    checked={warningLevel === level.value}
                    onChange={(e) => setWarningLevel(e.target.value)}
                    className="mr-3"
                  />
                  <div>
                    <div className="font-medium">{level.label}</div>
                    <div className="text-sm text-gray-600">{level.description}</div>
                  </div>
                </label>
              ))}
            </div>
          </div>

          {/* Topic Section */}
          <div>
            <label className="block text-sm font-medium text-gray-700 mb-2">
              Writing Topic (Optional)
            </label>
            
            {!hasAPIKey && (
              <div className="mb-4 p-4 bg-blue-50 border border-blue-200 rounded-lg">
                <div className="flex items-center justify-between">
                  <div>
                    <h3 className="font-medium text-blue-800">🤖 Enable AI Features</h3>
                    <p className="text-sm text-blue-600">
                      Set up your OpenAI API key to use AI topic generation and text refinement
                    </p>
                  </div>
                  <button
                    type="button"
                    onClick={handleAPIKeySetup}
                    className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 text-sm"
                  >
                    Setup API Key
                  </button>
                </div>
              </div>
            )}

            <div className="flex space-x-2">
              <textarea
                value={topic}
                onChange={(e) => setTopic(e.target.value)}
                placeholder="Enter your writing topic or generate one with AI..."
                className="flex-1 p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                rows="3"
              />
              
              {hasAPIKey && (
                <button
                  type="button"
                  onClick={generateAITopic}
                  disabled={isGeneratingTopic}
                  className="px-4 py-2 bg-purple-500 text-white rounded-lg hover:bg-purple-600 disabled:opacity-50 whitespace-nowrap"
                >
                  {isGeneratingTopic ? '🤖 Generating...' : '🎲 AI Topic'}
                </button>
              )}
            </div>
            
            <div className="mt-2 text-sm text-gray-600">
              Leave blank to write freely, or use a topic to guide your session
            </div>
          </div>

          {/* Start Button */}
          <button
            type="submit"
            className="w-full py-4 bg-green-500 text-white font-semibold rounded-lg hover:bg-green-600 transition-colors"
          >
            🚀 Start Writing Session
          </button>
        </form>

        {/* Features Preview */}
        <div className="mt-8 pt-6 border-t border-gray-200">
          <h3 className="font-semibold mb-3">✨ What you'll get:</h3>
          <div className="grid md:grid-cols-2 gap-4 text-sm">
            <div className="space-y-2">
              <div className="flex items-center">
                <span className="text-green-500 mr-2">✓</span>
                Real-time WPM tracking
              </div>
              <div className="flex items-center">
                <span className="text-green-500 mr-2">✓</span>
                Session extensions
              </div>
              <div className="flex items-center">
                <span className="text-green-500 mr-2">✓</span>
                Writing momentum alerts
              </div>
            </div>
            <div className="space-y-2">
              <div className="flex items-center">
                <span className="text-purple-500 mr-2">🤖</span>
                AI text refinement
              </div>
              <div className="flex items-center">
                <span className="text-purple-500 mr-2">🤖</span>
                Writing assessment
              </div>
              <div className="flex items-center">
                <span className="text-purple-500 mr-2">🤖</span>
                Improvement suggestions
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
};

export default SessionSetup;
```

## 📁 File: src/components/SessionStats.jsx

```jsx
import React from 'react';

const SessionStats = ({ sessionState }) => {
  const {
    wordCount,
    currentSpeed,
    averageSpeed,
    timeRemaining,
    totalDuration,
    actualWritingTime
  } = sessionState;

  const formatTime = (seconds) => {
    const mins = Math.floor(seconds / 60);
    const secs = seconds % 60;
    return `${mins}m ${secs}s`;
  };

  const progress = ((totalDuration * 60 - timeRemaining) / (totalDuration * 60)) * 100;

  return (
    <div className="bg-white rounded-lg shadow-lg p-6">
      <h3 className="text-lg font-semibold mb-4">Session Stats</h3>
      
      <div className="grid grid-cols-2 md:grid-cols-4 gap-4 mb-4">
        <div className="text-center p-3 bg-blue-50 rounded-lg">
          <div className="text-xl font-bold text-blue-600">{wordCount}</div>
          <div className="text-xs text-gray-600">Words</div>
        </div>
        
        <div className="text-center p-3 bg-green-50 rounded-lg">
          <div className="text-xl font-bold text-green-600">{currentSpeed}</div>
          <div className="text-xs text-gray-600">Current WPM</div>
        </div>
        
        <div className="text-center p-3 bg-purple-50 rounded-lg">
          <div className="text-xl font-bold text-purple-600">{averageSpeed}</div>
          <div className="text-xs text-gray-600">Average WPM</div>
        </div>
        
        <div className="text-center p-3 bg-orange-50 rounded-lg">
          <div className="text-xl font-bold text-orange-600">{formatTime(actualWritingTime)}</div>
          <div className="text-xs text-gray-600">Writing Time</div>
        </div>
      </div>
      
      <div className="mb-2">
        <div className="flex justify-between text-sm text-gray-600 mb-1">
          <span>Progress</span>
          <span>{Math.round(progress)}%</span>
        </div>
        <div className="w-full bg-gray-200 rounded-full h-2">
          <div 
            className="bg-blue-600 h-2 rounded-full transition-all duration-300"
            style={{ width: `${progress}%` }}
          />
        </div>
      </div>
    </div>
  );
};

export default SessionStats;
```

## 📁 File: src/components/WritingEditor.jsx

```jsx
import React, { useRef, useEffect } from 'react';

const WritingEditor = ({ 
  text, 
  onChange, 
  isActive, 
  isPaused, 
  topic, 
  isWarning, 
  warningLevel 
}) => {
  const textareaRef = useRef(null);

  useEffect(() => {
    if (textareaRef.current && isActive && !isPaused) {
      textareaRef.current.focus();
    }
  }, [isActive, isPaused]);

  const getWarningStyles = () => {
    if (!isWarning || warningLevel === 'none') return '';
    
    switch (warningLevel) {
      case 'gentle':
        return 'border-yellow-300 bg-yellow-50';
      case 'moderate':
        return 'border-orange-400 bg-orange-50';
      case 'strict':
        return 'border-red-500 bg-red-50 animate-pulse';
      default:
        return '';
    }
  };

  const getPlaceholderText = () => {
    if (topic) {
      return `Start writing about: ${topic}...`;
    }
    return "Start writing about: Write about a moment when a simple act of kindness from a stranger deeply impacted your day...";
  };

  return (
    <div className="space-y-4">
      {topic && (
        <div className="bg-blue-50 border border-blue-200 rounded-lg p-4">
          <h3 className="font-medium text-blue-800 mb-1">📝 Topic:</h3>
          <p className="text-blue-700">{topic}</p>
        </div>
      )}
      
      {isWarning && warningLevel !== 'none' && (
        <div className="bg-yellow-100 border border-yellow-400 rounded-lg p-3">
          <div className="flex items-center">
            <span className="text-yellow-600 mr-2">⚠️</span>
            <span className="text-yellow-800 font-medium">
              Keep writing! Your pace has slowed down.
            </span>
          </div>
        </div>
      )}
      
      <div className="relative">
        <textarea
          ref={textareaRef}
          value={text}
          onChange={(e) => onChange(e.target.value)}
          placeholder={getPlaceholderText()}
          disabled={!isActive || isPaused}
          className={`w-full h-96 p-4 border rounded-lg resize-none focus:ring-2 focus:ring-blue-500 focus:border-transparent ${
            getWarningStyles()
          } ${
            !isActive || isPaused 
              ? 'bg-gray-100 cursor-not-allowed' 
              : 'bg-white'
          }`}
        />
        
        {isPaused && (
          <div className="absolute inset-0 bg-gray-900 bg-opacity-50 flex items-center justify-center rounded-lg">
            <div className="bg-white p-4 rounded-lg text-center">
              <div className="text-2xl mb-2">⏸️</div>
              <div className="font-medium">Session Paused</div>
              <div className="text-sm text-gray-600">Click Resume to continue</div>
            </div>
          </div>
        )}
      </div>
      
      <div className="text-sm text-gray-600 text-center">
        {isActive && !isPaused ? (
          "Keep writing! Don't stop to edit or think too much."
        ) : !isActive ? (
          "Session not started. Click Start to begin writing."
        ) : (
          "Session paused. Click Resume to continue."
        )}
      </div>
    </div>
  );
};

export default WritingEditor;
```

## 📁 File: src/main.jsx

```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

## 📁 File: src/index.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, 'Courier New',
    monospace;
}

/* Custom animations for warning states */
@keyframes gentle-pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.8; }
}

@keyframes moderate-shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-2px); }
  75% { transform: translateX(2px); }
}

@keyframes strict-flash {
  0%, 100% { background-color: rgb(254 242 242); }
  50% { background-color: rgb(254 226 226); }
}

.warning-gentle {
  animation: gentle-pulse 2s infinite;
}

.warning-moderate {
  animation: moderate-shake 0.5s infinite;
}

.warning-strict {
  animation: strict-flash 0.5s infinite;
}

/* Smooth transitions */
.transition-all {
  transition: all 0.3s ease;
}

/* Focus states */
textarea:focus {
  outline: none;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #a1a1a1;
}
```

## 📁 File: public/index.html

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Write or Die - AI-Enhanced Writing Productivity</title>
    <meta name="description" content="Overcome writer's block with timed writing sessions and AI-powered assistance. Write faster, write better, write consistently." />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

