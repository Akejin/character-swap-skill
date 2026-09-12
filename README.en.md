<div align="center">

# Character Swap

### 移形换影

Put a specific person into a specific scene.

**Author · Akejin**

[简体中文](README.md) · [The problem it solves](#the-problem-it-solves) · [Get started](#get-started) · [Scene archive](#scene-archive)

</div>

> IDENTITY FROM THE SAMPLE. PERFORMANCE FROM THE SCENE.

Character Swap is a dual-reference photo-editing skill for Codex. Given a **scene photo** and 1–2 **identity samples**, it replaces the person in the scene with the person from the samples — and it deliberately separates *who the person is* from *how they perform*:

- **Identity** (the entire face, hairstyle, clothing, body shape, apparent age, gender presentation) comes **entirely from the identity samples** — never blended or averaged with the scene's original person;
- **Performance** (expression, gaze direction, head orientation, body pose, gestures) and **facial lighting** (direction, highlights, shadows, color temperature) are **replicated item by item from the scene's original person** — the skill first identifies where the original person was looking, then makes the replaced person look at exactly the same target;
- **Everything else** in the scene — background, lighting, other people, props, existing text — stays pixel-identical.

The result reads as the sample person naturally doing, in the original scene, exactly what the original person was doing.

---

## The problem it solves

```text
scene photo (Image 1) + identity sample(s) (Image 2/3)  →  one image: same scene, new person
```

The most common failure of naive photo swapping is the **blended face** — features averaged between two people, resembling neither. Character Swap constrains the generation model with one fixed three-part directive:

| Source | Provides |
| --- | --- |
| **Identity sample(s)** (Image 2+) | The entire face, hairstyle, clothing, body shape, apparent age, gender — *who this is* |
| **Scene photo** (Image 1) | Expression, gaze target, head angle, body pose, facial lighting — *what they are doing* |

Only the specified person changes: hands still cover the face, cups still hide the mouth, a profile stays a profile.

## Two modes

| | Single identity | Dual identity |
| --- | --- | --- |
| **Input** | Image 1 scene + Image 2 sample | Image 1 scene + Image 2 and Image 3 samples |
| **Replaces** | The most prominent person in the scene | Image 2 → first/most prominent person, Image 3 → second person by default |
| **Multi-person scenes** | Say in text which person to replace | Say in text who replaces whom |

Typical requests:

```text
Use $character-swap-v1-0 to replace the person on the left in the first image
with the person from the second image. Keep everything else unchanged.

Use $character-swap-v1-0 for a dual swap: Image 2 replaces the man in the
white shirt, Image 3 replaces the woman on the right.
```

[Read the full skill](skills/character-swap-v1-0/SKILL.md)

## Generation backend

The skill does not generate images itself. It compiles a fixed directive and calls the MCP image tools available in the environment:

- **Primary:** `seedream-image` (Volcengine Seedream 5.0, multi-reference, best face-identity fidelity);
- Alternatives: `nano-banana` / `gpt-image` (multi-reference, good consistency, slower).

Use identity samples with clear, front-facing faces. The output ratio follows the scene photo by default.

## Scene archive

Representative cases are saved as “scene photo + identity samples → final work” pairs, each noting which image provided identity, which provided performance, and what was preserved in the scene.

The first collection is being prepared. Future cases will live in [`examples/`](examples/).

## Get started

### Install

Clone the repository and copy the skill into your Codex skills directory:

```bash
git clone https://github.com/Akejin/character-swap-skill.git
mkdir -p ~/.codex/skills
cp -R character-swap-skill/skills/character-swap-v1-0 ~/.codex/skills/
```

Restart Codex if the skill does not appear immediately.

### Use

1. Prepare a scene photo (containing the person to replace) and 1–2 identity samples.
2. Upload the images and invoke `$character-swap-v1-0`; optionally add text describing the mapping or clothing details.
3. You get back a single swapped photograph plus a brief creative note.

## Repository structure

```text
character-swap-skill/
├── README.md
├── README.en.md
├── LICENSE
├── examples/
└── skills/
    └── character-swap-v1-0/
        ├── SKILL.md
        └── agents/openai.yaml
```

## About the photos

The skill treats user-supplied photos strictly as references for the current swap task. Unless explicitly requested, photos are never browsed, shared, re-uploaded, or saved, and identity samples are never used for any purpose beyond the requested swap. See the [skill document](skills/character-swap-v1-0/SKILL.md) for the exact rules.

## More AI image tools

If you want to keep going with AI imagery, try **Imagin AI** ([www.imaginai.art](https://www.imaginai.art)) — a browser-based AI image platform, no local setup required.


> Character Swap handles the swap itself — replacing the person in your scene with the one you choose. Imagin AI is a convenient place for what comes next: stylization and further creative variations.

*Imagin AI is a third-party site, independent of this skill. Before uploading any photo to an external platform, it's worth reading their privacy policy first.*


## License

[MIT](LICENSE) © Akejin

<div align="center">

**SAME SCENE. NEW PERSON. NOTHING ELSE CHANGES.**

AI CHARACTER REPLACEMENT · 2026

</div>
