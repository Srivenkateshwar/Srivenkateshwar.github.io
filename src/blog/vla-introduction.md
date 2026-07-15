# From ChatGPT to Robots: A Beginner's Introduction to Vision-Language-Action Models

<img src="./eyantra-logo.png" alt="e-Yantra — Engineering a better tomorrow" style="height:44px;background:#fff;border-radius:6px;padding:5px 10px;" />

*Srivenkateshwar,Sahil ShindeJuly 2026 · 12 min read*

> ChatGPT can tell you how to make tea. A Vision-Language-Action model can actually walk to the kitchen and make it.

That single sentence captures the most exciting shift happening in robotics right now. For decades, teaching a robot to do anything required an engineer to program that exact task, step by step, for that exact environment. Vision-Language-Action (VLA) models throw that paradigm away: one neural network that sees through a camera, understands natural language, and outputs physical actions — no task-specific code at all.

This post builds up the idea from scratch. If you understand how ChatGPT works even loosely, you'll understand how VLAs work by the end. And if you don't, that's fine too — we start there.

## What is a VLA?

A Vision-Language-Action model combines three capabilities in a single neural network:

- **Vision** — the model perceives the world through camera images or video. These are its eyes.
- **Language** — the model understands instructions like "pick up the red cube." This is how humans talk to it.
- **Action** — the model outputs motor commands: gripper positions, joint angles, movement targets. These are its hands.

The crucial point: these are not three separate programs glued together. It is **one model, trained end-to-end**, where seeing, understanding, and acting share the same internal representation. That sharing is what makes generalization possible — and generalization is the whole game.

## Why this matters

Before VLAs, robotics was dominated by task-specific programming. An engineer wrote explicit rules for every task: if the object is at position (x, y), move the arm to (x, y), close the gripper. Change the task, the object, or even the lighting, and the program breaks.

VLAs change the paradigm in four ways:

1. **One model, many tasks.** The same weights can pick, place, fold, and wipe — you just change the instruction.
2. **No programming for the end user.** You talk to the robot the way you'd talk to a person.
3. **Generalization to the unseen.** Modern VLAs handle objects and rooms they were never trained on.
4. **The foundation-model shift, but for robots.** NLP already went from one model per task to one general model for everything. Robotics is now making the same jump.

## Step 1: The LLM foundation

Everything starts with a plain large language model, because it is the backbone everything else attaches to.

<div style="background:#FAFAF7;border-radius:12px;padding:22px 14px 8px;">
<svg viewBox="0 0 680 300" role="img" xmlns="http://www.w3.org/2000/svg" style="width:100%;height:auto;font-family:sans-serif;">
<title>LLM basic flow</title>
<defs><marker id="a1" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="#8A887F" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>
<rect x="40" y="122" width="110" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="95" y="144" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Text input</text>
<text x="95" y="162" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">"The cat sat…"</text>
<line x1="150" y1="150" x2="188" y2="150" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a1)"/>
<rect x="192" y="122" width="100" height="56" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.75"/>
<text x="242" y="144" text-anchor="middle" dominant-baseline="central" fill="#085041" font-size="14" font-weight="600">Tokenizer</text>
<text x="242" y="162" text-anchor="middle" dominant-baseline="central" fill="#0F6E56" font-size="12">Text → tokens</text>
<line x1="292" y1="150" x2="330" y2="150" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a1)"/>
<rect x="334" y="50" width="200" height="200" rx="16" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.75"/>
<text x="354" y="76" dominant-baseline="central" fill="#3C3489" font-size="14" font-weight="600">LLM</text>
<rect x="354" y="98" width="160" height="52" rx="8" fill="#FBEAF0" stroke="#993556" stroke-width="0.75"/>
<text x="434" y="118" text-anchor="middle" dominant-baseline="central" fill="#72243E" font-size="14" font-weight="600">Embeddings</text>
<text x="434" y="135" text-anchor="middle" dominant-baseline="central" fill="#993556" font-size="12">Tokens → vectors</text>
<line x1="434" y1="150" x2="434" y2="166" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a1)"/>
<rect x="354" y="170" width="160" height="52" rx="8" fill="#FBEAF0" stroke="#993556" stroke-width="0.75"/>
<text x="434" y="190" text-anchor="middle" dominant-baseline="central" fill="#72243E" font-size="14" font-weight="600">Transformer</text>
<text x="434" y="207" text-anchor="middle" dominant-baseline="central" fill="#993556" font-size="12">Attention layers</text>
<line x1="534" y1="150" x2="572" y2="150" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a1)"/>
<rect x="576" y="122" width="90" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="621" y="144" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Output</text>
<text x="621" y="162" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">Next token</text>
</svg>
</div>

