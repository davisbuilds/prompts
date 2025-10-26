# Claude Code Best Practices

<https://www.anthropic.com/engineering/claude-code-best-practices>

<https://docs.anthropic.com/en/docs/claude-code/cli-reference>

## Customize your setup

### CLAUDE.md

- CC auto pulls CLAUDE.md into context when starting a convo -- no required format, keep concise & human readable
- Useful for documenting common bash cmds, core files & utility functions, coding style guidelines, testing instructions, repo etiquette, dev env setup, any unexpected behaviors or warnings particular to the project, etc
- Can be placed in several locations:
  - (1) the root of your repo, or wherever you run Claude from, (most common)
  - (2) any parent of the dir where you run Claude (most useful for monorepos)
  - (3) any child of the dir where you run Claude
  - (4) home folder (~/.claude/CLAUDE.md) -- applies to all claude sessions
- Run /init cmd to have Claude auto generate a CLAUDE.md
- Common mistake is adding extensive content without iterating on its effectiveness -- tune with prompt improver & take the time to experiment/determine what produces best instruction following
  - Press the # key to give Claude an instruction that it will automatically incorporate into the relevant CLAUDE.md
- Curate Claudes tools allowlist --> customize to permit add'l tools you know are safe, or allow potentially unsafe tools that are easy to undo (e.g., file editing, git commit). 4 ways to manage:
  - (1) select "Always allow" when prompted during a session
  - (2) use /permissions cmd to add/remove tools from allow list (e.g., add `Edit` to always allow file edits, Bash(git commit:*) to allow git commits)
  - (3) manually edit .claude/settings.json or ~/.claude.json
  - (4) use the `--allowedTools` CLI flag for session-specific permissions
- If using GitHub, install the `gh` CLI
  - Claude can use for creating issues, opening pull requests, reading comments, etc

## Give Claude more tools

- CC inherits your bash env & knows about common utilities like unix tools and gh, but won't know about custom bash tools without instructions
  - Tell Claude the tool name with usage examples
  - Tell Claude to run `--help` to see tool documentation
  - Document frequently used tools in CLAUDE.md
- CC functions as both an MCP server and client. As a client it can connect to any number of MCP servers to access their tools in 3 ways:
  - In project config (available when running CC in that dir)
  - In global config (available in all projects)
  - In a checked-in .mcp.json file (available to anyone working in your codebase)
- When working with MCP, can be helpful to launch Claude with the `--mcp-debug` flag to help identify config issues
- Use custom slash cmds
  - For repeated workflows--debugging loops, log analysis, etc--store prompt templates in .md files within the .claude/commands folder -- these become available thru slash cmds menu when you type /
  - Custom / cmds can include the special keyword `$ARGUMENTS` to pass params from cmd invocation

### Example: .claude/commands/fix-github-issue.md

Please analyze and fix the GitHub issue: $ARGUMENTS.
Follow these steps:

1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR

Remember to use the GitHub CLI (`gh`) for all GitHub-related tasks.

- Putting the above content into `.claude/commands/fix-github-issue.md` makes it available as the `/project:fix-github-issue` command in CC. You could then for example use `/project:fix-github-issue 1234` to have Claude fix issue #1234.
- Can add personal cmds to the `~/.claude/commands` folder for cmds you want available in all your sessions

## Try common workflows

### Explore, plan, code, commit

1. Ask Claude to read relevant files, images, or URLs
   - Provide general pointers/filenames but explicitly tell it not to write any code yet
   - Consider using subagents for complex problems needing to verify details or investigate particular questions, tends to preserve context availability without much downside in terms of lost efficacy
2. Ask Claude to make a plan for how to approach a specific problem (specify "thinking")
   - If results seem reasonable, have Claude create a doc or GH issue with its plan so you can reset if implementation (step 3) isn't what you want
   - Start a new session in plan mode with `claude --permission-mode plan`
3. Ask Claude to implement its solution in code
   - Ask it to explicitly verify the reasonableness of its solution as it implements pieces
4. Ask Claude to commit the result and create a PR
   - Good time to have it update any READMEs or changelogs with explanations

