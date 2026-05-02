# 😂 AI-Powered Bangla Meme Generator

> **Live App:** [ai-powered-meme-maker.netlify.app](https://ai-powered-meme-maker.netlify.app/)

An AI-powered web app that generates funny Bengali meme captions from any image — no backend server needed by the user, no app to install, just open the link and create memes instantly.

---

## 🌟 What This Project Does

Upload any image → AI analyzes it → generates 3 funny Bengali captions → you pick one, edit it, style it, add stickers → download your meme.

Everything runs in the browser. No registration, no login, no cost for the user.

---

## 🧠 How the AI Works

This project does **not** train any custom AI model. Instead, it uses an existing vision AI model via API:

- **Model used:** `meta-llama/llama-4-scout` — Meta's multimodal vision model
- **What it does:** The model looks at the uploaded image and understands what's in it (people, expressions, context, objects), then generates culturally relevant Bangladeshi humor in Bengali
- **Why this model:** It supports image input, understands Bengali, is free to use via OpenRouter, and produces natural-sounding Bengali captions

The AI was not "trained" on our images — it already has the capability to understand images and generate Bengali text. We simply send it the image with a prompt asking for funny Bengali meme captions.

---

## 🔑 API — Where It Comes From

**API Provider:** [OpenRouter](https://openrouter.ai)

OpenRouter is a platform that gives access to many AI models through a single API. Think of it like a gateway — instead of going directly to Meta, Google, or OpenAI, you go through OpenRouter and choose which model to use.

**How to get your own API key:**
1. Go to [openrouter.ai/workspaces/default/keys](https://openrouter.ai/workspaces/default/keys)
2. Create an account (free)
3. Click **"Create"** to generate a new API key
4. Copy the key — it starts with `sk-or-v1-...`

**Important:** Never paste your API key directly in public code (GitHub will automatically detect and block it). Instead, store it as an environment variable in Netlify (explained below).

---

## 🏗️ How It Was Built — Full Process

### Phase 1: Experimenting in Google Colab

Before building the web app, everything was first tested in Google Colab (a free cloud Python environment):

1. **Collected ~1000 Bangladeshi meme images** stored in Google Drive at `MyDrive/memes/`
2. **Used CLIP** (Contrastive Language-Image Pretraining) to create image embeddings — a way to represent images as numbers so similar images can be compared
3. **Built a FAISS index** (`memes_index.faiss` + `memes_meta.pkl`) — a fast similarity search index to find the most visually similar memes from the collection
4. **Got an OpenRouter API key** and tested it with Llama 4 Scout vision model
5. **First successful test:** uploaded a `.heic` photo, sent it to Llama 4 Scout, got back the caption: *"কাপড়টা ভালো হয়েছে, কিন্তু চেহারাটা বোকার মতো হয়েছে"* ✅

### Phase 2: Simplifying the Approach

After testing, we realized:
- The CLIP + FAISS similarity search was unnecessary for the final web app
- Llama 4 Scout is powerful enough to generate good captions from **any** image directly
- No dataset or training needed — the model handles everything

So the final app skips the FAISS step entirely and goes straight: **image → AI → caption**.

### Phase 3: Building the Web App

Built a single `index.html` file with:
- Image upload with drag & drop
- Direct API call to OpenRouter (Llama 4 Scout)
- Canvas-based meme editor

### Phase 4: Solving the API Key Problem

**Problem:** Putting the API key directly in `index.html` and pushing to GitHub caused GitHub's secret scanning to automatically detect and block the key within seconds.

**Solution:** Netlify Serverless Functions — a small backend function (`netlify/functions/generate.js`) that:
- Sits between the browser and OpenRouter
- Stores the API key securely as a Netlify environment variable
- The browser calls `/.netlify/functions/generate` instead of OpenRouter directly
- The key is never exposed in the code

### Phase 5: Deploying

1. Pushed all code to GitHub (public repository)
2. Connected GitHub repo to Netlify
3. Added `OPENROUTER_API_KEY` as an environment variable in Netlify
4. Netlify automatically deploys on every GitHub push

---

## ✨ Features

| Feature | Description |
|---|---|
| 🖼️ Image Upload | Drag & drop or click — JPG, PNG, HEIC, WEBP supported |
| 🤖 3 AI Captions | Get 3 different Bengali captions: simple, sarcastic, overdramatic |
| ✏️ Text Editing | Edit top and bottom text separately (classic meme format) |
| 🎨 Style Controls | Font, size, color, outline, shadow, text background box |
| 📌 Drag Text | Drag text anywhere on the image |
| 😂 Stickers | 24 emoji stickers — click to add, drag to reposition |
| ⬇️ Download | Download final meme as PNG |
| 🔒 Secure | API key never exposed — stored server-side on Netlify |

---

## 📁 Project Structure

```
meme-maker/
├── index.html                  # Complete frontend app
├── netlify/
│   └── functions/
│       └── generate.js         # Serverless function (API proxy)
├── meme_maker.ipynb            # Original Google Colab experiments
└── README.md                   # This file
```

---

## 🚀 How to Deploy Your Own Copy

### Step 1 — Get an OpenRouter API Key
1. Go to [openrouter.ai/workspaces/default/keys](https://openrouter.ai/workspaces/default/keys)
2. Sign up and create a new key
3. Copy the key

### Step 2 — Fork or Clone This Repository
```
https://github.com/AMANOT-ULLAH/meme-maker
```

### Step 3 — Deploy to Netlify
1. Go to [app.netlify.com](https://app.netlify.com)
2. Click **"Add new site"** → **"Import from Git"**
3. Connect your GitHub and select the repo
4. Click **Deploy**

### Step 4 — Add Your API Key
1. In Netlify → **Site configuration** → **Environment variables**
2. Click **"Add a variable"**
3. Key: `OPENROUTER_API_KEY`
4. Value: your OpenRouter key (`sk-or-v1-...`)
5. Save → **Deploys** → **Trigger deploy** → **Deploy project**

Done! Your own instance is live. ✅

---

## ⚠️ Important Notes

- **Never put the API key in `index.html`** — GitHub will detect and block it automatically
- The API key must always be stored in Netlify Environment Variables
- OpenRouter's free tier has rate limits — for heavy usage, add credits to your OpenRouter account
- Set a spending limit in OpenRouter to prevent unexpected charges if others use your app

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| HTML / CSS / JavaScript | Frontend (single file, no framework) |
| Canvas API | Meme rendering, text drawing, image export |
| Netlify | Hosting + Serverless Functions |
| Netlify Functions (Node.js) | API proxy to hide the OpenRouter key |
| OpenRouter API | Gateway to access AI models |
| Llama 4 Scout (Meta) | Vision AI — reads images, writes Bengali captions |
| Google Colab | Initial experimentation (CLIP, FAISS, API testing) |
| CLIP + FAISS | Image similarity search (used in experiments, not in final app) |

---

## 👤 Author

**AMANOT-ULLAH**
GitHub: [@AMANOT-ULLAH](https://github.com/AMANOT-ULLAH)
Live App: [ai-powered-meme-maker.netlify.app](https://ai-powered-meme-maker.netlify.app/)
