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

6. What is the purpose of the claude.md file in a project?
6. The claude.md file provides context to the AI model about the project's structure, conventions, technologies used, commands, architecture, and data flow. It helps reduce the number of tool calls by giving the model upfront information about the codebase, preventing it from needing to make additional queries to understand basic project details.

7. How can you generate a claude.md file for an existing project?
7. You can generate a claude.md file by running the /init command. This explores the codebase, understands conventions and structures, and creates a claude.md file based on that analysis.

8. What is the tradeoff to consider when creating a large claude.md file?
8. The entire claude.md file gets added to the assembled prompts and counts toward your context usage. If the file is very large and contains information that isn't used by the model, you'll consume your token budget much faster. It's important to include only relevant information that the model will actually need.

9. How can you check your current context window usage when using Claude Code?
9. You can use the /context command to view your current context window usage. This shows how many tokens you've used out of the total available (for example, 21,000 out of 1 million tokens), broken down by system prompts, messages exchanged with the model, and other components.

10. What is Plan Mode in Claude Code and how is it activated?
10. Plan Mode instructs Claude to create an implementation plan without writing any code yet. It can be activated by using shift+tab in the CLI or selecting it from a dropdown in the desktop app. Alternatively, you can simply prompt Claude directly to plan first before coding, as Plan Mode essentially adds a instruction to the prompt telling the model not to code anything yet.

11. What is the purpose of plan mode in Claude Code?
11. Plan mode allows Claude to review and plan changes without immediately executing code or making modifications. It's a way to see what Claude intends to do before it actually performs any actions.

12. Where are permissions for Claude Code defined?
12. Permissions are defined in the .claude/settings.json file, where you can specify allow lists, deny lists, ask prompts, and default modes for different tool calls.

13. What is the difference between an allow list and a deny list in Claude Code permissions?
13. An allow list contains commands that run with no prompts (executed automatically), while a deny list contains commands that Claude is prevented from executing. There's also an ask list for commands that require explicit user confirmation before execution.

14. What does the "fewer permission prompts" command do?
14. The "fewer permission prompts" command analyzes your previous Claude Code sessions, identifies tool calls that you've frequently accepted, and automatically adds them to your permissions settings to reduce repetitive prompting.

15. What is the hierarchy of Claude Code settings when there are conflicts between global and project-level permissions?
15. The hierarchy from highest to lowest precedence is: managed settings (enterprise/organization level), global settings, project settings, then user settings. Higher-level settings always take precedence over lower-level ones, ensuring that team or company permissions cannot be overridden by individual users.

16. What is the purpose of Advisor mode in Claude?
16. Advisor mode allows you to use a different model (like Opus) specifically for planning tasks while remaining on another model (like Sonnet) for implementation. This lets you leverage the deeper reasoning capabilities of one model for planning without having to manually switch models.

17. What are the four effort level settings available in Claude Opus 4.6, and what do they control?
17. As of Opus 4.6, the four effort levels are low, medium, high, and max. These levels control the amount of thinking and reasoning the model will do for a specific prompt. The effort level gets appended to the prompt so the model understands how deeply it should reason.

18. What is a common reason why Claude might refuse to complete a task or appear lazy in its responses?
18. The effort level may be set too low (medium or low instead of high or max). Before switching to a different model like Opus or Haiku, adjusting the effort level can often resolve the issue.

19. What is the trade-off when setting the effort level to max?
19. Setting the effort level to max provides deeper reasoning but results in higher inference costs and more expensive usage. The model may also overthink simple tasks that don't require extensive reasoning.

20. What is the standard context window size, and what is the extended limit available?
20. The standard context window is 200,000 tokens, but it can be extended up to 1 million tokens.