*Figure 1. The LLM pipeline: text becomes tokens, tokens become vectors, the transformer predicts the next token.*

Take the sentence "The cat sat…". A **tokenizer** splits it into tokens — subword pieces — and maps each to an integer ID. One detail worth knowing: the tokenizer is not part of the neural network. It has no learned weights; it's a deterministic lookup table built with an algorithm like byte-pair encoding. The neural network proper begins at the **embedding layer**, which turns those integer IDs into vectors — the numerical language the transformer speaks.

The **transformer** is a stack of attention layers that let every token look at every other token and build contextual understanding. Its output is a prediction of the next token. Run that prediction in a loop and you get generated text.

> **Takeaway:** an LLM is a machine that takes tokens in and predicts the next token out. Everything that follows builds on this.

## Step 2: From LLM to VLM — teaching the model to see

A Vision-Language Model (VLM) adds a second input path to the same backbone. Images come from outside, get converted to tokens, and land in the same transformer as the text tokens.

<div style="background:#FAFAF7;border-radius:12px;padding:22px 14px 8px;">
<svg viewBox="0 0 680 310" role="img" xmlns="http://www.w3.org/2000/svg" style="width:100%;height:auto;font-family:sans-serif;">
<title>VLM architecture</title>
<defs><marker id="a2" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="#8A887F" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>
<rect x="180" y="40" width="390" height="230" rx="16" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.75"/>
<text x="200" y="64" dominant-baseline="central" fill="#3C3489" font-size="14" font-weight="600">VLM</text>
<rect x="35" y="80" width="115" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="92" y="102" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Image input</text>
<text x="92" y="120" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">Camera frame</text>
<line x1="150" y1="108" x2="204" y2="108" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a2)"/>
<rect x="35" y="180" width="115" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="92" y="202" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Text input</text>
<text x="92" y="220" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">"What is this?"</text>
<line x1="150" y1="208" x2="204" y2="208" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a2)"/>
<rect x="208" y="80" width="142" height="56" rx="8" fill="#FAECE7" stroke="#993C1D" stroke-width="0.75"/>
<text x="279" y="102" text-anchor="middle" dominant-baseline="central" fill="#712B13" font-size="14" font-weight="600">Vision encoder</text>
<text x="279" y="120" text-anchor="middle" dominant-baseline="central" fill="#993C1D" font-size="12">Image → tokens</text>
<rect x="208" y="180" width="142" height="56" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.75"/>
<text x="279" y="202" text-anchor="middle" dominant-baseline="central" fill="#085041" font-size="14" font-weight="600">Tokenizer</text>
<text x="279" y="220" text-anchor="middle" dominant-baseline="central" fill="#0F6E56" font-size="12">Text → tokens</text>
<path d="M350 108 H380 V145 H406" fill="none" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a2)"/>
<path d="M350 208 H380 V175 H406" fill="none" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a2)"/>
<rect x="410" y="130" width="140" height="60" rx="8" fill="#FBEAF0" stroke="#993556" stroke-width="0.75"/>
<text x="480" y="152" text-anchor="middle" dominant-baseline="central" fill="#72243E" font-size="14" font-weight="600">Transformer</text>
<text x="480" y="170" text-anchor="middle" dominant-baseline="central" fill="#993556" font-size="12">Fuses tokens</text>
<line x1="550" y1="160" x2="594" y2="160" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a2)"/>
<rect x="598" y="132" width="72" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="634" y="154" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Output</text>
<text x="634" y="172" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">Text</text>
</svg>
</div>

