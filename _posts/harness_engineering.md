
<details>
  <summary>Reading list - 相关文章链接 </summary>
## Reading list
- [ ] OpenAI: Harness engineering: leveraging Codex in an agent-first world https://openai.com/zh-Hans-CN/index/harness-engineering/

- [ ] Anthropic: Effective harnesses for long-running agents https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents Harness design for long-running application development https://www.anthropic.com/engineering/harness-design-long-running-apps

- [ ] LangChain: The Anatomy of an Agent Harness https://www.langchain.com/blog/the-anatomy-of-an-agent-harness

- [ ] Mitchell Hashimoto: My AI Adoption Journey https://mitchellh.com/writing/my-ai-adoption-journey

- [ ] martinfowler.com: Harness Engineering - first thoughts https://martinfowler.com/articles/exploring-gen-ai/harness-engineering-memo.html Harness engineering for coding agent users https://martinfowler.com/articles/harness-engineering.html
- [ ] [Project glasswing](https://www.anthropic.com/glasswing)

</details>

<details>
<summary>Repository AI-Native - Train the beast </summary>
## Repository AI-Native - Build harness for the beast!
### If use both assistants in IDEs, which is why direction lives in two files:

GitHub Copilot reads .github/copilot-instructions.md automatically. 
Skills are loaded "where supported by the client."

Claude Code reads CLAUDE.md, the .claude/skills/, and the permission/hook config under .claude/.

### Repos level
[CLAUDE.md, .github/copilot-instructions.md](1-step.md)

### File level
[instructions](2-step.md)

### Skills
[skills](3-step.md)

### Custom Agents
[Agents](4-step.md)


</details>

## How we refer to files and code

- In the IDE (VS Code / Visual Studio extension chat), use markdown links, with paths relative to the workspace root:
A file: [copilot-instructions.md](.github/copilot-instructions.md)
A specific line: [SKILL.md:42](.claude/skills/whats-new-newsletter/SKILL.md#L42)
A line range: [Program.cs:10-25](TheWolfDen/Program.cs#L10-L25)
A folder: [skills](.claude/skills/)

- In commit messages, PRs, and code comments, the file_path:line_number form (TheWolfDen/Program.cs:42) is the convention — most tools render it as a jump-to link.
Attach context deliberately. 
- In Copilot use #file/selection references; in Claude Code, highlight the code and it's included as context. Pointing the assistant at the right file beats pasting a wall of code.
- This is also how skills and docs cross-link to each other — see how the standards file links straight to frontend-ui-conventions/SKILL.md for the popover rules rather than restating them.

## Risks to be aware of
AI-native tooling adds new attack surface and new ways to footgun yourself. Treat these as first-class.

### Hooks (Claude Code)
Hooks let Claude Code run arbitrary shell commands automatically on events (a tool call, a prompt submit, session end). This repo uses them benignly — see .claude/settings.json and .claude/hooks/: logging skill usage, logging doc reads, reporting usage, emitting a session summary, all via reviewed PowerShell scripts.

That same power is the risk: a hook runs without prompting you each time. So —

Review every hook before it lands, exactly like any other code, and keep the scripts in the repo under .claude/hooks/ so they're diffable.
Be suspicious of hooks arriving via a settings.local.json or a cloned repo you didn't write — a malicious hook is just code that runs on your machine automatically.
Copilot does not have an equivalent auto-run-shell hook mechanism today; this is a Claude-Code-specific consideration.
Editor extensions
Disable automatic updates for any extension you aren't highly confident in. An auto-updating extension is an unattended code-push onto your machine — a compromised or hijacked extension ships straight to you with no review step. Pin versions and update deliberately.
Prefer first-party / well-established extensions; audit the permissions an extension requests.
Dependencies & the software supply chain
This is the big one, and AI assistants amplify it because they'll happily reach for a package to solve a problem.

Open-source packages (npm, NuGet, PyPI, …) are a prime target for being "popped" — attackers compromise a maintainer account or typo-squat a name and push a malicious version. The more packages you depend on, the larger your exposure.
Mitigate by simplifying dependency scope. Don't pull in a package for something you can write in a few lines. Be especially wary when an assistant suggests "just add library X" — evaluate whether the dependency is worth the supply-chain risk, not just the lines of code it saves.
Pin and review. Lock versions, review additions to project files (.csproj, package.json) in PRs as carefully as code, and prefer well-maintained, widely-used packages over niche ones.
Our data-silo NuGets (ProductDevDb, QualityOpsDb, DwsGlobalEnums, …) are internal packages we control — a different risk profile from public registries, but still version-pinned and reviewed on every bump.
The through-line: anything that runs automatically (hooks, auto-updating extensions, transitively-pulled dependencies) is something a human didn't approve at the moment it ran. Keep that set small, reviewed, and pinned.

## FPGA 
Extensions for VsCode for FPGA Dev: TerosHDL, VHDL LS, Verilog-HDL/SystemVerilog

## Code Review
- [Reference harness](https://github.com/anthropics/defending-code-reference-harness)t
- [Blog post](https://github.com/anthropics/defending-code-reference-harness/blob/main/docs/blog-post.md)
### And for safety 
- limit using npm packages and nuget  packages to the bare minimum you need. Any public package you use adds an attack vector. AI attacks are happening to those repositories and malicious code can then get into your machine (work or personal).
 
- Same with browser extensions, VsCode or other tools extensions. Use very trusted names only and limit your exposure. Make sure VsCode extensions aren't "Auto Update" unless very trusted sources.
 
- It's easy to compromise an extension and then auto push subtle malicious update

- The trend is that open source is going to become very dangerous to use - and so our world is going to be rocked a little bit. The good news is that if you've been building your AI building skills, then this matters less and less as it becomes smarter/better to write most of our own things anyway for the kinds of tasks we are doing and this limits the attack surface.