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

21. What is the purpose of skills in Claude Code?
21. Skills are markdown files with specific procedures for repeated multi-step workflows. They allow you to define processes that would otherwise need to be re-explained to Claude every time, eliminating the need to repeatedly type the same prompts for tasks like deployment, integrations, or Q&A loops.

22. How does Claude Code determine when to invoke a skill automatically?
22. Claude determines skill invocation based on the skill's description field. By default, the name and description of skills are sent with prompts. When a prompt matches the skill's description, the model may call it automatically. Users can also explicitly invoke skills as slash commands.

23. What configuration option prevents a model from automatically invoking a skill, making it only available as a manual slash command?
23. Setting "disable model invocation: true" in the skill's front matter prevents the model from automatically invoking the skill, keeping it only available for manual user invocation via slash commands.

24. How can you pass arguments to a skill in Claude Code?
24. You can pass arguments to skills using standard argument syntax. For example, if you have a deploy skill that accepts an environment argument, you can invoke it with "deploy staging" or "deploy production", and the skill will use the specified argument in its execution.

25. What is the primary purpose of Skill Creator in relation to skills?
25. Skill Creator is used to automatically create skills and can also check if a skill actually improves workflow performance. It runs evaluations (evals) on skills to test how the codebase performs with and without the skill, ensuring that added skills actually improve the code rather than just adding unnecessary tokens.

26. What is the difference between skills and tool calls in Claude Code?
26. Skills are packaged prompts written in markdown that help avoid retyping the same prompts and can be shared with teammates. Tool calls are different - they allow the model to perform specific actions like reading or editing files. Tool calls are necessary for an agent to function; without them, Claude would just be a chatbot that responds with text.

27. Why should you use the @ symbol when referencing specific files in prompts?
27. Using @ in prompts to reference files attaches the file directly to the prompt itself, which reduces or removes one tool call (like read or write operations). This makes the interaction more efficient by including the file context upfront rather than requiring Claude to make an additional tool call to access it.

28. What is the purpose of the when_to_use field in skill front matter?
28. The when_to_use field provides additional context specifically for the model to understand when to invoke a particular skill. While the description field helps both users and the model (and is visible in the CLI), the when_to_use field gives even more precision for the model without cluttering the user-facing description.

29. How does using context_fork in a skill affect where the skill runs?
29. Using context_fork in a skill configuration makes the skill run in a sub-agent rather than the main context. The sub-agent runs in parallel with a brand new context, and only the results are returned to the main conversation. This prevents the main context from being cluttered with intermediate tool calls and can improve the quality of model output.

30. What are hooks in the context of agentic systems?
30. Hooks allow custom logic to run at specific points in the agentic loop or lifecycle. They can be configured to trigger specific events at particular points in the loop, such as executing a prompt, calling an HTTP endpoint, or running a shell command.

31. What is the difference between pre-tool use and post-tool use hooks?
31. Pre-tool use hooks fire after the model emits a tool call but before it executes, allowing you to add validation or block certain operations. Post-tool use hooks fire after a tool has successfully executed, which is useful for tasks like formatting or linting.

32. When does the 'session start' hook fire?
32. The 'session start' hook fires when Claude Code opens or when the CLI starts.

33. How can you add a hook in Claude Code?
33. You can add a hook in your settings.json file. For example, to run a type check after a tool use that edits or writes files, you would add a post-tool use hook with a command like bun run typecheck.

34. What is a Claude Code plugin and what can it contain?
34. A plugin is a package stored in a separate repository or folder that contains a .claude plugin file (similar to package.json with name, version, and author information). In the same directory, you can add skills, hooks, and other configurations that get packaged together within that plugin.

NOTE : The /agents wizard has been removed as of v.2..1.198. Source
@"code-reviewer (agent)" analyze the code

35. What is a sub-agent and how does its context differ from the main agent?
35. A sub-agent is a separate loop that runs in its own forked environment with its own context, tools, and system prompt. It runs in the background and only returns results to the main agent, ensuring that its tool call results and intermediate steps don't pollute the main conversation's context.

36. What is the difference between creating a personal agent versus a project-specific agent?
36. A personal agent can be reused across multiple projects and repositories, while a project-specific agent is limited to a single project or repository.

37. Why do sub-agents tend to consume a large number of tokens?
37. Sub-agents use a lot of tokens because they essentially start from scratch with their own context. They don't have cached prompts at that point, and when multiple sub-agents run in parallel, each one makes its own requests, multiplying token usage significantly.

38. What is the key difference between sub-agents and agent teams in terms of communication?
38. Sub-agents run independently and cannot communicate with each other - the main thread only sees their final results. Agent teams, however, can actively communicate with each other while working, coordinating their tasks together.

39. How do agent teams determine when their work is complete?
39. Agent teams work against a shared task list created by the main agent. They check this task list continuously, and only when all tasks are complete do they end their work.

40. What is a significant drawback of using agent teams compared to sub-agents?
40. Agent teams use a lot more tokens than sub-agents. For most use cases, a sub-agent would be sufficient and more efficient.

41. What types of specialized roles can be assigned to team agents?
41. Team agents can be assigned specialized roles such as code reviewer, implementer, PR agent, UI designer, or QA teammate. The system understands what the appropriate system prompt should be for each role.