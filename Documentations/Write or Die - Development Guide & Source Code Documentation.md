# Write or Die - Development Guide & Source Code Documentation

## 📁 Project Structure

```
write-or-die/
├── public/
│   ├── index.html
│   └── vite.svg
├── src/
│   ├── components/
│   │   ├── Timer.jsx                    # Main timer component
│   │   ├── SessionSetup.jsx             # Session configuration
│   │   ├── SessionStats.jsx             # Real-time statistics
│   │   ├── WritingEditor.jsx            # Text editor component
│   │   └── CompletionScreen.jsx         # Post-session AI features
│   ├── hooks/
│   │   └── useWritingSession.js         # Main state management hook
│   ├── services/
│   │   └── aiService.js                 # OpenAI API integration
│   ├── App.jsx                          # Main application component
│   ├── main.jsx                         # React entry point
│   └── index.css                        # Tailwind CSS styles
├── package.json                         # Dependencies and scripts
├── vite.config.js                       # Vite build configuration
├── tailwind.config.js                   # Tailwind CSS configuration
├── postcss.config.js                    # PostCSS configuration
└── README.md                            # Project documentation
```

## 🛠️ Technology Stack & Dependencies

### Core Technologies
- **React 18.2.0** - Frontend framework
- **Vite 4.4.5** - Build tool and dev server
- **Tailwind CSS 3.3.0** - Utility-first CSS framework

### Key Dependencies (package.json)
```json
{
  "name": "write-or-die",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint . --ext js,jsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.15",
    "@types/react-dom": "^18.2.7",
    "@vitejs/plugin-react": "^4.0.3",
    "autoprefixer": "^10.4.14",
    "eslint": "^8.45.0",
    "eslint-plugin-react": "^7.32.2",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.3",
    "postcss": "^8.4.27",
    "tailwindcss": "^3.3.0",
    "vite": "^4.4.5"
  }
}
```

## 🖥️ Mac Terminal Commands & Compilation

### Initial Setup
```bash
# Create new Vite React project
npm create vite@latest write-or-die -- --template react
cd write-or-die

# Install dependencies
npm install

# Install Tailwind CSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Development Commands
```bash
# Start development server (http://localhost:5173)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Lint code
npm run lint
```

### Build Output
- Development: Runs on `http://localhost:5173`
- Production: Creates `dist/` folder with minified files
- Deploy: Upload `dist/` contents to static hosting (GitHub Pages, Netlify, etc.)

## 📝 Core Source Code Files

