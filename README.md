# CLI-Agent-Templates
guardrail and prompt format docs


The only way to get reliable, production-grade results from LLM coding agents (especially in complex/brownfield repos) is through spec-first development, frequent intentional compaction, and strict control over the context window.

You must stop shouting at the agent and start managing its information diet with surgical precision.

⸻

🔑 High-Value Takeaways (Organized by Concept)

1. Spec-First Development Is Mandatory
	•	Dex’s team at HumanLayer moved from line-by-line code reviews to trusting well-written specs.
	•	Now, they only review:
	•	Research docs (how the system works)
	•	Planning docs (what will change, how it will be tested)
	•	They don’t read the actual code anymore unless necessary.
	•	“A bad line of code is bad. A bad spec leads to 100s of bad lines. A bad line of research leads to 1000s.”

⸻

2. Context Management Is Everything

“Everything that makes coding agents good is context engineering.”

	•	LLMs are pure functions. Their only input is the context window.
	•	Garbage in → garbage out.
	•	Goals:
	•	🟩 Correctness
	•	🟩 Completeness
	•	🟩 Compactness
	•	🟨 Trajectory (vibes-based for now)

⸻

3. Frequent Intentional Compaction
	•	Don’t just “restart” when things go bad. That’s naive.
	•	Instead, do intentional compaction:
	•	Log what’s done.
	•	Clear agent memory.
	•	Start next step with a focused context window.

Tools:
	•	Write structured progress.md files after each stage.
	•	Keep context utilization below 40%.
	•	Use files like:
	•	research.md: what the system is doing (with file names + line numbers)
	•	plan.md: what changes will be made
	•	implement.md: code writing, step by step

⸻

4. Three Phases of Context-Aware AI Dev
	1.	Research – understand system flow, file interdependence, where things live.
	2.	Plan – write detailed high-level plans of what to change, where, and how to test it.
	3.	Implement – short sessions of actual code generation, informed by above.

	•	If implementation fails, the problem was in the plan — not the model.
	•	If the plan was bad, the research was flawed.

⸻

5. Sub-Agents for Inline Compaction
	•	Use sub-agents (e.g., Claude tools or OpenAI function calling) for reading/searching tasks.
	•	Parent agent offloads reading 50+ files to a sub-agent → keeps main context clean.
	•	Main agent receives distilled results, gets back to work.

“Sub-agents aren’t about being a frontend or PM — they’re about memory offloading.”

⸻

6. Code Review is Mental Alignment
	•	Purpose of code review = maintain shared understanding.
	•	Can’t review 2000 lines of Go every day, but can review 200 lines of plan.md.
	•	Teams should prioritize mental alignment over nitpicking code.

⸻

7. Real World Proof
	•	Dex helped:
	•	Ship a PR into a 300K-line Rust codebase without prior context → got merged
	•	Pair-program with a CEO to ship 35K lines of code in one day (adding WASM support to a language)
	•	Intern on Dex’s team shipped 10 PRs in 1 day after onboarding into this system.

⸻

🧪 Sample Workflow (Inferred from Talk)

1. Run research agent
   → Generates research.md (file paths, line numbers, flow)

2. Human reviews research → tweaks if needed

3. Run planning agent
   → Generates plan.md (exact changes, rationale, test plan)

4. Human approves plan

5. Run implementation agent in small steps
   → Constantly compact context

6. Log completed tasks into progress.md
   → Prepare next round of implementation


⸻

⚠️ Warnings and Anti-Patterns
	•	Don’t:
	•	Shout at agents in an endless loop (/compact is trash)
	•	Let them “vibe” in massive repos without constraints
	•	Start implementing before research + planning are solid
	•	Do:
	•	Treat context like sacred real estate
	•	Use markdown files for logging progress between sessions
	•	Build your entire workflow around context preservation

⸻

🧠 Best Quotes

“I haven’t opened a non-markdown file in 2 months.”

“I don’t read code anymore. I read specs.”

“A bad part of a plan can be 100s of bad lines. A bad research line = 1000s.”

“The only thing that matters in code agents is the context window.”

⸻

📦 Resources Mentioned
	•	Dex’s podcast episode on coding agents fixing a large Rust repo (w/ BAML)
	•	SourceAmp + “Ralph Wiggum as a software engineer” (on smart prompting)
	•	All prompts and files are open source (linked via QR code in talk)

⸻

✅ What to Apply Right Now
	•	Switch from code-first to spec-first dev.
	•	Add a progress.md and update it after every PR/step.
	•	Implement a 3-step loop: Research → Plan → Implement.
	•	Set hard rules on max context usage (<40%) per agent loop.
	•	Use intentional compaction instead of /clear or /reset.



AI is not good software but it’s good people—it’s a super eager intern that needs guidance, structure, and coaching to produce great results. Most users fail not because the AI is weak, but because they don’t provide the right context. This talk introduces Context Engineering as the evolved version of prompt engineering.

