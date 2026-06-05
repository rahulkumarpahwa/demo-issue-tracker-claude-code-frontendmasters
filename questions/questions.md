1. What are the two main pieces that make up the Claude Code architecture?
1. The harness (Claude Code itself, which runs on desktop, web, or other platforms) and the model (either Opus, Sonnet, or Haiku). The model cannot directly interact with your machine - it can only reason about prompts. The harness is responsible for exposing tools, shell commands, and your codebase to the model.

2. What are the three Claude models available in Claude Code and what are their primary trade-offs?
2. Opus (most capable, best for deep reasoning and complex problems, but slowest and most expensive), Sonnet (balanced capability, speed, and cost - ideal for general software engineering tasks like building features and fixing bugs), and Haiku (fast and cheap, good for tasks that don't require reasoning like refactoring or renaming functions).

3. Why is the model described as stateless in Claude Code?
3. The model has no in-session memory and no memory between calls. Every time the model is called, it starts from zero. The harness must provide all the state, including files, conversation history, environment setup, and other context through the assembled prompt.

4. What are the four main components of an assembled prompt in Claude Code?
4. Tool schemas (JSON schemas defining actions like bash, edit, read, web fetch), 2) System prompts (hard-coded instructions about the model's identity, tone, coding conventions, and security rules), 3) Environment information (operating system, shell, model type, git branch), and 4) Messages array (conversation history including prompts, claude.md file contents, and skill lists).

5. What is the agentic loop in Claude Code?
5. The agentic loop is the continuous back-and-forth process between the harness (Claude Code) and the API/model. The model sends tool calls to the harness, the harness executes them and returns tool results, and this process repeats. The loop ends when the model responds with just text instead of a tool call, signaling it doesn't need to perform any more actions.