### 1. App.jsx - Main Application Component
```jsx
import React, { useState } from 'react';
import SessionSetup from './components/SessionSetup';
import Timer from './components/Timer';
import SessionStats from './components/SessionStats';
import WritingEditor from './components/WritingEditor';
import CompletionScreen from './components/CompletionScreen';
import useWritingSession from './hooks/useWritingSession';

function App() {
  const [view, setView] = useState('setup');
  
  const {
    sessionState,
    startSession,
    toggleSession,
    stopSession,
    finishSession,
    updateText,
    updateTimer,
    completeSession,
    startNewSession,
    handleTimerTick,
    extendSession
  } = useWritingSession();

  // Handle session start
  const handleStartSession = (config) => {
    startSession(config);
    setView('writing');
  };

  // Handle new session
  const handleNewSession = () => {
    startNewSession();
    setView('setup');
  };

  // Handle session completion (timer ends naturally)
  const handleSessionComplete = () => {
    completeSession();
    setView('completed');
  };

  // Handle finish session (user clicks FINISH button during writing)
  const handleFinishSession = () => {
    finishSession();
    setView('completed');
  };

  // Handle timer completion (show continue prompt)
  const handleTimerComplete = () => {
    console.log('Timer completed! Showing continue prompt');
    setView('continue-prompt');
  };

  // Handle session extension (user chooses specific minutes to continue)
  const handleExtendSession = (minutes) => {
    updateTimer(minutes * 60); // Convert minutes to seconds
    setView('writing');
  };

  // Handle stop session
  const handleStopSession = () => {
    stopSession();
    setView('setup');
  };

  // Render current view
  const renderCurrentView = () => {
    switch (view) {
      case 'setup':
        return <SessionSetup onStartSession={handleStartSession} />;
      
      case 'loading':
        return (
          <div className="max-w-2xl mx-auto p-6 text-center">
            <div className="mb-6">
              <div className="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-500 mx-auto"></div>
            </div>
            <h2 className="text-xl font-semibold mb-2">🤖 Generating AI Topic...</h2>
            <p className="text-gray-600">
              Finding an interesting writing topic for you. This may take a few seconds.
            </p>
          </div>
        );
      
      case 'writing':
        return (
          <div className="space-y-6">
            {/* Timer */}
            <Timer
              timeRemaining={sessionState.timeRemaining}
              totalDuration={sessionState.totalDuration}
              isActive={sessionState.isActive}
              isPaused={sessionState.isPaused}
              onTick={handleTimerTick}
              onComplete={handleTimerComplete}
              onToggle={toggleSession}
              onStop={handleStopSession}
              onFinish={handleFinishSession}
            />
            
            {/* Session Stats */}
            {sessionState.isActive && (
              <SessionStats sessionState={sessionState} />
            )}
            
            {/* Writing Editor */}
            <WritingEditor
              text={sessionState.text}
              onChange={updateText}
              isActive={sessionState.isActive}
              isPaused={sessionState.isPaused}
              topic={sessionState.topic}
              isWarning={sessionState.isWarning}
              warningLevel={sessionState.warningLevel}
            />
          </div>
        );
      
      case 'continue-prompt':
        return (
          <div className="max-w-2xl mx-auto p-6 text-center">
            <div className="mb-6">
              <div className="text-6xl mb-4">⏰</div>
              <h2 className="text-2xl font-bold mb-2">Time's Up!</h2>
              <p className="text-gray-600 mb-6">
                Great job! You've completed your writing session. Would you like to continue writing to finish your thoughts?
              </p>
              
              <div className="grid grid-cols-2 md:grid-cols-4 gap-3 mb-6">
                <button
                  onClick={() => handleExtendSession(1)}
                  className="px-4 py-3 bg-blue-500 text-white rounded-lg font-semibold hover:bg-blue-600 text-sm"
                >
                  +1 min
                </button>
                
                <button
                  onClick={() => handleExtendSession(3)}
                  className="px-4 py-3 bg-blue-500 text-white rounded-lg font-semibold hover:bg-blue-600 text-sm"
                >
                  +3 min
                </button>
                
                <button
                  onClick={() => handleExtendSession(5)}
                  className="px-4 py-3 bg-blue-500 text-white rounded-lg font-semibold hover:bg-blue-600 text-sm"
                >
                  +5 min
                </button>
                
                <button
                  onClick={handleSessionComplete}
                  className="px-4 py-3 bg-green-500 text-white rounded-lg font-semibold hover:bg-green-600 text-sm"
                >
                  ✅ Finish
                </button>
              </div>
              
              <div className="text-sm text-gray-500">
                Choose a short extension to wrap up your thoughts, or finish to see your results and AI features.
              </div>
            </div>
          </div>
        );
      
      case 'completed':
        return (
          <CompletionScreen
            sessionState={sessionState}
            onNewSession={handleNewSession}
          />
        );
      
      default:
        return <SessionSetup onStartSession={handleStartSession} />;
    }
  };

  return (
    <div className="min-h-screen bg-gray-100">
      {/* Header */}
      <header className="bg-white shadow-sm border-b">
        <div className="max-w-7xl mx-auto px-4 py-4">
          <div className="flex justify-between items-center">
            <div className="flex items-center space-x-2">
              <h1 className="text-xl font-bold">⏰ Write or Die</h1>
              {view !== 'setup' && (
                <span className="text-sm text-gray-500">
                  {view === 'writing' ? 'Writing Session' : 
                   view === 'completed' ? 'Session Complete' : 
                   view === 'continue-prompt' ? 'Session Complete' : ''}
                </span>
              )}
            </div>
            <div className="flex items-center space-x-4">
              <span className="text-sm text-green-600 font-medium">🤖 AI: Ready</span>
            </div>
          </div>
        </div>
      </header>

      {/* Main Content */}
      <main className="max-w-7xl mx-auto px-4 py-6">
        {renderCurrentView()}
      </main>

      {/* Footer */}
      <footer className="bg-white border-t mt-12">
        <div className="max-w-7xl mx-auto px-4 py-6">
          <div className="text-center text-sm text-gray-500">
            <p>🤖 AI Enhanced | Complete Writing Productivity Suite</p>
          </div>
        </div>
      </footer>
    </div>
  );
}

export default App;
```