### Write tests, commit; code, iterate, commit

1. Ask Claude to write tests based on expected input/output pairs
   - Be explicit about the fact you're doing TDD so it avoids creating mock implementations
2. Tell Claude to run tests and confirm they fail
   - Explicitly telling it not to write any implementation code at this stage is often helpful
3. Ask Claude to commit the tests when satisfied
4. Ask Claude to write code that passes the tests
   - Instruct it not to modify the tests and keep going until all tests pass (takes few iterations)
   - Helpful at this stage to ask it to verify with independent subagents that the implementation isn't overfitting to the tests
5. Ask Claude to commit the code once satisfied
   - Claude performs best when it has a clear target to iterate against—a visual mock, a test case, or another kind of output. By providing expected outputs like tests, Claude can make changes, evaluate results, and incrementally improve until it succeeds.

### Write code, screenshot result, iterate

1. Give Claude a way to take browser screenshots (e.g., with the Puppeteer MCP server, an iOS simulator MCP server, or manually c/p screenshots into Claude)
2. Give Claude a visual mock by c/p or drag-drop an img, or giving Claude the img filepath
3. Ask Claude to implement the design in code
   - take screenshots of the result, and iterate until its result matches the mock
4. Ask Claude to commit once satisfied
   - Like humans, Claude's outputs tend to improve significantly with iteration. While the first version might be good, after 2-3 iterations it will typically look much better. Give Claude the tools to see its outputs for best results.

### Safe YOLO mode

Can use claude `--dangerously-skip-permissions` to bypass all permission checks and work interrupted until completion