*Figure 2. A VLM is an LLM with a second input path: the vision encoder turns images into tokens, just as the tokenizer does for text.*

The **vision encoder** is typically a pretrained Vision Transformer (ViT) such as CLIP or SigLIP. It slices the image into patches and converts them into "image tokens."

Notice the symmetry: the vision encoder does for images exactly what the tokenizer does for text — it converts raw input into tokens. And the transformer doesn't fundamentally care whether a token came from pixels or from words. Inside it, attention layers mix image tokens and text tokens freely, which is how the model can look at a photo and answer "What is this?"

One nuance: the symmetry is conceptual, not literal. The tokenizer is a fixed lookup table, but the vision encoder is itself a neural network with learned weights. There's also usually a small "projector" — a lightweight MLP — between the vision encoder and the LLM that reshapes image features to match the LLM's token dimension. Beginners can safely ignore both details on a first pass.

## Step 3: From VLM to VLA — teaching the model to act

Now the payoff, and it's smaller than you might expect. Compare the diagram below with the previous one: **everything upstream is identical.** The only new component is the action head, and the text output has been replaced by robot actions.

<div style="background:#FAFAF7;border-radius:12px;padding:22px 14px 8px;">
<svg viewBox="0 0 680 310" role="img" xmlns="http://www.w3.org/2000/svg" style="width:100%;height:auto;font-family:sans-serif;">
<title>VLA architecture</title>
<defs><marker id="a3" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="#8A887F" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>
<rect x="180" y="40" width="390" height="230" rx="16" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.75"/>
<text x="200" y="64" dominant-baseline="central" fill="#3C3489" font-size="14" font-weight="600">VLA</text>
<rect x="35" y="80" width="115" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="92" y="102" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Image input</text>
<text x="92" y="120" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">Camera frame</text>
<line x1="150" y1="108" x2="204" y2="108" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a3)"/>
<rect x="35" y="180" width="115" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="92" y="202" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Instruction</text>
<text x="92" y="220" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">"Pick the cube"</text>
<line x1="150" y1="208" x2="204" y2="208" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a3)"/>
<rect x="208" y="80" width="142" height="56" rx="8" fill="#FAECE7" stroke="#993C1D" stroke-width="0.75"/>
<text x="279" y="102" text-anchor="middle" dominant-baseline="central" fill="#712B13" font-size="14" font-weight="600">Vision encoder</text>
<text x="279" y="120" text-anchor="middle" dominant-baseline="central" fill="#993C1D" font-size="12">Image → tokens</text>
<rect x="208" y="180" width="142" height="56" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.75"/>
<text x="279" y="202" text-anchor="middle" dominant-baseline="central" fill="#085041" font-size="14" font-weight="600">Tokenizer</text>
<text x="279" y="220" text-anchor="middle" dominant-baseline="central" fill="#0F6E56" font-size="12">Text → tokens</text>
<path d="M350 108 H375 V118 H396" fill="none" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a3)"/>
<path d="M350 208 H375 V132 H396" fill="none" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a3)"/>
<rect x="400" y="96" width="150" height="56" rx="8" fill="#FBEAF0" stroke="#993556" stroke-width="0.75"/>
<text x="475" y="118" text-anchor="middle" dominant-baseline="central" fill="#72243E" font-size="14" font-weight="600">Transformer</text>
<text x="475" y="136" text-anchor="middle" dominant-baseline="central" fill="#993556" font-size="12">Fuses tokens</text>
<line x1="475" y1="152" x2="475" y2="172" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a3)"/>
<rect x="400" y="176" width="150" height="56" rx="8" fill="#FAEEDA" stroke="#854F0B" stroke-width="0.75"/>
<text x="475" y="198" text-anchor="middle" dominant-baseline="central" fill="#633806" font-size="14" font-weight="600">Action head</text>
<text x="475" y="216" text-anchor="middle" dominant-baseline="central" fill="#854F0B" font-size="12">Tokens → actions</text>
<line x1="550" y1="204" x2="584" y2="204" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a3)"/>
<rect x="588" y="176" width="80" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="628" y="198" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Actions</text>
<text x="628" y="216" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">Robot arm</text>
</svg>
</div>