### 2. useWritingSession.js - Core State Management Hook
```javascript
import { useState, useRef, useCallback } from 'react';

const useWritingSession = () => {
  const [sessionState, setSessionState] = useState({
    isActive: false,
    isPaused: false,
    isCompleted: false,
    duration: 15, // minutes - initial session length
    totalDuration: 15, // minutes - cumulative duration after extensions
    timeRemaining: 15 * 60, // seconds - current countdown
    startTime: null, // when session started
    actualWritingTime: 0, // seconds - real time spent writing
    text: '',
    wordCount: 0,
    currentSpeed: 0,
    averageSpeed: 0,
    warningLevel: 'moderate',
    topic: '',
    isWarning: false
  });

  const speedMeasurements = useRef([]);
  const lastTypingTime = useRef(Date.now());
  const sessionStartTime = useRef(null);

  // Calculate writing speed
  const calculateSpeed = useCallback((wordCount) => {
    const now = Date.now();
    
    // Add measurement
    speedMeasurements.current.push({
      timestamp: now,
      wordCount: wordCount
    });
    
    // Keep only last 30 seconds of measurements
    const thirtySecondsAgo = now - 30000;
    speedMeasurements.current = speedMeasurements.current.filter(
      m => m.timestamp >= thirtySecondsAgo
    );
    
    // Calculate current WPM
    let currentWPM = 0;
    if (speedMeasurements.current.length >= 2) {
      const oldest = speedMeasurements.current[0];
      const newest = speedMeasurements.current[speedMeasurements.current.length - 1];
      
      const wordsDiff = newest.wordCount - oldest.wordCount;
      const timeDiff = (newest.timestamp - oldest.timestamp) / 1000 / 60; // minutes
      
      currentWPM = timeDiff > 0 ? Math.round(wordsDiff / timeDiff) : 0;
    }
    
    // Calculate average WPM for entire session
    let averageWPM = 0;
    if (sessionStartTime.current && wordCount > 0) {
      const sessionDuration = (now - sessionStartTime.current) / 1000 / 60; // minutes
      averageWPM = sessionDuration > 0 ? Math.round(wordCount / sessionDuration) : 0;
    }
    
    setSessionState(prev => ({
      ...prev,
      currentSpeed: currentWPM,
      averageSpeed: averageWPM
    }));
    
    return { currentWPM, averageWPM };
  }, []);

  // Check warning conditions
  const checkWarningConditions = useCallback((currentWPM) => {
    const now = Date.now();
    const timeSinceLastTyping = now - lastTypingTime.current;
    
    // Trigger warning if speed is too low or no typing for 15 seconds
    const shouldWarn = currentWPM < 10 || timeSinceLastTyping > 15000;
    
    setSessionState(prev => ({
      ...prev,
      isWarning: shouldWarn && prev.isActive && !prev.isPaused && prev.warningLevel !== 'none'
    }));
    
    return shouldWarn;
  }, []);

  // Start a new session
  const startSession = useCallback((config) => {
    const now = Date.now();
    sessionStartTime.current = now;
    lastTypingTime.current = now;
    speedMeasurements.current = [];
    
    // Handle initial text (e.g., from AI outline generation)
    const initialText = config.initialText || '';
    const initialWordCount = initialText.trim() === '' ? 0 : initialText.trim().split(/\s+/).length;
    
    setSessionState(prev => ({
      ...prev,
      isActive: true,
      isPaused: false,
      isCompleted: false,
      duration: config.duration,
      totalDuration: config.duration,
      timeRemaining: config.duration * 60,
      startTime: now,
      actualWritingTime: 0,
      warningLevel: config.warningLevel,
      topic: config.topic || '',
      text: initialText,
      wordCount: initialWordCount,
      currentSpeed: 0,
      averageSpeed: 0,
      isWarning: false,
      generatedOutline: config.generatedOutline || null
    }));
  }, []);

  // Complete session (timer finished)
  const completeSession = useCallback(() => {
    setSessionState(prev => ({
      ...prev,
      isActive: false,
      isPaused: false,
      isCompleted: true,
      isWarning: false
    }));
  }, []);

  // Finish session early (user chooses to finish)
  const finishSession = useCallback(() => {
    setSessionState(prev => ({
      ...prev,
      isActive: false,
      isPaused: false,
      isCompleted: true,
      isWarning: false
    }));
  }, []);

  // Pause/resume session
  const toggleSession = useCallback(() => {
    setSessionState(prev => ({
      ...prev,
      isPaused: !prev.isPaused,
      isWarning: false // Clear warnings when pausing
    }));
  }, []);

  // Stop session (reset to setup) - PRESERVES TEXT
  const stopSession = useCallback(() => {
    setSessionState(prev => ({
      ...prev,
      isActive: false,
      isPaused: false,
      isCompleted: false,
      isWarning: false
      // NOTE: Removed text clearing to preserve user's work
    }));
    
    sessionStartTime.current = null;
    speedMeasurements.current = [];
  }, []);

  // Update text content
  const updateText = useCallback((newText) => {
    const wordCount = newText.trim() === '' ? 0 : newText.trim().split(/\s+/).length;
    const now = Date.now();
    
    setSessionState(prev => {
      // Calculate actual writing time using totalDuration and timeRemaining
      const timeSpent = (prev.totalDuration * 60) - prev.timeRemaining; // seconds
      const actualWritingTime = timeSpent;
      
      return {
        ...prev,
        text: newText,
        wordCount: wordCount,
        actualWritingTime: actualWritingTime
      };
    });
    
    lastTypingTime.current = now;
    
    // Calculate speed and check warnings
    const { currentWPM } = calculateSpeed(wordCount);
    checkWarningConditions(currentWPM);
  }, [calculateSpeed, checkWarningConditions]);

  // Update timer (for extensions)
  const updateTimer = useCallback((additionalSeconds) => {
    setSessionState(prev => ({
      ...prev,
      timeRemaining: prev.timeRemaining + additionalSeconds,
      totalDuration: prev.totalDuration + (additionalSeconds / 60)    
    }));
  }, []);

  // Start new session (reset everything)
  const startNewSession = useCallback(() => {
    setSessionState(prev => ({
      ...prev,
      isCompleted: false,
      text: '',
      wordCount: 0,
      currentSpeed: 0,
      averageSpeed: 0,
      topic: ''
    }));
    
    sessionStartTime.current = null;
    speedMeasurements.current = [];
  }, []);

  // Handle timer tick - called every second
  const handleTimerTick = useCallback(() => {
    setSessionState(prev => {
      if (!prev.isActive || prev.isPaused || prev.timeRemaining <= 0) {
        return prev;
      }
      return {
        ...prev,
        timeRemaining: prev.timeRemaining - 1
      };
    });
  }, []);

  // Handle session extension
  const extendSession = useCallback((additionalMinutes) => {
    setSessionState(prev => ({
      ...prev,
      totalDuration: prev.totalDuration + additionalMinutes,
      timeRemaining: prev.timeRemaining + (additionalMinutes * 60)
    }));
  }, []);

  return {
    sessionState,
    startSession,
    toggleSession,
    stopSession,
    finishSession,
    updateText,
    updateTimer,
    completeSession,
    startNewSession,
    handleTimerTick,
    extendSession
  };
};

export default useWritingSession;
```

