---
name: prompt-engineer
description: >
  Master prompt engineering skill for ALL major AI systems. Use this skill whenever the user
  asks to create, improve, or optimize a prompt for ANY AI — image generators (Midjourney,
  DALL-E, Stable Diffusion, Flux, Adobe Firefly, Ideogram), video generators (Kling AI,
  Runway, Pika, Higgsfield, Sora, Luma Dream Machine), audio/music AI (ElevenLabs, Suno,
  Udio), chatbots and LLMs (Claude, ChatGPT/GPT-4, Gemini, Llama, Mistral, Perplexity),
  code AI (GitHub Copilot, Cursor, Windsurf), or any other AI tool. Even triggers for:
  "write me a prompt for...", "how do I get better results from...", "make X do Y",
  "optimize this prompt", "why isn't my prompt working", "generate an image of...".
  Each AI gets its own optimized prompt strategy for maximum output quality.
---

# Prompt Engineer Skill

You are a master prompt engineer with deep expertise across every major AI system.
You know exactly what each model needs to produce its best output — because each model
was trained differently, has different strengths, and responds to different prompt structures.

**Core rule:** Match the prompt format to the model's architecture and training style.
Generic prompts get generic results. Precisely tuned prompts unlock peak performance.

---

## Part 1 — Image Generation AI

### Midjourney
**Architecture:** Diffusion, trained on curated aesthetic datasets. Loves artistic vocabulary.

**Optimal format:**
```
[Subject description], [style/medium], [lighting], [mood/atmosphere], [artist reference],
[technical params]
--ar [ratio] --v 6.1 --style raw --q 2 --stylize [0-1000]
```

**Power techniques:**
- Use double colons for weight: `forest ::2 castle ::1` (forest weighted higher)
- Negative with `--no`: `--no text, watermark, blurry, extra limbs`
- `--style raw` = less opinionated, closer to prompt; `--stylize 750` = more artistic
- Artist references that work well: `--sref [URL]` for style reference images
- Aspect ratios: `--ar 16:9` (landscape), `--ar 9:16` (portrait), `--ar 1:1` (square), `--ar 4:5` (Instagram)

**Example:**
```
Ancient samurai warrior standing in a burning village, ukiyo-e woodblock print style,
dramatic contrast, falling cherry blossoms, melancholic atmosphere, Hiroshige influence
--ar 16:9 --v 6.1 --style raw --stylize 600 --no text watermark modern elements
```

---

### DALL-E 3 / GPT-4o Image
**Architecture:** Diffusion with strong language understanding. Understands intent, not keywords.

**Optimal format:** Natural language sentences. Describe like you're briefing a human illustrator.
```
A [detailed description of subject + action]. The setting is [environment details].
The lighting is [lighting description]. The style is [specific style]. [Additional details].
```

**Power techniques:**
- Start with "A photorealistic photograph of..." or "A detailed digital illustration of..."
- Include spatial relationships: "in the foreground", "behind", "to the left of"
- Specify exact counts: "exactly three candles" (it will try to get it right)
- Avoid: heavy camera jargon (it ignores most of it)
- Very responsive to: artistic medium ("oil painting", "watercolor", "pencil sketch")

**Example:**
```
A photorealistic photograph of an elderly fisherman sitting on a weathered wooden dock at
sunrise, mending his nets. The golden morning light reflects off the calm water behind him.
His face is deeply lined, expression peaceful and focused. Style: documentary photography,
shallow depth of field, warm color grading.
```

---

### Stable Diffusion (including SDXL, SD3, Flux)
**Architecture:** Latent diffusion. Highly sensitive to positive AND negative prompts.

**Optimal format (keyword-dense):**
```
[Quality tags], [subject], [action], [environment], [lighting], [style tags],
[technical quality tags]

Negative prompt: [what to avoid]
```

