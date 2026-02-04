# OpenClaw Skills

Public repository of skills for [OpenClaw](https://github.com/openclaw) — AI agent skills for blockchain interactions.

## Structure

Each top-level directory is a provider. Each subdirectory within a provider is an installable skill containing a `SKILL.md` and other skill related files.

```
openclaw-skills/
└── nadfun/
    ├── SKILL.md
    └── skills/
        ├── trading.md
        ├── create.md
        ├── quote.md
        ├── token.md
        ├── indexer.md
        ├── agent-api.md
        ├── wallet.md
        ├── ausd.md
        └── abi.md
```

## Install Instructions

Give OpenClaw the URL to this repo and it will let you choose which skill to install.

```
https://github.com/openclaw/openclaw-skills
```

## Available Skills

| Provider                  | Skill             | Description                                                                                                    |
| ------------------------- | ----------------- | -------------------------------------------------------------------------------------------------------------- |
| [nadfun](https://nad.fun) | [nadfun](nadfun/) | Monad blockchain token launchpad with bonding curves. Trade tokens, launch your own, monitor events with viem. |

## Contributing

We welcome community contributions! Here's how to add your own skill:

### Adding a New Skill

1. **Fork this repository** and create a new branch for your skill.

2. **Create a provider directory** (if it doesn't exist):
   ```
   mkdir your-provider-name/
   ```

3. **Add the required files**:
   - `SKILL.md` — The main skill definition file (required)
   - `skills/` — Supporting skill modules (optional)

4. **Follow the structure**:
   ```
   your-provider-name/
   ├── SKILL.md
   └── skills/
       └── your-skill.md
   ```

5. **Submit a Pull Request** with a clear description of your skill.

### Guidelines

- Keep skill definitions clear and well-documented
- Include examples of usage in your `SKILL.md`
- Test your skill before submitting
- Use descriptive commit messages