### 3. Timer.jsx - Timer Component
```jsx
import { useEffect, useRef } from 'react';

const Timer = ({ 
  timeRemaining,
  totalDuration, 
  isActive, 
  isPaused,
  onTick, // Called every second when active
  onComplete, // Called when timer reaches 0
  onToggle,
  onStop,
  onFinish
}) => {
  const intervalRef = useRef(null);

  // Timer logic - purely functional, no internal state
  useEffect(() => {
    if (intervalRef.current) {
      clearInterval(intervalRef.current);
      intervalRef.current = null;
    }

    if (isActive && !isPaused && timeRemaining > 0) {
      intervalRef.current = setInterval(() => {
        if (onTick) onTick(); // Just notify parent to decrement
      }, 1000);
    }

    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current);
        intervalRef.current = null;
      }
    };
  }, [isActive, isPaused, onTick]);

  // Check for completion
  useEffect(() => {
    if (isActive && timeRemaining <= 0 && onComplete) {
      onComplete();
    }
  }, [timeRemaining, isActive, onComplete]);

  const formatTime = (seconds) => {
    const mins = Math.floor(seconds / 60);
    const secs = seconds % 60;
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
  };

  const getTimerColor = () => {
    const percentage = (timeRemaining / (totalDuration * 60)) * 100;
    if (percentage > 50) return 'text-green-600';
    if (percentage > 25) return 'text-amber-600';
    return 'text-red-600';
  };

  const handleToggle = () => {
    if (onToggle) onToggle();
  };

  const handleStop = () => {
    if (intervalRef.current) {
      clearInterval(intervalRef.current);
      intervalRef.current = null;
    }
    if (onStop) onStop();
  };

  return (
    <div className="w-full max-w-md mx-auto bg-white rounded-lg shadow-lg p-3">
      <div className="text-center">
        <div className="flex items-center justify-center mb-2">
          <h3 className="text-md font-semibold">Writing Timer</h3>
        </div>
        
        <div className={`text-3xl font-mono font-bold mb-3 ${getTimerColor()}`}>
          {formatTime(timeRemaining)}
        </div>
        
        <div className="w-full bg-gray-200 rounded-full h-1 mb-3">
          <div 
            className="bg-blue-600 h-1 rounded-full transition-all duration-1000"
            style={{ 
              width: `${((totalDuration * 60 - timeRemaining) / (totalDuration * 60)) * 100}%` 
            }}
          />
        </div>
        
        <div className="flex justify-center space-x-2">
          <button
            onClick={handleToggle}
            className={`px-4 py-2 rounded ${
              isActive && !isPaused ? 'bg-gray-500' : 'bg-blue-500'
            } text-white hover:opacity-80`}
          >
            {isActive && !isPaused ? 'Pause' : (isPaused ? 'Resume' : 'Start')}
          </button>
          
          {isActive && onFinish && (
            <button
              onClick={onFinish}
              className="px-4 py-2 rounded bg-green-500 text-white hover:opacity-80"
            >
              Finish
            </button>
          )}
          
          <button
            onClick={handleStop}
            className="px-4 py-2 rounded bg-red-500 text-white hover:opacity-80"
          >
            Stop
          </button>
        </div>
        
        <div className="mt-4 text-sm text-gray-600">
          Session: {totalDuration} minutes
          {isPaused && <span className="ml-2 text-amber-600">(Paused)</span>}
        </div>
      </div>
    </div>
  );
};

export default Timer;
```

