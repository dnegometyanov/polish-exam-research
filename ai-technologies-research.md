# AI Technologies & APIs for Polish Language Exam Preparation iPad App

**Research Date: March 2026**

---

## Table of Contents

1. [Speech Recognition & Pronunciation Scoring](#1-speech-recognition--pronunciation-scoring)
2. [Handwriting Recognition (OCR)](#2-handwriting-recognition-ocr)
3. [AI Text Verification for Language Learning](#3-ai-text-verification-for-language-learning)
4. [AI-Assisted Learning Features](#4-ai-assisted-learning-features)
5. [Existing Language Exam Apps with AI](#5-existing-language-exam-apps-with-ai)
6. [Text-to-Speech for Polish](#6-text-to-speech-for-polish)
7. [Recommended Architecture](#7-recommended-architecture)

---

## 1. Speech Recognition & Pronunciation Scoring

### 1.1 Azure Speech Services — Pronunciation Assessment (RECOMMENDED)

**Polish Support: YES (pl-PL confirmed)**

Azure's pronunciation assessment is the strongest option for Polish. It is one of 34 supported locales, and the service is specifically designed for language learning scenarios.

**Features:**
- Phoneme-level, word-level, and sentence-level accuracy scoring
- Fluency scoring (speech rhythm, pace, silences)
- Completeness scoring (percentage of expected words spoken)
- Miscue detection (insertions, deletions, substitutions)
- Prosody assessment (currently only en-US)
- Content assessment: grammar, vocabulary, and topic scoring (for interactive learning scenarios)
- Real-time and batch processing modes

**Pricing:**
- Standard Speech-to-Text: **$0.017/minute** (~$1.02/hour)
- Pronunciation assessment is billed at the same standard STT rate
- Free tier: 5 hours/month of standard STT
- Custom models: $0.048/min transcription + $0.068/hour hosting

**Implementation:**
- REST API and SDK (Swift SDK available for iOS)
- Supports streaming audio and file upload
- Returns JSON with detailed per-phoneme scoring
- SSML input supported for reference text

**Limitations:**
- Prosody scoring only available for en-US
- Requires internet connection (cloud-based)
- Content assessment (grammar/vocabulary/topic) only in interactive chat mode

**Links:**
- [Pronunciation Assessment Docs](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/how-to-pronunciation-assessment)
- [Language Learning with Pronunciation Assessment](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/language-learning-with-pronunciation-assessment)
- [Azure Speech Pricing](https://azure.microsoft.com/en-us/pricing/details/speech/)

---

### 1.2 Apple Speech Framework (SFSpeechRecognizer)

**Polish Support: YES (pl-PL)**

**Features:**
- Real-time and pre-recorded audio transcription
- On-device recognition available (`supportsOnDeviceRecognition`)
- Confidence levels per transcription segment
- Alternative interpretations returned
- New in iOS 26 (WWDC 2025): **SpeechAnalyzer** API — simplified speech-to-text with Swift

**Pricing:** Free (built into iOS)

**Limitations:**
- No pronunciation scoring — only transcription
- On-device Polish quality may be lower than cloud-based
- Usage limits: ~1000 requests/hour per device, 1 minute max per recognition request
- Cannot score accent quality or phoneme accuracy
- Privacy: server-based recognition sends audio to Apple

**Best Use Case:** Free fallback for basic transcription; combine with custom scoring logic or LLM-based evaluation

**Links:**
- [SFSpeechRecognizer Documentation](https://developer.apple.com/documentation/speech/sfspeechrecognizer)
- [SpeechAnalyzer WWDC25](https://developer.apple.com/videos/play/wwdc2025/277/)

---

### 1.3 OpenAI Whisper API

**Polish Support: YES (exceeds <50% WER threshold)**

**Features:**
- High-accuracy multilingual transcription
- Supports 97+ languages including Polish
- Timestamp-level word segmentation
- GPT-4o Transcribe model adds speaker identification

**Pricing:**
- Whisper: **$0.006/minute** ($0.36/hour)
- GPT-4o Transcribe: **$0.006/minute**
- GPT-4o Mini Transcribe: **$0.003/minute**
- Billed per second

**Limitations:**
- No pronunciation scoring — transcription only
- 25 MB file size limit (~30 min audio)
- No real-time streaming support
- Requires internet connection
- No phoneme-level analysis

**Best Use Case:** Cheap, accurate transcription; pair with custom pronunciation comparison logic (compare transcription against expected text to detect errors)

**Links:**
- [OpenAI Speech-to-Text Guide](https://platform.openai.com/docs/guides/speech-to-text)
- [Whisper GitHub](https://github.com/openai/whisper)
- [OpenAI API Pricing](https://openai.com/api/pricing/)

---

### 1.4 Google Cloud Speech-to-Text

**Polish Support: YES**

**Features:**
- Standard, Enhanced, and Chirp 3 models
- Chirp 3: next-gen universal speech model, 100+ languages
- Automatic language detection
- Speaker diarization
- Real-time streaming and batch processing

**Pricing:**
- Standard: **$0.016–$0.024/minute**
- Enhanced: **$0.036/minute**
- First 60 minutes/month free
- Data logging opt-out adds 40%

**Limitations:**
- No built-in pronunciation scoring
- More expensive than Whisper for pure transcription

**Links:**
- [Google Cloud Speech-to-Text](https://cloud.google.com/speech-to-text)
- [Pricing](https://cloud.google.com/speech-to-text/pricing)

---

### 1.5 Specialized Pronunciation Scoring APIs

#### SpeechSuper
- **Languages:** English, Chinese, Korean, Japanese, German, French, Spanish, Russian — **NO POLISH**
- **Pricing:** Word: $0.0028/req, Sentence: $0.0042/req, Paragraph: $0.0056/req
- **Features:** Phoneme/syllable/word/sentence scoring, mispronunciation detection, syllable stress
- [Website](https://www.speechsuper.com/)

#### Speechace
- **Languages:** English, French, Spanish — **NO POLISH**
- **Features:** Phoneme-level scoring, fluency assessment, IELTS/TOEFL correlation
- **Pricing:** Subscription-based, contact for details; free trial available
- [Website](https://www.speechace.com/)

#### ELSA Speak API
- **Languages:** English, Hindi, Indonesian, Japanese, Korean, Portuguese, Spanish, Thai, Vietnamese — **NO POLISH**
- **Features:** Word stress, pronunciation, fluency, intonation, grammar, vocabulary, IELTS/TOEFL prediction
- **Pricing:** Consumer app $13.33/month; API pricing per-user basis, contact for details
- [ELSA API](https://elsaspeak.com/en/elsa-api/)

### 1.6 Summary: Pronunciation Scoring for Polish

| Solution | Polish STT | Polish Pronunciation Scoring | Pricing |
|---|---|---|---|
| **Azure Speech** | Yes | **Yes (phoneme/word/sentence)** | $0.017/min |
| Apple Speech | Yes | No (transcription only) | Free |
| OpenAI Whisper | Yes | No (transcription only) | $0.006/min |
| Google Cloud STT | Yes | No | $0.016–0.036/min |
| SpeechSuper | No | No | $0.003–0.006/req |
| Speechace | No | No | Contact |
| ELSA API | No | No | Contact |

**Verdict:** Azure Speech Services is the only production-ready API offering Polish pronunciation scoring with phoneme-level granularity. For budget-conscious implementations, combine Whisper transcription with custom diff-based scoring logic.

---

## 2. Handwriting Recognition (OCR)

### 2.1 Google ML Kit — Digital Ink Recognition (RECOMMENDED for iPad)

**Polish Support: YES (Latin script, including diacritics)**

**Features:**
- Recognizes handwritten strokes drawn on screen (ideal for Apple Pencil on iPad)
- Supports 300+ languages and 25+ writing systems
- Polish Latin script with gesture classifiers
- Works **offline** — on-device processing
- Real-time recognition as user writes
- Available for iOS (Swift/Objective-C)

**Pricing:** Free (part of ML Kit)

**Limitations:**
- Works with digital ink (drawn strokes), not photos of handwriting
- Requires stroke data input (x, y coordinates + timestamps)
- Quality depends on handwriting legibility

**Best Use Case:** Perfect for an iPad app where users write with Apple Pencil. Captures stroke data in real time and recognizes Polish text including diacritics (ą, ć, ę, ł, ń, ó, ś, ź, ż).

**Links:**
- [ML Kit Digital Ink Recognition](https://developers.google.com/ml-kit/vision/digital-ink-recognition)

---

### 2.2 Apple Vision Framework (VNRecognizeTextRequest)

**Polish Support: LIMITED / NOT OFFICIALLY LISTED**

The officially supported languages for VNRecognizeTextRequest are: English, French, Italian, German, Spanish, Portuguese, Chinese (Simplified & Traditional). Polish is **not** in the official list.

**Features:**
- Printed and handwritten text recognition from images
- On-device processing
- Bounding box coordinates for detected text
- Confidence levels

**Limitations:**
- Polish not officially supported for handwriting recognition
- May partially work through automatic detection but unreliable for diacritics
- Better suited for printed text in supported languages

**Links:**
- [VNRecognizeTextRequest](https://developer.apple.com/documentation/vision/vnrecognizetextrequest)

---

### 2.3 Google Cloud Vision API (DOCUMENT_TEXT_DETECTION)

**Polish Support: YES for printed text; handwriting support uncertain**

**Features:**
- Handwriting detection via DOCUMENT_TEXT_DETECTION
- Supports 50+ handwritten languages
- Returns structured hierarchy: pages > blocks > paragraphs > words > characters
- Bounding box coordinates
- Confidence scores

**Pricing:**
- **$1.50 per 1,000 requests**
- First 1,000 units/month free
- Each feature type per image = 1 unit

**Limitations:**
- Requires photo/image of handwriting (not real-time stroke data)
- Polish handwriting accuracy with diacritics not well documented
- Requires internet connection

**Links:**
- [Cloud Vision Handwriting](https://docs.cloud.google.com/vision/docs/handwriting)
- [Cloud Vision Pricing](https://cloud.google.com/vision/pricing)

---

### 2.4 Azure Computer Vision (Read API)

**Polish Support: YES for printed text; NOT for handwriting**

Handwriting recognition supports only: English, Chinese Simplified, French, German, Italian, Japanese, Korean, Portuguese, Spanish.

However, Azure's deep-learning "universal model" may attempt Polish text extraction even when not explicitly supported.

**Links:**
- [Azure Vision Language Support](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/language-support)

---

### 2.5 Research on Polish Handwritten OCR

Academic research has extended the EMNIST dataset with Polish diacritics (Ą, Ć classes). CNNs can distinguish similar characters like "A" vs "Ą", but no production-ready Polish-specific handwriting API exists.

Key challenge: Standard datasets (NIST, EMNIST) lack Polish diacritical marks, making general-purpose models less reliable for Polish handwriting.

### 2.6 Summary: Handwriting Recognition for Polish

| Solution | Stroke-based (Pencil) | Photo-based | Polish Diacritics | Offline | Price |
|---|---|---|---|---|---|
| **Google ML Kit Digital Ink** | **Yes** | No | **Yes** | **Yes** | Free |
| Apple Vision | No | Yes | Limited | Yes | Free |
| Google Cloud Vision | No | Yes | Likely | No | $1.50/1K |
| Azure Computer Vision | No | Yes | Printed only | No | ~$1/1K |

**Verdict:** Google ML Kit Digital Ink Recognition is the clear winner for iPad — it supports Polish with diacritics, works offline, is free, and is designed exactly for stylus/finger input on touch screens.

---

## 3. AI Text Verification for Language Learning

### 3.1 LanguageTool (RECOMMENDED for Grammar Checking)

**Polish Support: YES — one of the primary supported languages**

**Features:**
- Grammar, spelling, punctuation, and style checking
- 20,000+ rules for Polish (among the most supported languages)
- Open-source core (LGPL 2.1)
- REST API with JSON responses
- Self-hostable (Java-based server)
- Detects context-dependent errors beyond simple spell-check

**Pricing:**
- Free tier: 10,000 characters per text, limited requests
- API access: starting at **$29/month** for 250 calls/day
- Self-hosted: free (open-source)
- Premium: from $4.99/month for individual use

**Implementation:**
- HTTP REST API — simple JSON request/response
- Can be self-hosted for unlimited usage at zero marginal cost
- Input: text + language code (pl-PL) → Output: list of errors with position, message, suggested corrections

**Links:**
- [LanguageTool API](https://languagetool.org/proofreading-api)
- [GitHub Repository](https://github.com/languagetool-org/languagetool)

---

### 3.2 LLM-Based Essay Grading (Claude / GPT-4)

**Polish Support: YES (all major LLMs handle Polish)**

**Research Findings:**
- GPT-4 performs best for automated essay scoring, with excellent intrarater reliability and good validity
- LLMs achieve Quadratic Weighted Kappa of 0.68 (substantial agreement with human markers)
- Average score discrepancy: +/- 0.5 bands compared to human graders
- Providing a detailed rubric/mark scheme significantly improves accuracy
- Zero-shot approach works: each essay scored independently in a new conversation

**Recommended Approach:**
1. Define a rubric with CEFR-aligned criteria (B1 level for Polish exam)
2. Include criteria categories: grammar accuracy, vocabulary range, task completion, coherence, spelling
3. Use few-shot examples of scored essays at different levels
4. Request structured JSON output with scores per category + detailed feedback
5. Use LanguageTool for mechanical grammar/spelling errors, LLM for holistic assessment

**Implementation Pattern:**
```
System prompt: "You are a Polish language examiner for CEFR B1 exam.
Score the following essay using these criteria: [rubric].
Return JSON with scores (1-5) for each category and specific feedback in Polish."

User: [student essay text]
```

**Pricing (per essay ~500 words):**
- Claude Sonnet: ~$0.01–0.03 per essay
- GPT-4o: ~$0.01–0.03 per essay
- GPT-4o Mini / Claude Haiku: ~$0.001–0.005 per essay

**Limitations:**
- LLMs can exhibit bias based on writing style
- Anti-bias instructions in prompts don't fully prevent bias
- Should not be sole grading mechanism — use as formative feedback tool

**Links:**
- [LLMs for Automated Essay Scoring (ScienceDirect)](https://www.sciencedirect.com/science/article/pii/S2666920X24000353)
- [Claude API Evaluation Tools](https://platform.claude.com/docs/en/test-and-evaluate/eval-tool)

---

### 3.3 Sapling AI

Offers a free Polish grammar checker online but is less established than LanguageTool for Polish.

- [Sapling Polish Grammar Checker](https://sapling.ai/lang/polish)

### 3.4 Summary: Text Verification

| Approach | Polish Support | Grammar | Style | Holistic Scoring | Cost |
|---|---|---|---|---|---|
| **LanguageTool** | **Excellent (20K+ rules)** | **Yes** | **Yes** | No | Free–$29/mo |
| **Claude/GPT-4** | Good | Yes | Yes | **Yes** | ~$0.01/essay |
| Sapling | Basic | Yes | Limited | No | Free tier |

**Verdict:** Use LanguageTool (self-hosted) for real-time grammar/spelling checks as user types, and Claude/GPT-4 for holistic essay scoring with rubric-based feedback after submission.

---

## 4. AI-Assisted Learning Features

### 4.1 Image Description for Speaking Practice

Use vision-capable LLMs to describe images for speaking exercises:

- **Claude (Vision):** Describe a photo in Polish, generate questions about it, suggest vocabulary
- **GPT-4o (Vision):** Same capabilities, multi-modal input
- **Google Gemini (Vision):** Competitive alternative

**Implementation:** Send image + prompt like "Describe this image in Polish at B1 level. List 10 vocabulary words relevant to the image. Create 5 speaking questions about the image."

**Cost:** ~$0.01–0.05 per image analysis depending on model

### 4.2 Vocabulary Suggestion from Images

- Use vision AI to identify objects in images
- Map to Polish vocabulary with declension forms
- Generate contextual example sentences
- Create flashcards automatically from photos

### 4.3 Contextual Grammar Explanations

Use LLMs to provide explanations of Polish grammar in context:
- Explain why a specific case was used
- Show conjugation/declension patterns
- Provide similar example sentences
- Adapt explanations to user's native language

### 4.4 Adaptive Difficulty

**Approaches used by leading apps:**
- **Duolingo's BirdBrain:** Profiles each user, predicts learning journey, adjusts lessons based on performance
- **Spaced Repetition:** Track per-word/per-grammar-rule accuracy, schedule reviews using SM-2 or similar algorithm
- **Error Pattern Analysis:** Track common mistakes, generate targeted exercises
- **CEFR Level Detection:** Use LLM to assess current level from speech/writing samples, adjust content accordingly

**Implementation:** Store per-skill accuracy metrics → feed into difficulty adjustment algorithm → generate exercises at appropriate level using LLM

---

## 5. Existing Language Exam Apps with AI

### 5.1 Duolingo

**AI Features:**
- **Roleplay:** Practice conversations with AI characters
- **Video Call with Lily:** Voice conversations with AI tutor
- **Explain My Answer:** GPT-4-powered contextual feedback (not just correct/incorrect)
- **BirdBrain:** Adaptive learning system
- Uses speech recognition for pronunciation feedback in real-time
- AI-generated content pipelines

### 5.2 Speak (speak.com)

- Heavy speaking repetition focus
- Real-time pronunciation feedback
- Conversational lessons
- Immediate AI-generated feedback on fluency
- Adaptive difficulty based on performance

### 5.3 IELTS/TOEFL AI Prep Apps

| App | Features | Notes |
|---|---|---|
| **LingoLeap** | Instant pronunciation/fluency feedback based on official TOEFL rubrics | |
| **LexiBot** | AI Writing scorer for IELTS; +/-0.5 band accuracy | 100K+ users in first year |
| **WritingBetter** | IELTS/TOEFL writing + speaking with phoneme-level pronunciation feedback | |
| **UpScore.ai** | IELTS Writing Task 2 AI scoring with instant personalized feedback | |
| **Cathoven** | Full IELTS prep powered by AI | |
| **Speechace** | Speaking test with IELTS/PTE/TOEFL score correlation | |

### 5.4 Polish-Specific AI Apps

| App | Features |
|---|---|
| **Polish Ai** | Real-time chat, grammar/pronunciation correction, CEFR A1-B2, gamified vocab |
| **Talkpal (Polish)** | Conversational AI with pronunciation/intonation/rhythm analysis |
| **Univext** | Targeted exercises for 7 grammatical cases, real-time feedback |
| **SpeakPal** | AI-powered Polish conversation practice |

Research suggests learners using adaptive AI achieve **50% faster progress** in complex languages like Polish compared to traditional methods.

---

## 6. Text-to-Speech for Polish

### 6.1 Azure Neural TTS (RECOMMENDED)

**Polish Voices:**
- **pl-PL-ZofiaNeural** (Female)
- **pl-PL-MarekNeural** (Male)

**Features:**
- Neural voices "nearly indistinguishable from human recordings"
- SSML support (pitch, rate, pauses, emphasis control)
- Multiple speaking styles
- Real-time and batch synthesis

**Pricing:**
- Neural voices: **$16/1M characters**
- Free tier: 0.5M characters/month

**Links:**
- [Azure TTS Pricing](https://azure.microsoft.com/en-us/pricing/details/speech/)

---

### 6.2 ElevenLabs (HIGHEST QUALITY)

**Polish Support: YES**

**Features:**
- Eleven v3 and Flash v2.5 models support Polish
- Extremely natural, emotionally expressive speech
- Voice cloning capability
- 75ms latency for Flash model (real-time suitable)
- Captures accent nuances and cultural context
- 70+ languages

**Pricing:**
- Free tier: limited characters/month
- Starter: $5/month (30K characters)
- Creator: $22/month (100K characters)
- Pro: $99/month (500K characters)

**Best for:** Premium listening comprehension exercises where natural-sounding speech matters most

**Links:**
- [ElevenLabs Polish TTS](https://elevenlabs.io/text-to-speech/polish)

---

### 6.3 Google Cloud TTS

**Polish Support: YES**

**Voice Types:**
- Standard voices (basic quality)
- WaveNet voices (higher quality neural)
- Neural2 voices (best quality)

**Pricing:**
- Standard: **$4/1M characters**
- WaveNet: **$16/1M characters**
- Neural2: **$16/1M characters**
- Free tier: Standard 4M chars/month, WaveNet/Neural2 1M chars/month

**Links:**
- [Google Cloud TTS](https://cloud.google.com/text-to-speech)

---

### 6.4 Apple AVSpeechSynthesizer

**Polish Support: YES (pl-PL)**

**Features:**
- Built into iOS — no API costs
- Multiple Polish voice options on iOS 17+
- On-device, offline capable

**Limitations:**
- Sounds more robotic than neural cloud services
- Limited voice variety and expressiveness
- Cannot process streaming text
- No SSML support

**Best for:** Offline fallback, or non-critical TTS (UI prompts, button labels)

---

### 6.5 Summary: TTS for Polish

| Service | Quality | Latency | Offline | Price |
|---|---|---|---|---|
| **ElevenLabs** | Excellent | 75ms (Flash) | No | $5–99/mo |
| **Azure Neural** | Very Good | Low | No | $16/1M chars |
| **Google Neural2** | Very Good | Low | No | $16/1M chars |
| Apple AVSpeech | Basic | Instant | **Yes** | Free |

**Verdict:** Use ElevenLabs or Azure Neural TTS for listening comprehension exercises. Fall back to Apple AVSpeechSynthesizer for offline mode or non-critical audio.

---

## 7. Recommended Architecture

### Primary Tech Stack for Polish Exam iPad App

| Feature | Primary Solution | Fallback | Notes |
|---|---|---|---|
| **Pronunciation Scoring** | Azure Speech (pl-PL) | Apple Speech + custom diff | Only real Polish pronunciation scoring API |
| **Speech-to-Text** | Azure Speech or Whisper | Apple SFSpeechRecognizer | Whisper is cheapest; Apple is free/offline |
| **Handwriting Recognition** | Google ML Kit Digital Ink | — | Free, offline, Polish diacritics support |
| **Grammar Checking** | LanguageTool (self-hosted) | LanguageTool API | 20K+ Polish rules, free if self-hosted |
| **Essay Scoring** | Claude API (rubric-based) | GPT-4o | ~$0.01/essay, CEFR-aligned scoring |
| **Image Description** | Claude Vision / GPT-4o | — | ~$0.01–0.05/image |
| **Text-to-Speech** | Azure Neural or ElevenLabs | Apple AVSpeechSynthesizer | ElevenLabs = best quality |
| **Adaptive Learning** | Custom algorithm | — | Track per-skill accuracy, spaced repetition |

### Estimated Per-User Costs (Active Daily Usage)

Assuming ~15 min speech practice, ~5 essays/month, ~50 TTS generations/day:

| Service | Monthly Cost/User |
|---|---|
| Azure Speech (pronunciation) | ~$4–8 |
| Whisper (additional transcription) | ~$1–2 |
| LLM (essay grading + image + grammar) | ~$1–3 |
| TTS (Azure Neural) | ~$0.50–2 |
| Google ML Kit | Free |
| LanguageTool (self-hosted) | Free (server cost) |
| **Total estimated** | **~$6–15/user/month** |

### Key Implementation Considerations

1. **Offline Mode:** Use Apple Speech + ML Kit Digital Ink + AVSpeechSynthesizer for offline functionality
2. **Privacy:** On-device processing where possible (Apple frameworks, ML Kit)
3. **Latency:** ElevenLabs Flash (75ms) and Azure Speech streaming for real-time interactions
4. **Polish Diacritics:** ML Kit Digital Ink is the only reliable free option for handwritten diacritics
5. **Cost Optimization:** Cache TTS audio for common phrases; use cheaper models (Haiku/GPT-4o-mini) for simpler tasks