*Figure 3. A VLA is a VLM with the text output swapped for an action head. Everything upstream is unchanged.*

The text input is now a command ("Pick the cube") instead of a question. The **action head** translates the transformer's internal decision into motor commands: gripper position, arm joint angles, movement deltas.

Different models implement the action head differently:

- **Discrete action tokens** (RT-2, OpenVLA): actions are literally output as tokens, using the same next-token mechanism the LLM already has. The robot's vocabulary simply gains "action words."
- **Continuous action heads** (π0, Octo): a separate diffusion or flow-matching head outputs smooth, continuous joint values, which suits fine motor control like folding cloth.

> **Takeaway:** a VLA reuses a VLM's brain and teaches it to speak "robot" instead of English.

## Step 4: The feedback loop — why robotics is harder than chat

A chatbot's job ends when it prints text. A robot's output *changes the world* — and that changed world becomes its next input.

<div style="background:#FAFAF7;border-radius:12px;padding:22px 14px 8px;">
<svg viewBox="0 0 680 300" role="img" xmlns="http://www.w3.org/2000/svg" style="width:100%;height:auto;font-family:sans-serif;">
<title>VLA feedback loop</title>
<defs><marker id="a4" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="#8A887F" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>
<rect x="80" y="60" width="160" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="160" y="82" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Observation</text>
<text x="160" y="100" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">Camera image</text>
<line x1="240" y1="88" x2="436" y2="88" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a4)"/>
<rect x="440" y="60" width="160" height="56" rx="8" fill="#EEEDFE" stroke="#534AB7" stroke-width="0.75"/>
<text x="520" y="82" text-anchor="middle" dominant-baseline="central" fill="#3C3489" font-size="14" font-weight="600">VLA model</text>
<text x="520" y="100" text-anchor="middle" dominant-baseline="central" fill="#534AB7" font-size="12">Decides action</text>
<line x1="520" y1="116" x2="520" y2="196" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a4)"/>
<rect x="440" y="200" width="160" height="56" rx="8" fill="#FAECE7" stroke="#993C1D" stroke-width="0.75"/>
<text x="520" y="222" text-anchor="middle" dominant-baseline="central" fill="#712B13" font-size="14" font-weight="600">Robot</text>
<text x="520" y="240" text-anchor="middle" dominant-baseline="central" fill="#993C1D" font-size="12">Executes action</text>
<line x1="436" y1="228" x2="244" y2="228" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a4)"/>
<rect x="80" y="200" width="160" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.75"/>
<text x="160" y="222" text-anchor="middle" dominant-baseline="central" fill="#444441" font-size="14" font-weight="600">Environment</text>
<text x="160" y="240" text-anchor="middle" dominant-baseline="central" fill="#5F5E5A" font-size="12">Scene changes</text>
<line x1="160" y1="196" x2="160" y2="120" stroke="#8A887F" stroke-width="1.5" marker-end="url(#a4)"/>
<text x="340" y="162" text-anchor="middle" dominant-baseline="central" fill="#8A887F" font-size="12">↻ loop runs many times per second</text>
</svg>
</div>

*Figure 4. The perception-action loop: sense, think, act, and the changed world comes back as the next observation.*

