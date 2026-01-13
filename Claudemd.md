To get you started, let's focus on setting up a CLAUDE.md file.

This file acts as the "brain" or memory for Claude Code within your project. It’s where you define your project's coding standards, preferred libraries, and architectural rules so that Claude doesn't have to guess how you like things done.

Step 1: Initialize the Project
If you haven't already, run the initialization command in your terminal:

```
claude /init
```

Step 2: Customize your CLAUDE.md

A good CLAUDE.md should be concise but specific. Here is a high-quality template you can adapt. You can ask Claude Code to "Update CLAUDE.md with these rules" and paste this in:

# Project: [Your Project Name]

## Build & Test Commands

- Build: `npm run build`
- Test: `npm test`
- Lint: `npm run lint`

## Architecture & Style
- Use **TypeScript** for all new files.
- Favor **Functional Components** and Hooks over Class Components.
- Use **Tailwind CSS** for styling; avoid inline styles.
- State management: Use **Zustand** for global state.

## Coding Patterns
- Always use `export const` for components.
- Wrap all API calls in a `try/catch` block with standard error logging.
- Unit tests should use **Vitest**.

Step 3: Give Claude a Task
Now that Claude knows your "rules," try giving it a complex, multi-step command to see the agentic behavior in action. For example:
 * Refactoring: "Find all components using CSS modules and convert them to Tailwind CSS."
 * Feature Addition: "Add a new 'Settings' page with a toggle for email notifications. Update the sidebar to link to it."
 * Debugging: "Run the tests. If any fail, fix the code until they all pass."
Pro-Tip: Using "Flags"
When you run a command, you can use flags to control how Claude behaves:
 * claude --readonly: Lets Claude analyze code without the permission to edit files.
 * claude "your prompt": Run a single command and exit immediately.
