# 💰 YouTube Finance Bot

An autonomous AI pipeline that generates, produces, and uploads Finance/Money content to YouTube — fully hands-free.

```
AI Script → Voice Narration → Video Assembly → YouTube Upload → Repeat Daily
```

---

## What It Does

| Step | What happens |
|---|---|
| 1 | Gemini AI picks a finance topic and writes a full script + title + description + tags |
| 2 | Google TTS converts the script to natural-sounding audio |
| 3 | MoviePy + Pillow assembles a professional 1080p video (dark navy, gold title, rolling subtitles) |
| 4 | YouTube Data API uploads the video on schedule |
| 5 | SQLite logs everything — never repeats a topic |

---

## Prerequisites

- Python 3.10+
- ffmpeg installed and on PATH → [ffmpeg.org/download.html](https://ffmpeg.org/download.html)
- A Gemini API key (free) → [aistudio.google.com](https://aistudio.google.com)
- A YouTube channel with API access → see setup below

---

## Setup

### 1. Clone / open the project folder

```
cd youtube-finance-bot
```

### 2. Install dependencies

```powershell
pip install -r requirements.txt
```

### 3. Configure credentials

```powershell
copy .env.example .env
```

Open `.env` and fill in:

```env
GEMINI_API_KEY=your_key_here
UPLOAD_PRIVACY=private    # start with private while testing!
```

### 4. Set up YouTube API

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (or use an existing one)
3. Enable the **YouTube Data API v3**
4. Go to **Credentials → Create Credentials → OAuth 2.0 Client ID**
5. Application type: **Desktop app**
6. Download the JSON → rename it `client_secrets.json` → place it in the project root
7. First time you run the bot, a browser window will open asking you to sign in — do that once and the token is saved automatically

---

## Usage

```powershell
# Test run — generates script + audio + video locally, no upload
python main.py --test

# Run once right now and upload
python main.py --now

# Run daily at 09:00 (default) as a background daemon
python main.py

# Run daily at a custom time
python main.py --time 14:30

# Check stats (videos made, topics used, successful runs)
python main.py --stats
```

---

## Project Structure

```
youtube-finance-bot/
├── main.py                    ← Entry point / scheduler
├── requirements.txt
├── .env                       ← Your credentials (never commit this)
├── client_secrets.json        ← YouTube OAuth credentials
├── token.json                 ← Auto-generated after first login
├── bot.db                     ← SQLite memory (topics, videos, runs)
├── src/
│   ├── config.py              ← Settings loader
│   ├── database.py            ← SQLite layer
│   ├── content_generator.py   ← Gemini AI content engine
│   ├── tts_engine.py          ← Text-to-speech narration
│   ├── video_assembler.py     ← Video production (MoviePy + Pillow)
│   └── youtube_uploader.py    ← YouTube API uploader
├── assets/
│   └── backgrounds/           ← Optional: drop custom background videos here
├── output/
│   ├── scripts/               ← Generated scripts (.txt)
│   ├── audio/                 ← Generated narrations (.mp3)
│   └── videos/                ← Finished videos (.mp4)
└── logs/
    └── bot.log                ← Full activity log
```

---

## Customising the Bot

**Change the niche** — edit `CHANNEL_NICHE` in `.env`:
```env
CHANNEL_NICHE=cryptocurrency and blockchain
```

**Add your own topic seeds** — edit the `TOPIC_SEEDS` list in `src/content_generator.py`

**Change upload schedule** — edit `DAILY_UPLOAD_TIME` in `.env`:
```env
DAILY_UPLOAD_TIME=18:00
```

**Post as private first** — always recommended until you're happy with quality:
```env
UPLOAD_PRIVACY=private
```

---

## Running 24/7 (Windows)

To keep the bot running even when you close the terminal, use **Task Scheduler**:

1. Open Task Scheduler → Create Basic Task
2. Trigger: Daily at your chosen time
3. Action: Start a program
   - Program: `python`
   - Arguments: `C:\path\to\youtube-finance-bot\main.py --now`
4. Done — Windows will fire it every day automatically

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `ffmpeg not found` | Install ffmpeg and add to PATH |
| `GEMINI_API_KEY not set` | Add key to `.env` file |
| `client_secrets.json not found` | Download from Google Cloud Console |
| `quota exceeded` (YouTube) | YouTube free quota = 6 uploads/day. Space them out. |
| Video renders but no audio | Check ffmpeg is properly installed |

---

## ⚠️ Important Notes

- Start with `UPLOAD_PRIVACY=private` and review videos before going public
- YouTube API free quota allows ~6 uploads per day
- Mark videos as **not made for kids** (already handled in the uploader)
- Do not commit `.env` or `token.json` to git — add them to `.gitignore`