- Works well for workflows like fixing lint errors or generating boilerplate
- Use in a container without internet access (follow this [reference implementation](https://github.com/anthropics/claude-code/tree/main/.devcontainer))

### Codebase Q&A

Use for learning and exploration when onboarding to a new codebase -- Claude can agentically search the codebase to answer general questions

- Core onboarding workflow at Anthropic, significantly improving ramp-up time and reducing load on other engineers

### Use Claude to interact with git

- Search git history to answer questions like "what changes made it into v1.2.3?", "why was this API designed this way?"
- Writing commit messages
- Handle complex git operations like reverting files, resolving rebase conflicts, comparing and grafting patches

### Use Claude to interact with GitHub

- Creating PRs
- Implementing one-shot resolutions for simple code review comments: tell it to fix comments on your PR and push back to the PR branch when its done
- Fixing failing builds or linter warnings
- Categorizing and triaging open issues by asking Claude to loop over open GH issues

### Use Claude to work with Jupyter notebooks

- Recommend having CC and a `.ipynb` file open side-by-side in VS Code
- Telling it to make the notebook or its data viz "aesthetically pleasing" reminds it that it's optimizing for a human viewing experience

## Optimize your workflow

- Be specific in your instructions -- specificity leads to better alignment with expectations
- Give Claude imgs
  - Paste screenshots (cmd+ctrl+shift+4 in macOS to screenshot to clipboard and ctrl+v to paste)
  - Drag-drop imgs directly into the prompt input
  - Provide file paths for imgs
- If you are not adding visuals to context, it can still be helpful to be clear with Claude about how important it is for the result to be visually appealing.
- Mention files you want Claude to look at or work on by using tab completion to quickly reference files or folders anywhere in your repo
- Give Claude URLs alongside your prompts for Claude to fetch and read
  - To avoid permission prompts for the same domains use `/permissions` to add domains to your allowlist
- Course correct early and often
  - Make plans before coding, explicitly telling it not to code until plan looks good
  - Press Esc to interrupt during any phase, preserving context so you can redirect or expand instructions
  - Double-tap Esc to jump back in history, edit a previous prompt, and explore a diff direction
  - Ask Claude to undo changes, often in conjunction with Esc to take a diff approach
- Use /clear to keep context focused between tasks to reset the context window
- Use checklists and scratchpads for complex workflows
  - Have Claude use a .md file or GH issue as a checklist and working scratchpad
  - e.g. to fix a large number of lint issues, do the following:
    - Tell Claude to run the lint command and write all resulting errors (with filenames and line numbers) to a Markdown checklist
    - Instruct Claude to address each issue one by one, fixing and verifying before checking it off and moving to the next
- Pass data into Claude
  - c/p directly into your prompt
  - Pipe into CC (e.g. `cat foo.txt | claude`)
  - Tell Claude to pull data via bash cmds, MCP tools, or custom slash cmds
  - Most sessions involve a combo of these tools, e.g. piping in a log file, then telling Claude to use a tool to pull in add'l context to debug the logs

Multi turn convos

**Continue the most recent conversation**

`claude --continue "Now refactor this for better performance"`

**Resume a specific conversation by session ID**

`claude --resume 550e8400-e29b-41d4-a716-446655440000 "Update the tests"`

**Resume in non-interactive mode**

`claude --resume 550e8400-e29b-41d4-a716-446655440000 "Fix all linting issues" --no-interactive`

## Use headless mode to automate your infra

- Run CC programmatically without interactive UI using the --print (or -p) flag with a prompt, and --output-format-stream-json for streaming json output
  - Headless mode has to be triggered each session
- Headless mode can power automations triggered by GH events, e.g. when a new issue is created in your repo
  - Public CC repo uses Claude to inspect new issues as they come in and assign appropriate labels
- Claude as a linter -- CC can provide subjective code reviews beyond traditional linting tools -- identifying typos, stale comments, misleading function/var names, etc

```bash
claude -p "Stage my changes and write a set of commits for them" \
  --allowedTools "Bash,Read" \
  --permission-mode acceptEdits \
  --cwd /path/to/project
```

## Uplevel with multi-Claude workflows

- Have one Claude write code, another verify
  - Use Claude to write code
  - Run /clear or start a second Claude in another terminal
  - Have the second review the firsts work
  - Start another Claude (or /clear again) to read both the code and review feedback
  - Have this Claude edit the code based on the feedback
- Can do something similar with tests
  - Can even have Claude instances communicate with each other by giving them separate working scratchpads and telling them which one to write to and which one to read from
  - Separation often yields better results than having a single Claude handle everything
- Have multiple checkouts of your repo
  - Create 3-4 git checkouts in separate folders
  - Open each folder in separate terminal tabs
  - Start Claude in each folder with diff tasks
  - Cycle through to check progress and approve/deny permission requests
- Use git worktrees
  - Allow you to check out multiple branches from the same repo into separate dirs -- each worktree has its own working dir with isolated files but shares the same git history
  - Enables you to run multiple Claude sessions simultaneously on diff parts of project, each focused on its own independent task -- since they don’t overlap, each Claude can work at full speed without waiting for the others changes or dealing with merge conflicts
    - Create worktrees: `git worktree add ../project-feature-a feature-a`
    - Launch Claude in each worktree: `cd ../project-feature-a && claude`
    - Create add'l worktrees as needed (repeat steps 1-2 in new terminal tabs)
  - Some tips
    - Use consistent naming conventions
    - Maintain one terminal tab per worktree
    - If you’re using iTerm2 on Mac, set up notifications for when Claude needs attention
    - Use separate IDE windows for different worktrees
    - Clean up when finished: git worktree remove ../project-feature-a
- Use headless mode (`claude -p`) with a custom harness
  - 2 primary patterns
    - **Fanning Out** handles large migrations or analyses (e.g. analyzing sentiment in hundreds of logs or analyzing thousands of csvs)
      - Have Claude write a script to generate a task list. E.g., generate a list of 2k files that need to be migrated from framework A to framework B.
      - Loop thru tasks calling CC programmatically for each and giving it a task and set of tools to use. E.g.

```
claude -p “migrate foo.py from React to Vue. When you are done, you MUST return the string OK if you succeeded, or FAIL if the task failed.” --allowedTools Edit Bash(git commit:*)
```

- Run the script several times and refine your prompt to get the desired outcome
- **Pipelining** integrated Claude into existing data/processing pipelines
  - Call `claude -p "<your prompt>" --json | your_command
  - JSON output (optional) can help provide structure for easier automated processing
- For both cases it can be helpful to use the --verbose flag for debugging the Claude invocation. Turn off in production for cleaner output
