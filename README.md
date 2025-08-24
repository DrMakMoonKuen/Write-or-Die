# Write or Die - Production Release

A modern, AI-powered writing application that helps writers overcome writer's block and improve their craft through timed writing sessions and intelligent feedback.

## 🚀 **Features**

### ⏱️ **Timed Writing Sessions**
- Multiple timer options: 1, 3, 5, 10, 15, 20, 25 minutes
- Real-time progress tracking with visual indicators
- Session extension options (+1min, +3min, +5min)
- Pause, resume, finish, and stop controls

### 🤖 **AI-Powered Writing Assistant**
- **Topic Generation**: AI suggests diverse writing prompts across multiple categories
- **Text Refinement**: AI improves grammar, clarity, and flow while preserving your voice
- **Writing Assessment**: Detailed scoring on grammar, clarity, creativity, and structure
- **Improvement Suggestions**: Personalized advice to enhance your writing skills
- **Topic-Aware AI**: All AI features consider your original writing intent and topic

### 📊 **Performance Tracking**
- Real-time word count and writing speed (WPM)
- Detailed session statistics with actual writing time
- Progress visualization and completion metrics
- Export functionality for session results

### 🛡️ **Secure & Private**
- Backend-integrated AI services for enhanced security
- No API keys exposed in frontend code
- Graceful fallbacks when AI services are unavailable
- Responsive design for desktop and mobile devices

## 🎯 **Writing Modes**

### **Warning Levels**
- **None**: Peaceful writing without interruptions
- **Gentle**: Subtle reminders to keep writing
- **Moderate**: Balanced encouragement and pressure
- **Aggressive**: Intense motivation for serious writers

### **Session Controls**
- **Start**: Begin your writing session
- **Pause/Resume**: Take breaks without losing progress
- **Finish**: Complete early and access AI features
- **Stop**: End session while preserving your work

## 🔧 **Technical Details**

### **Built With**
- **Frontend**: React 18, Vite, Tailwind CSS
- **Backend Integration**: Secure API communication
- **AI Services**: Topic generation, text refinement, scoring, suggestions
- **Responsive Design**: Works on all devices

### **File Structure**
```
WriteOrDie_Production_Release/
├── index.html              # Main application entry point
├── assets/
│   ├── index-[hash].js     # Minified JavaScript bundle
│   ├── index-[hash].css    # Minified CSS bundle
│   └── favicon.ico         # Application icon
└── README.md              # This file
```

## 📦 **Deployment**

### **GitHub Pages**
1. Upload all files to your GitHub repository
2. Enable GitHub Pages in repository settings
3. Select source as "Deploy from a branch"
4. Choose main branch and root folder
5. Your app will be available at `https://yourusername.github.io/repository-name`

### **Vercel**
1. Connect your GitHub repository to Vercel
2. Set build command to: `npm run build`
3. Set output directory to: `dist`
4. Deploy automatically on every push

### **Netlify**
1. Drag and drop the entire folder to Netlify
2. Or connect your GitHub repository
3. Set build command to: `npm run build`
4. Set publish directory to: `dist`

## 🎨 **Customization**

The application uses Tailwind CSS for styling. To customize:
1. Modify the source code in the development version
2. Rebuild using `npm run build`
3. Deploy the updated `dist` folder

## 🔒 **Security**

- All AI features use secure backend integration
- No sensitive API keys are exposed in the frontend
- CORS-enabled for cross-origin requests
- Graceful error handling and fallbacks

## 📝 **License**

This project is ready for production use. Please ensure you have proper licensing for any AI services used in the backend.

## 🚀 **Getting Started**

Simply upload these files to any static hosting service and start writing! The application works immediately without any additional setup required.

---

**Built with ❤️ for writers who want to improve their craft through focused practice and AI-powered feedback.**

