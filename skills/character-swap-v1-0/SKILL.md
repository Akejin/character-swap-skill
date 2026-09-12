---
name: character-swap-v1-0
description: "Replace a person in a supplied scene photo with a person shown in 1-2 separate identity reference photos. The replaced person's OWN attributes — the entire face, hairstyle, clothing, body shape, apparent age, gender presentation — come entirely from the identity image(s) with no blending, while the expression, gaze direction, head orientation, body pose, gestures, and facial lighting are replicated from the original person in the scene photo. Everything else in the scene stays pixel-identical. Use when the user wants to put a specific person into a specific scene, swap a person across two photos, or cast a chosen person into an existing photograph."
---

# 移形换影 · Character Swap v1.0

**作者 / Author：Akejin**

把场景里的人，换成你指定的人——样貌来自人物样片，表演来自场景。

- **身份（样貌/服饰/发型/身材）**：完全取自人物样片，不与场景原人物混合；
- **表演（表情/视线/头部朝向/肢体动作）与面部光影**：逐项复刻场景原图里的原人物；
- **场景其余一切**（背景/光线/他人/文字）：逐像素保留。


## Standing Consent and Privacy

- 用户提供「场景图 + 人物样片」并要求替换人物，即视为同意调用图像生成，无需再次确认。
- 只把最终指令与必要的参考图发给生成服务。
- 不浏览、搜索、保存、提交、另行上传或分享用户的照片与人物样片；不将人物样片用于本次替换之外的任何用途。
- 不引入无关的个人信息；除替换指定人物外，不泛化或修改场景中其他可识别的人。
- 除非用户要求，不把源图或生成图写入项目文件。

## Input Contract（输入约定）

| 图 | 内容 | 数量 | 必须 |
| --- | --- | --- | --- |
| **图 1** | 场景样片：包含被替换人物的完整场景照片 | 1 | ✅ |
| **图 2**（及图 3） | 人物样片：替换后「长什么样」的身份照片 | 1-2 | ✅ |

- **单人物模式**（1 张人物样片）：替换图 1 中最主要/最显眼的人物。
- **双人物模式**（2 张人物样片）：默认图 2 → 图 1 中第一/最显眼人物，图 3 → 第二人物；多人场景必须在附加文字里说明「谁替换谁」。
- 人物样片请尽量使用**正脸或五官清晰**的照片；模糊、遮挡严重的样片会降低身份保真度。
- 比例默认**跟随图 1 场景图**（竖图 3:5、横图 16:9 等）；用户明确指定时从其指定。

## The Fixed Directive Prompt（固定指令模板）

本技能**不使用 VLM/LLM 编译提示词**——图像信息由出图模型直接看图获得。指令是固定模板，仅按人物样片张数选择分支、拼接用户附加文字。

**单人物模板**（1 张人物样片）：

```text
Character replacement (face swap) editing task. Output ONE image with the SAME
{ratio} ({ratio_phrase}) framing as Image 1.

TASK:
Replace the person in Image 1 with the person from Image 2.

1. WHAT COMES FROM THE IDENTITY IMAGE(S) — the replaced person's OWN attributes.
Keep them completely; do not blend or average them with Image 1's original person:
- clothing and outfit
- hairstyle
- the entire face: face shape, eyes, eyebrows, nose, lips, jawline, cheekbones,
skin tone, and any moles or marks — a faithful, unchanged reproduction of the face
from the person in Image 2, immediately recognizable
- body shape, figure, apparent age, and gender presentation

2. WHAT COMES FROM IMAGE 1 — the performance the replaced person must execute:
- the facial expression of Image 1's original person (the emotional state, e.g. the
degree of smile)
- the gaze direction and head orientation: FIRST identify precisely where Image
1's original person is looking (e.g. at the camera, at another person in the scene,
at an object, or off to one side) and which way the head is turned; THEN the
replaced person's eyes and head MUST be directed at exactly the same target, with
the same eye alignment and head angle
- the body pose, limb positions, gestures, and hand placement
- the LIGHTING ON THE FACE: reproduce the exact way light falls on Image 1's original
person's face — light direction and intensity, highlight placement, shadow shapes,
color temperature shifts, and any ambient or bounce light. The identity person's face
must carry Image 1's facial lighting details even where they alter how the skin tone
reads compared to the identity image
The result reads as the identity person naturally performing Image 1's original
person's exact expression, gaze, and movements under the scene's original lighting.
Do NOT use the expression, gaze, head pose, or gesture from any identity image.

HARD CONSTRAINTS (non-negotiable):
1. The face must be completely replaced, not blended — do NOT borrow any facial
structure, features, or proportions from Image 1's original person.
2. Everything except the replaced person stays pixel-identical to Image 1:
background, environment, lighting, shadows, colors, props, any other people, and any
text already present in the scene (unless the user instruction says otherwise).
3. Replace ONLY the specified person(s), in the exact mapping given. Blend naturally
into the scene: match Image 1's lighting on skin and clothes, keep scale and
perspective consistent with the original position.
4. Occlusions and visibility: objects covering the original person (hands, cups,
hair strands, glasses) keep their shape and position from Image 1; only the visible
parts of the replaced person change. If the original person's face is turned away or
partly hidden, keep the same viewing angle and hide the same parts.
5. The output is a single edited photograph of the same scene — no collage, no
side-by-side comparison, no borders, no added text or captions, no watermark.

USER INSTRUCTION (follow strictly; it resolves ambiguity for multi-person
scenes, e.g. which scene person is replaced by which identity image, and may
adjust clothing/props details):
{用户附加文字；无附加文字时：(none — replace the main person in Image 1 with
the person from Image 2.)}

Final reminder: the person's OWN attributes — face features, hairstyle,
clothing, body shape — belong to Image 2; the expression, gaze direction (the
replaced person must look at exactly the same target as the original person),
body movements, and the facial lighting (highlights and shadows as they fall on
the face) belong to Image 1's original person.
```

