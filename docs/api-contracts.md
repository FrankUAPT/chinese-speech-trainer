# API Contracts (MVP)

Base path: `/api/*`
All endpoints return JSON unless returning audio bytes.

## POST /api/tts
Generate target audio for a given Chinese text.

### Request (JSON)
{
  "text": "你好",
  "voice": "default",
  "speed": 1.0
}

### Response
- 200 OK
- Content-Type: audio/mpeg (or audio/wav)
- Body: audio bytes

### Errors (JSON)
{
  "error": "string",
  "details": "optional string"
}

---

## POST /api/stt
Transcribe the user’s recorded speech.

### Request
- Content-Type: multipart/form-data
- Fields:
  - `audio` (file): user recording (webm/wav/mp3)
  - `language` (string, optional): "zh" default

### Response (JSON)
{
  "text": "你好",
  "segments": [
    { "start": 0.0, "end": 0.8, "text": "你好" }
  ]
}

### Errors (JSON)
{
  "error": "string",
  "details": "optional string"
}

---

## POST /api/coach
Given:
- target (hanzi + pinyin + english optional)
- user transcript (from /stt)
Return short actionable feedback.

### Request (JSON)
{
  "target": {
    "hanzi": "你好",
    "pinyin": "nǐ hǎo",
    "english": "hello"
  },
  "user": {
    "transcript": "你号"
  }
}

### Response (JSON)
{
  "score": 0.0,
  "feedback": [
    "Say hǎo with a clear dipping 3rd tone. Slow down and exaggerate the dip.",
    "Keep the 'h' in hǎo audible — don’t let it collapse into 'ao'."
  ],
  "debug": {
    "notes": "optional; may be omitted in production"
  }
}

Rules:
- feedback: array length MUST be 0–3
- each entry must be one short actionable bullet

### Errors (JSON)
{
  "error": "string",
  "details": "optional string"
}