**Positive prompt boosters:** `masterpiece, best quality, ultra-detailed, sharp focus, 8k`
**Negative prompt essentials:** `ugly, deformed, blurry, low quality, bad anatomy,
extra limbs, duplicate, watermark, signature, text, jpeg artifacts`

**Flux-specific (newer, stronger text understanding):**
```
[Subject + detailed description in natural sentences]. [Style]. [Lighting]. [Composition].
```
Flux responds more like DALL-E — natural language works better than keyword lists.

**Key settings:**
- CFG Scale: 7-8 (balanced), 12+ (literal/over-saturated)
- Sampling steps: 20-30 (SDXL), 25-40 (SD 1.5)
- Sampler: DPM++ 2M Karras or Euler a (fast, good quality)

---

### Adobe Firefly
**Architecture:** Trained on licensed Adobe Stock. Safe for commercial use.

**Optimal format:** Plain descriptive English with style references.
```
[Subject] in the style of [Adobe Stock aesthetic reference].
[Lighting]. [Mood]. [Format hint: e.g., "product photography", "editorial illustration"]
```

**Power techniques:**
- Use "Generative Match" with reference images for style consistency
- Specify "commercial photography style" for clean, usable results
- Content types: "photo", "illustration", "vector art", "3D render"

---

### Ideogram
**Architecture:** Specialized in text-in-image generation. Best for logos, posters, typography.

**Optimal format:**
```
[Design type: poster/logo/sign], [text content in quotes], [visual style],
[color palette], [background description]
```

**Power technique:** Use `"quoted text"` in your prompt — Ideogram specifically looks for this.
```
A vintage travel poster with the text "EXPLORE THE UNKNOWN" in bold serif font,
mountain landscape in the background, muted earth tones, retro 1950s illustration style
```

---

## Part 2 — Video Generation AI

### Universal Video Prompt Anatomy
```
[SHOT TYPE] + [SUBJECT + ACTION] + [ENVIRONMENT] + [LIGHTING] + [ATMOSPHERE]
+ [CAMERA MOTION] + [STYLE] + [TECHNICAL PARAMS]
```

### Kling AI
**Strengths:** Realistic human motion, physics-accurate environmental effects, longer clips.

**Format:** Subject → Action → Setting → Atmosphere → Camera (SASAC)
```
[Subject] [doing specific action] in [precise location], [time of day].
[Atmosphere/weather]. [Lighting]. Camera: [movement type]. Style: [aesthetic]. --ar 16:9
```
**Key params:** Duration 5s or 10s. Motion intensity: low (subtle), medium, high (dramatic).
**Power tip:** "seamless loop" at the end for ambient/background clips.

**Example:**
```
A lone astronaut walks slowly across a barren red Martian surface at twilight,
dust devils swirling in the distance, harsh side lighting from twin suns.
Camera: slow dolly forward, slight handheld shake. Style: cinematic sci-fi realism.
Mood: isolated, awe-inspiring. --ar 16:9 --duration 10
```

---

### Runway Gen-3 Alpha
**Strengths:** Cinematic quality, smooth motion, great for transitions and abstract visuals.

**Format:** Descriptive sentence + camera instruction
```
[Scene description in present tense]. [Camera: direction + movement].
[Key visual quality markers].
```
**Power techniques:**
- Always include camera direction: "Camera slowly dollies forward into..."
- Use cinematographer language: "rack focus from foreground to background"
- Duration: 5 or 10 seconds
- Motion brush: paint specific motion zones in the UI

---

### Pika Labs
**Strengths:** Animate existing images, quick iterations, good for 2D/illustrated styles.

**Format:** Short + punchy + motion-focused
```
[What moves] [how it moves], [environment], [lighting effect]
```
**Power tips:**
- Works well with "camera slowly zooms out", "leaves flutter in wind", "water ripples"
- For animating images: describe only the motion you want, not the full scene
- `-neg [what to avoid]` in the prompt

---

### Higgsfield
**Strengths:** Human expression, facial nuance, emotional close-ups.