Two consequences follow. First, **mistakes compound**: if the robot grabs badly, the next camera frame shows the cube in the wrong place, and the model must notice and correct. Second, that is exactly why the loop runs continuously — typically several times per second, from a few Hz up to 50+ Hz depending on the model. The robot doesn't plan once and execute blindly; it re-observes and re-decides constantly, correcting as it goes.

This closed loop is the fundamental reason embodied AI is harder than chat — and the reason demos of robots recovering from their own mistakes are so impressive.

## A concrete example: π0

To make this concrete, consider **π0** from Physical Intelligence — one of the most capable open VLAs. It follows the exact recipe above with real components filled in: a SigLIP vision encoder, a PaliGemma VLM backbone (about 3B parameters), and a flow-matching action head that outputs chunks of 50 actions at a time for smooth control. Trained on diverse robot data, it can fold laundry, clear tables, and — in its π0.5 version — clean kitchens and bedrooms it has never seen before. The demo videos on the [Physical Intelligence blog](https://www.pi.website/blog/pi05) are worth a watch; seeing a robot generalize to a brand-new home makes the point better than any diagram.

## VLAs in the real world

**Manipulation and household robotics.** Physical Intelligence's π0/π0.5 handle laundry, table clearing, and cleaning in unseen homes. Google DeepMind's RT-2 pioneered the "actions as tokens" approach, and Gemini Robotics extends Gemini's reasoning into dexterous physical tasks. Figure's Helix drives humanoid robots' full upper bodies, including fingers, and lets two robots collaborate on tasks like putting away groceries.

**Industrial and logistics.** Warehouse pick-and-place with unstructured, never-before-seen objects is where traditional automation fails and VLA-style generalization shines — bin picking and package sorting across endless variation in shape and packaging.

**Beyond robot arms.** The recipe isn't limited to grippers. End-to-end autonomous driving research follows the same pattern (camera in, language-grounded reasoning, control out). And the "action" need not be physical at all: computer-use agents that see a screen and click buttons are VLAs whose body is a browser.

## Where to go next

**Papers, in reading order:**

1. **RT-1** (Google, 2022) — the precursor: a transformer for robot control at scale.
2. **RT-2** (Google DeepMind, 2023) — coined "vision-language-action model." Read the blog post before the paper; the blog has the intuition.
3. **OpenVLA** (2024) — a 7B open-source VLA you can download, run, and fine-tune. The best starting point for hands-on work.
4. **π0** (Physical Intelligence, 2024) — flow-matching action head over a VLM backbone; open-sourced in the openpi repository.
5. **Octo** (UC Berkeley, 2024) — a lightweight open generalist policy, good for understanding transformer-based control without huge compute.

**Datasets and benchmarks:** Open X-Embodiment (1M+ trajectories across 22 robot types — the dataset most modern VLAs train on), and LIBERO / SimplerEnv for evaluating policies in simulation.

**Hands-on:** The easiest practical entry is Hugging Face's **LeRobot** — an open-source library with pretrained policies, datasets, tutorials, and support for low-cost hardware like the SO-100/SO-101 arm, so you can train a real policy on real hardware for a couple hundred dollars. If you have a decent GPU, the OpenVLA and openpi repos let you run full-scale VLAs. No hardware at all? MuJoCo and Isaac Sim have you covered.

**A suggested path:** read the RT-2 blog post → install LeRobot and run a pretrained policy in simulation → fine-tune a small policy on a LIBERO task → and if budget allows, build an SO-101 arm and train on your own real data.

## Summary

The whole story in four sentences. An LLM is a next-token predictor over text. A VLM adds a vision encoder so images become tokens too, letting the same backbone see. A VLA swaps the text output for an action head, so the model's "words" become motor commands. Wrap it in a perception-action loop running many times per second, and you have a robot that sees, understands, and acts — **without a single line of task-specific code.**

---

*Based on a lecture prepared for e-Yantra, IIT Bombay.*
