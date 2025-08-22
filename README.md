# Write or Die - AI-Powered Writing Productivity Tool

![Write or Die Banner](https://i.imgur.com/your-banner-image.png)  <!-- Replace with a real banner image -->

**Write or Die** is a powerful productivity tool designed to help writers, students, and professionals overcome writer's block and maintain writing momentum. It offers a distraction-free environment with timed writing sessions, configurable warning systems, and optional AI-powered assistance for outline generation and text refinement.

This project provides two versions of the application:
1.  **Web Application**: A modern, responsive web app hosted on GitHub Pages.
2.  **Word Macro**: A fully integrated macro for Microsoft Word on Mac.

---

## Table of Contents

- [Features](#features)
- [Live Demo](#live-demo)
- [Getting Started](#getting-started)
  - [Web Application](#web-application)
  - [Word Macro (for Mac)](#word-macro-for-mac)
- [How It Works](#how-it-works)
- [AI Integration](#ai-integration)
- [Security and Privacy](#security-and-privacy)
- [Screenshots](#screenshots)
- [Technology Stack](#technology-stack)
- [Contributing](#contributing)
- [License](#license)

---

## Features

### Core Productivity Features

-   **Timed Writing Sessions**: Choose from 1, 3, 5, 10, 15, 20, or 25-minute sessions to stay focused.
-   **Configurable Warning System**: Four levels of warnings to keep you writing:
    -   **None (Zen Mode)**: No interruptions, just you and your writing.
    -   **Gentle**: Subtle visual cues and status bar notifications.
    -   **Moderate**: Audio beeps and more prominent visual alerts.
    -   **Aggressive**: Screen flashing, persistent alerts, and other surprises.
-   **Real-Time Statistics**: Track your word count, words per minute (WPM), and session progress.
-   **Session Management**: Start, pause, stop, and export your writing sessions.
-   **Distraction-Free Interface**: A clean, professional UI designed to keep you focused.
-   **Local Auto-Save**: Your writing is automatically saved in your browser's local storage.

### AI-Powered Assistance (Optional)

-   **AI Outline Generation**: Generate structured outlines for your writing topics using OpenAI or Hugging Face.
-   **AI Text Refinement**: Improve the grammar, clarity, and style of your writing with AI-powered suggestions.
-   **Secure API Key Management**: Your API keys are stored locally in your browser and never sent to our servers.
-   **Multiple AI Providers**: Choose between OpenAI (recommended) and Hugging Face, with support for more in the future.

### Platform-Specific Features

-   **Web Application**:
    -   Fully responsive design for desktop and mobile.
    -   Hosted on GitHub Pages for easy access.
    -   No installation required.
-   **Word Macro (for Mac)**:
    -   Fully integrated into the Microsoft Word interface.
    -   Native macOS notifications and sounds.
    -   Customizable menu and keyboard shortcuts.

---

## Live Demo

Experience the web application live on GitHub Pages:

**[▶️ Launch Write or Die Web App](https://your-github-username.github.io/write-or-die/)**  <!-- Replace with your actual GitHub Pages URL -->

---

## Getting Started

### Web Application

No installation is needed for the web application. Simply visit the live demo link above to start using it.

### Word Macro (for Mac)

1.  **Download the Macro**: Download the `WriteOrDie_Word_Macro_v1.0.zip` file from the [releases page](https://github.com/your-github-username/write-or-die/releases). <!-- Replace with your releases page URL -->
2.  **Follow the Installation Guide**: Unzip the file and follow the detailed instructions in `WriteOrDie_Installation_Guide.md`.

---

## How It Works

Write or Die is based on the principle of **continuous writing**. The application monitors your writing speed and provides feedback to keep you engaged. If you stop typing for too long, the warning system will activate, encouraging you to keep going.

This method is highly effective for:
-   Overcoming writer's block
-   Generating first drafts quickly
-   Building a consistent writing habit
-   Improving writing speed and fluency

---

## AI Integration

The AI features are designed to enhance your writing process, not replace it. You can use the AI to:
-   **Brainstorm ideas**: Generate an outline to structure your thoughts.
-   **Refine your work**: Improve the quality of your writing after a session.

To use the AI features, you'll need to provide your own API key from a supported provider (OpenAI or Hugging Face). This ensures that you have full control over your usage and costs.

---

## Security and Privacy

Your privacy is our top priority. Here's how we protect your data:

-   **Local Storage**: All your writing and settings are stored locally in your browser or on your computer. Nothing is sent to our servers.
-   **Secure API Keys**: Your API keys are stored in your browser's local storage and are only used to communicate directly with your chosen AI provider.
-   **No Tracking**: We do not track your usage or collect any personal data.

---

## Screenshots

| Web App - Session Setup | Web App - Writing Interface |
| :----------------------: | :-------------------------: |
| ![Session Setup](https://i.imgur.com/your-setup-screenshot.png) | ![Writing Interface](https://i.imgur.com/your-writing-screenshot.png) |

| Word Macro - Setup Dialog | Word Macro - Timer Window |
| :-----------------------: | :-----------------------: |
| ![Word Setup](https://i.imgur.com/your-word-setup-screenshot.png) | ![Word Timer](https://i.imgur.com/your-word-timer-screenshot.png) |

<!-- Replace with real screenshots -->

---

## Technology Stack

### Web Application

-   **Framework**: React
-   **Styling**: Tailwind CSS
-   **UI Components**: shadcn/ui
-   **Icons**: Lucide React
-   **State Management**: React Hooks

### Word Macro

-   **Core Logic**: VBA (Visual Basic for Applications)
-   **Mac Integration**: AppleScript

---

## Contributing

Contributions are welcome! If you have ideas for new features, improvements, or bug fixes, please open an issue or submit a pull request. See our [contributing guide](CONTRIBUTING.md) for more details.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