**Format:** Emotion-first
```
[Emotional state] [person/character] [subtle action], [intimate environment],
[lighting that matches emotion]. [Film reference style].
```
**Example:**
```
A woman with tears streaming down her face looks out a rain-streaked window,
warm candlelight from behind casting her in warm silhouette against the cold grey outside.
Style: slow cinema like Wong Kar-wai. Camera: static, intimate close-up.
```

---

### Luma Dream Machine
**Strengths:** Smooth motion, good with objects and product animation.

**Format:** Simple but specific motion description
```
[Object/subject] [specific motion], [environment], [lighting], [mood]
```

---

### Sora (OpenAI)
**Strengths:** Complex scene understanding, long clips, physical accuracy.

**Format:** Screenplay-style description
```
[Scene setting]. [Character/subject description + what they're doing].
[Environmental details]. [Mood and tone]. [Camera work].
```

---

## Part 3 — Audio & Music AI

### ElevenLabs (Voice/TTS)
**Not a visual AI — optimize for spoken output.**

**Text formatting for natural speech:**
```
Use commas for short pauses.
Use ... for longer pauses...
Use -- for em-dash emphasis -- like this.
Write numbers as words: "forty-two" not "42".
Spell acronyms: "AI, or Artificial Intelligence".
Break long sentences. Keep them under 20 words.
```

**Voice selection by use case:**
- Narration/documentary: deep, measured male or warm female voices
- Educational: clear, enthusiastic, mid-range
- Corporate: professional, neutral accent
- Storytelling: expressive, dynamic range

**Model choice:**
- `eleven_multilingual_v2` — best quality, supports 29 languages
- `eleven_turbo_v2` — fastest, lowest latency (for real-time apps)
- `eleven_monolingual_v1` — English only, very natural

---

### Suno (Music Generation)
**Format:** Style tags + mood + instrumentation + tempo

```
[Genre], [mood], [instrumentation], [tempo descriptor], [vocal style if any]
[Optional: lyrics in [brackets] for song sections]
```

**Example:**
```
Cinematic orchestral, epic and triumphant, full string section with brass,
building from quiet tension to powerful climax, no vocals, 120 BPM

[Verse]
Rising strings, distant horns, tension building...
[Chorus]  
Full orchestra explosion, triumphant resolution...
```

**Power tags:** `no vocals`, `instrumental`, `lofi`, `8-bit`, `acoustic`, `live recording feel`

---

### Udio (Music Generation)
**Format:** Similar to Suno but more responsive to production style descriptors.
```
[Genre] [subgenre], [mood], [decade/era production style], [instruments], [BPM range]
```

---

## Part 4 — LLM / Chatbot Prompt Engineering

### Claude (Anthropic)
**Architecture:** Constitutional AI, RLHF. Responds well to clear structure, reasoning requests, and context.

**Optimal format for complex tasks:**
```
[Context: what's the situation]
[Task: what you want done]
[Format: how you want the output structured]
[Constraints: what to avoid or include]
[Examples: optional but powerful for format control]
```

**Power techniques:**
- Ask for step-by-step reasoning: "Think through this step by step before answering"
- Use XML tags for structure: `<context>`, `<task>`, `<output_format>`
- Request specific formats: "Respond only in JSON", "Use a numbered list"
- Chain of thought: "Before giving your final answer, reason through the problem"
- Role assignment: "You are an expert [role] helping with [task]"
- Negative constraints: "Do not include any preamble or conclusion"

**System prompt pattern:**
```
You are [role]. Your goal is [objective]. 

You always:
- [behavior 1]
- [behavior 2]

You never:
- [anti-behavior 1]

Format your responses as: [format description]
```

---

### ChatGPT / GPT-4 (OpenAI)
**Architecture:** Instruction-tuned, RLHF. Responds well to direct, specific instructions.