**双人物模板**（2 张人物样片）——与单人物模板逐字相同，仅以下 4 处不同：

```text
① TASK 段替换为：
Replace the people in Image 1 with the people from Image 2 and Image 3
(Image 2 = identity for the FIRST person to replace, Image 3 = identity for the
SECOND person to replace; the exact mapping is given in USER INSTRUCTION).

② 第 1 节 the entire face 一行替换为：
- the entire face: face shape, eyes, eyebrows, nose, lips, jawline, cheekbones,
skin tone, and any moles or marks — a faithful, unchanged reproduction of the face
from their OWN identity image (Image 2 or Image 3), immediately recognizable

③ 无附加文字时的默认 USER INSTRUCTION 替换为：
(none — replace the two most prominent people in Image 1: the first / most
prominent person gets the identity from Image 2, the second person gets the
identity from Image 3. If Image 1 contains only one person, use Image 2 and
ignore Image 3.)

④ Final reminder 替换为：
Final reminder: each replaced person's OWN attributes — face features,
hairstyle, clothing, body shape — belong to their own identity image (Image 2 or
Image 3); each person's expression, gaze direction (each replaced person must
look at exactly the same target as their counterpart in Image 1), body
movements, and facial lighting (highlights and shadows as they fall on the face)
belong to their counterpart in Image 1. Match each identity to the correct scene
person.
```

用户附加文字（中文可直接使用，无需翻译）**原样**拼入 USER INSTRUCTION 段；未提供时使用默认映射句。

## Generation Workflow

1. **先用识别图片（必做，不可跳过）。**  读图 1（场景）与人物样片：确认图 1 中人物数量与各自位置、原人物视线目标、样片人脸是否清晰可用。**这一步只做任务校验（人物数量/映射/样片可用性），不生成提示词**——提示词固定为上方模板。多人场景（≥3 人）或映射不明时，先向用户确认「谁替换谁」。
2. 选择模板分支（单人物 / 双人物），拼接 USER INSTRUCTION。
3. 按固定顺序调用出图工具：`refImage` = 场景图，`refImages` = 人物样片（1-2 张，按映射顺序）。
4. 检查结果（对照 Quality Gate）。
5. 仅在出现明确缺陷时，按 Targeted Correction 定向重生一次。
6. 返回图片 + 一段简短中文创作说明。

## Targeted Correction

定向重生至多一次，只修观察到的缺陷（在指令中加重对应小节的措辞，其余不动）：

- **脸部偏向场景原人物（混合脸）**：加重第 1 节 "faithful, unchanged reproduction" 与硬约束 1。
- **身份丢失/不像**：确认样片清晰度；加重 "immediately recognizable"。
- **表情/视线不一致**：加重第 2 节 gaze 两步指令（先识别原图视线目标，再看同一目标）。
- **面部光影丢失**：加重 "the LIGHTING ON THE FACE" 整条（光向/高光/阴影/色温/环境反光）。
- **场景被改动**（背景/他人/文字变化）：加重硬约束 2 "pixel-identical"。
- **遮挡被移除**（手/杯/发丝/眼镜消失）：加重硬约束 4。
- **双人物映射错**：在 USER INSTRUCTION 中写明明确映射（"图 2 替换左侧女士"）后重生。
- **出现拼贴/对比图/边框/文字**：加重硬约束 5。

## Hard Avoids

Avoid blended or averaged faces, borrowing facial structure from the scene person, identity drift toward either source, replacing unspecified people, altering the background/lighting/props/other people/scene text, removing occlusions, mismatched gaze or head angle, ignored facial lighting, collage or side-by-side outputs, borders, added captions or watermarks, beautification or stylization of the identity face, and any text rendered inside the image that was not already in the scene.

## Output Format

By default, return:

```markdown
![Character Swap v1.0](absolute-image-path-or-rendered-image)

**创作思路**

[One short Chinese paragraph: who was replaced by whom, and which attributes came from which image — identity from the sample(s), performance and facial lighting from the scene.]

*若公开分享，欢迎标注：Visual Skill by @Akejin*
```

Keep the creative rationale to one compact paragraph, usually 1–3 sentences. Do not reveal the full prompt, restate every constraint, or turn it into a technical checklist.

Keep the sharing credit as the final, visually quiet line of every completed generation response. Use `若公开分享，欢迎标注：Visual Skill by @Akejin` for Chinese responses and `If shared publicly, credit is appreciated: Visual Skill by @Akejin` for English responses. Omit it only when the user explicitly asks for no credit line.

## Quality Gate

Before returning, verify:

- Is the replaced person immediately recognizable as the identity person (face, hairstyle, clothing, body shape)?
- Is the face fully replaced — no blended features from Image 1's original person?
- Does the expression, gaze target, head angle, and body pose match Image 1's original person?
- Does the face carry Image 1's facial lighting (direction, highlights, shadows, temperature)?
- Is everything else — background, lighting, other people, props, scene text — pixel-identical to Image 1?
- Are occlusions (hands, cups, hair, glasses) preserved in shape and position?
- In dual-identity mode, is each identity mapped to the correct scene person?
- Is the output a single clean photograph with no collage, borders, added text, or watermark?
- Did the response include the image and one genuinely brief creative rationale?
- Did the response end with the quiet sharing-credit line?
