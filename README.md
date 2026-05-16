# SpeakMate

<img src="https://github.com/user-attachments/assets/cffed48d-43ce-49ef-9207-1ad5f6d47bec" alt="Logo" width="200" height="200" />

A voice analysis iOS app built for international students to practice spoken English and build confidence with Australian culture and language. SpeakMate records responses to daily prompts, sends them to the Deepgram API for transcription and sentiment analysis, and returns real, scored feedback on fluency, pronunciation, vocabulary, and confidence.

---

## Overview

SpeakMate is a personal iOS project that combines speech recognition, sentiment analysis, and performance scoring into a single mobile experience. Users respond to daily prompts themed around Australian culture, and the app analyses their speech using Deepgram's Nova-2 model to generate meaningful, structured feedback.

The project was built to understand how speech-to-text APIs actually work at the integration level, how sentiment scores translate into actionable performance metrics, and how to architect a clean service layer in Swift that handles async API calls, error states, and local data persistence properly.

---

## SpeakMate Demo

This video demonstrates the core functionality of SpeakMate, an iOS voice analysis app built for spoken English practice.

### What’s shown in this demo:
- Recording a response to a daily prompt  
- Uploading audio to the Deepgram API  
- Real-time transcription and sentiment analysis  
- Performance scoring across fluency, pronunciation, vocabulary, and confidence  
- Dynamic feedback and results visualization

Watch SpeakMate in action:  
👉 https://drive.google.com/file/d/1X4OoeI-oBUjp_PESaHSXKN-6_DpDQhjJ/view?usp=sharing

---

## Motivation

I wanted to build something that solved a real problem. International students often struggle not just with English grammar, but with sounding natural and confident in everyday Australian conversation. That gap felt like an interesting design challenge.

From a technical perspective, I was curious about how services like Deepgram process audio, return structured transcripts with word-level confidence scores, and attach sentiment analysis at the segment level. Reading the documentation only goes so far. I wanted to build the integration myself and understand what the response structure actually looks like in practice.

---

## Screenshots

_Left:_ Daily prompt screen with voice recording  
_Right:_ Score and feedback screen with personalised insights

<img src="https://github.com/user-attachments/assets/ad1ffc77-e085-47b7-908f-2ef19a58e618" alt="Recording Screen" width="250" height="500" />
<img src="https://github.com/user-attachments/assets/8203a0bc-0c14-439a-9113-215e470f04e1" alt="Results Screen" width="250" height="500" />

---

## Features

- Daily voice prompts themed around Australian culture and slang
- High-quality audio recording at 44.1kHz, 128kbps AAC
- Real-time transcription via Deepgram's Nova-2 model with smart formatting
- Sentiment analysis at the segment level, mapped to a 0 to 100 performance score
- Scoring across four dimensions: fluency, pronunciation, vocabulary range, and confidence
- Aussie slang suggestions generated from transcript content
- Dynamic feedback cards with confidence indicators from Deepgram word-level data
- Local storage of performance history with live chart updates
- Full error handling including retry logic for network failures and invalid audio

---

## How It Works

1. **Recording:** The user responds to a daily prompt. Audio is captured at high quality and stored temporarily in the app's documents directory.

2. **API call:** The audio file is uploaded to Deepgram's `/v1/listen` endpoint with `sentiment=true` and `smart_format=true` enabled. The request is handled asynchronously using Swift's `async/await` pattern.

3. **Response parsing:** Deepgram returns a structured JSON response containing the full transcript, word-level confidence scores, timing data, and sentiment segments scored from -1 to 1.

4. **Scoring:** A `PerformanceAnalyser` maps sentiment scores and confidence values to performance metrics on a 0 to 100 scale. Fluency is derived from speech timing and confidence, vocabulary range from word complexity, and pronunciation from word-level confidence levels.

5. **Feedback:** Results are displayed with dynamic feedback cards, slang suggestions, and a confidence indicator. All scores are stored locally and reflected in the progress charts.

The service layer is structured across three core files: `DeepgramService.swift` handles the pure API integration, `DeepgramModels.swift` holds all Codable response structures and error types, and `PerformanceAnalyser.swift` contains the scoring logic separately from the API layer.

---

## Architecture

```
Audio Recording (AudioRecorder.swift)
        |
        v
Deepgram API (DeepgramService.swift)
        |
        v
Response Parsing (DeepgramModels.swift)
        |
        v
Performance Scoring (PerformanceAnalyser.swift)
        |
        v
Local Storage (ResultsStorageManager.swift)
        |
        v
UI Display (PerformanceView / DailyScoreView / HomeView)
```

---

## Tech Stack

- Swift and SwiftUI
- Xcode (iOS 18 compatible)
- Deepgram API (Nova-2 model, sentiment analysis, smart formatting)
- URLSession with `async/await` for API calls
- UserDefaults with Codable models for local persistence
- Apple SF Pro font, blue and white colour theme

---

## How to Run

```bash
# Step 1: Clone the repository
git clone https://github.com/md2801/SpeakMateSwift.git
cd SpeakMateSwift

# Step 2: Open in Xcode
open SpeakMateSwift.xcodeproj
```

```
# Step 3: Add your Deepgram API key
# In DeepgramService.swift, replace the placeholder:
private let apiKey = "YOUR_DEEPGRAM_API_KEY"

# Get a free key at console.deepgram.com
# Free tier includes $200 credit (approximately 750 hours of audio)
```

```
# Step 4: Build and run on simulator or physical device
# Select your target device in Xcode and press Cmd+R
```

Note: Microphone access is required. When running on a physical device, accept the microphone permission prompt on first launch.

---

## Learning Outcomes

- Understood how speech-to-text APIs structure their responses at the word, segment, and channel level, and how to parse nested Codable models in Swift cleanly
- Learned how sentiment scores work in practice and the decisions involved in mapping a continuous score range to a discrete user-facing metric
- Gained experience designing a layered Swift architecture that separates API concerns, analysis logic, and UI clearly
- Understood the trade-offs in error handling for async network calls, particularly around timeout scenarios, invalid audio formats, and authentication failures
- Learned why word-level confidence scores are more useful than transcript-level confidence for tasks like pronunciation assessment
- Practised using `@MainActor` and `ObservableObject` correctly in an async Swift context

---

## Future Improvements

- Add waveform visualisation during recording for better user feedback
- Implement streak tracking and weekly progress summaries
- Explore local on-device transcription using Apple's Speech framework as a fallback
- Add support for additional Australian accents in the scoring calibration
- Build a prompt library with difficulty levels for more structured practice