⸻

🔑 High-Value Tactics and Concepts

1. AI is Like a Tireless Intern, Not a Senior Engineer
	•	It’s always eager to say yes, even when it shouldn’t.
	•	If it says, “Check back in 15 minutes,” it’s dodging because it doesn’t want to say “I don’t know.”
	•	You must coach it like a junior employee: Give clear instructions, allow it to ask questions, and demand honest feedback.

⸻

2. Context Engineering ≠ Prompt Engineering

Context engineering = “Prompt engineering on steroids.”
It’s about giving the AI all the background, expectations, and examples it needs.

Example:
	•	❌ “Write me a sales email.” → generic result.
	•	✅ “Write a sales email using my brand tone, based on this customer transcript, referencing these product specs.”

📌 Rule of thumb: If a human couldn’t complete the task with your prompt alone, the AI probably can’t either.

⸻

3. Chain of Thought Reasoning

Add to your prompt:
👉 “Before you respond to my query, walk me through your thought process step-by-step.”

Why it works:
	•	LLMs don’t generate answers holistically. They generate one word at a time.
	•	Asking for reasoning encourages it to plan before writing.
	•	You get transparency: both output and its assumptions.

⸻

4. Few-Shot Prompting (Examples)

Show AI what you want by including:
	•	✅ Good examples (“Here’s an email I like”)
	•	❌ Bad examples (“Here’s one I don’t want”)

You can even ask the AI:

“Create the opposite of this good example, and explain why it’s bad.”

Examples are far more effective than adjectives like “make it sound professional.”

⸻

5. Reverse Prompting

Ask the model to ask you questions before it starts.

Prompt template:

“Help me write a sales email.
Walk me through your thought process.
Reference this good example.
Before you start, ask me anything you need to do a good job.”

This prevents hallucination by forcing it to gather missing info instead of guessing.

⸻

6. Assigning Roles (Persona Prompting)

Tell the AI who it is. This steers it toward the right knowledge and tone.

Example:
	•	“You’re Dale Carnegie. Give me feedback on this email using your principles.”

Roles create a knowledge domain filter and shape tone, context, and depth.

⸻

7. Constraint Collisions

Push creativity by assigning random constraints or personas:
	•	How would Elon Musk solve this?
	•	How would Jerry Seinfeld?
	•	How would Amazon?

Useful both for human ideation and for breaking LLMs out of ruts.

⸻

8. Tough Conversation Flight Simulators

Use GPT to rehearse difficult real-life conversations by:
	1.	Creating a personality profiler for your conversation partner.
	2.	Roleplaying the conversation.
	3.	Getting objective feedback after.

This gives you a safe space to fail, iterate, and get better before the real conversation.

⸻

9. Feedback Hack: Brutal Russian Judge Mode

Instead of “Great job!”, ask:

“Be a Cold War-era Russian Olympic judge. Be brutal. Deduct points for any flinch.”

AI is otherwise too agreeable. This trick gets you honest, useful critique.

⸻

10. AI Reflects Your Bias

LLMs demonstrate 100% of human cognitive biases. If you use them passively, they’ll reinforce bad thinking.

BUT if you’re intentional, you can use them to become a sharper critical thinker.

Custom instruction tip:

“I want to preserve sharp critical thinking. Please push back when my logic is weak.”

⸻

🧩 Key Philosophical Takeaways
	•	AI ≠ software → AI = collaborator
Treat it like a teammate. It’s not plug-and-play; it’s relationship-based.
	•	Ideas aren’t the problem — execution is.
AI unlocks new adjacent possibilities only if human imagination does too.
	•	AI helps people become more of what they already are
Lazy people become lazier. Critical thinkers become sharper.

⸻

✍️ Actionable Prompts (from talk)
	1.	Chain of Thought Add-On
“Before responding, walk me through your thought process step by step.”
	2.	Reverse Prompting
“Before starting, ask me for any info you need to do a great job.”
	3.	Persona Role Assignment
“You are [insert expert/character]. Evaluate this from their perspective.”
	4.	Few-Shot Prompting
“Here’s a good example. Here’s a bad one. Emulate the good, avoid the bad.”
	5.	Feedback Mode
“Act like a Russian Olympic judge. Brutal, detailed scoring. I can take it.”
	6.	Tough Conversation Simulation
Use multiple windows: (1) profiler, (2) roleplay, (3) feedback engine.

⸻

🧠 Best Quote

“The best AI users are not coders. They’re coaches.”

⸻

✅ What To Do With This
	•	Start embedding step-by-step reasoning in all your important prompts.
	•	Create example banks for anything repetitive you ask AI to do.
	•	Assign roles and ask AI to “push back” when it disagrees or spots bias.
	•	Use it as a simulation environment for hard convos and feedback loops.
	•	Don’t settle for first outputs—iterate like you’re managing an eager intern.

⸻