## 🔧 Configuration Files

### vite.config.js
```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  base: '/write-or-die/', // For GitHub Pages deployment
})
```

### tailwind.config.js
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

### postcss.config.js
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

## 🐛 Key Bug Fixes Applied

### 1. Timer Extension Bug
**Problem**: Extensions only added 1 second instead of 60 seconds
**Fix**: Changed `updateTimer(minutes)` to `updateTimer(minutes * 60)` in App.jsx

### 2. WPM Calculation Bug  
**Problem**: Showed impossible 240 WPM due to wrong time calculation
**Fix**: Updated actualWritingTime calculation to use `totalDuration` instead of `duration`

### 3. Finish Button Bug
**Problem**: Finish button didn't show completion screen
**Fix**: Changed Timer's `onFinish={finishSession}` to `onFinish={handleFinishSession}`

### 4. CompletionScreen Crash
**Problem**: "duration is not defined" error
**Fix**: Updated WPM calculation to use `actualWritingTime / 60` instead of undefined `duration`

## 🚀 Deployment Process

### Local Development
```bash
npm run dev
# Runs on http://localhost:5173
```

### Production Build
```bash
npm run build
# Creates dist/ folder with minified files
```

### GitHub Pages Deployment
1. Build the project: `npm run build`
2. Upload contents of `dist/` folder to GitHub Pages repository
3. Enable GitHub Pages in repository settings
4. Access at: `https://username.github.io/write-or-die/`

## 🔒 Architecture Notes

### Privacy-First Design
- **Client-side only**: No backend server
- **Direct API calls**: Browser → OpenAI API (no intermediary)
- **Local storage**: All user data stays in browser
- **No tracking**: Developer cannot access user content

### State Management
- **Single source of truth**: useWritingSession hook
- **Immutable updates**: Using React's functional setState
- **Proper cleanup**: useEffect cleanup for timers and intervals

### Performance Optimizations
- **useCallback**: Prevents unnecessary re-renders
- **useRef**: For values that don't trigger re-renders
- **Conditional rendering**: Only render active components

## 📚 Learning Outcomes

### React Concepts Applied
- Functional components with hooks
- State management with useState
- Side effects with useEffect
- Performance optimization with useCallback/useRef
- Component composition and prop passing

### JavaScript Concepts
- Async/await for API calls
- Timer management with setInterval
- Event handling and user interactions
- Local storage for persistence

### CSS/Styling
- Tailwind CSS utility classes
- Responsive design principles
- Component-based styling
- Animation and transitions

This documentation provides a complete overview of the Write or Die application architecture, source code, and development methodology for continued development and educational purposes.