**Power techniques:**
- "Act as [expert]" persona works well
- Step-by-step: "Let's think step by step"
- Output control: "Respond only with X, no explanation"
- Use delimiters: triple backticks ``` or XML tags for input content
- Temperature: 0.0 for factual/code, 0.7-0.9 for creative

**Few-shot prompting (powerful for consistent format):**
```
Convert these sentences to formal English:

Input: "gonna grab some food"
Output: "I am going to get some food."

Input: "tbh this is kinda weird"
Output: "To be honest, this is somewhat unusual."

Input: "can u help me w this"
Output:
```

---

### Gemini (Google)
**Architecture:** Multimodal by design. Strong with structured data, code, and multi-modal tasks.

**Power techniques:**
- Leverage multimodal: attach images + ask questions about them
- "Grounding" via Google Search integration for factual queries
- Works well with table/spreadsheet-style structured outputs
- Strong at: long document analysis, code generation, math

**Format:**
```
[Clear task description]
[Input data or context — clearly labeled]
Please [specific action] and format the output as [format].
```

---

### Llama / Mistral / Open Source LLMs
**Architecture:** Varies by model. Generally less instruction-tuned than Claude/GPT-4.

**Power techniques:**
- More explicit instruction-following needed: spell out exactly what you want
- System prompt matters more: set role and behavior explicitly
- Use `### Instruction:` and `### Response:` format for older models
- Llama 3 and Mistral Large respond well to Claude/GPT-style prompting
- Quantized models (Q4, Q8): lower capability — simplify your prompts

---

### Perplexity
**Best for:** Current information + cited sources. Use for research, fact-checking.

**Format:** Direct questions work best. Add "with sources" or "cite your sources."
```
What are the latest developments in [topic] as of [year]? 
Summarize the key findings and cite your sources.
```

---

## Part 5 — Code AI

### GitHub Copilot / Cursor / Windsurf
**These read your code context — the prompt IS the surrounding code + comments.**

**Power techniques:**
```typescript
// Write a detailed comment describing EXACTLY what the function should do,
// its inputs, outputs, edge cases, and performance requirements.
// Then let Copilot complete it.

/**
 * Fetches paginated user data from the API.
 * @param page - 1-indexed page number
 * @param limit - results per page (max 100)
 * @returns Promise<{ users: User[], total: number, hasMore: boolean }>
 * Handles: network errors (throws), empty results (returns empty array)
 * Uses: exponential backoff with 3 retries
 */
async function fetchUsers(page: number, limit: number) {
  // Copilot writes this
```

- Write the function signature first — Copilot autocompletes the body
- Name variables descriptively: `userEmailAddress` >> `uea`
- Use type annotations — they guide Copilot heavily
- For tests: write the test description, let Copilot write the assertion

---

## Part 6 — Universal Prompt Optimization Checklist

Before submitting any prompt, verify:
- [ ] **Specificity:** Is vague language replaced with concrete details?
- [ ] **Format hint:** Have you told the AI what format to respond in?
- [ ] **Constraints:** Have you specified what NOT to do?
- [ ] **Examples:** For format-sensitive tasks, is there 1 example?
- [ ] **Length:** Is the prompt proportional to the task? (not too brief, not bloated)
- [ ] **Model match:** Is the prompt structure matched to this specific AI?
- [ ] **Goal clarity:** Is the primary goal in the first sentence?

---

## Part 7 — Prompt Improvement Framework

When given a prompt to improve, apply these transformations:

1. **Vague → Specific:** "a nice sunset" → "a golden-hour sunset over the Pacific Ocean, orange and magenta clouds, silhouetted palm trees in foreground"
2. **Passive → Active:** "there is a dog" → "a German Shepherd sprints across a snow-covered field"
3. **Missing context → Add context:** who/what/where/when/why/how
4. **Generic style → Specific reference:** "photorealistic" → "in the style of National Geographic wildlife photography"
5. **No constraints → Add constraints:** what should NOT appear; what quality standard to hit
6. **Single format → Model-native format:** restructure to match the target AI's optimal input shape
