# SLURM Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-2-green.svg)](#available-skills)

Claude Code agent skills for HPC/SLURM research workflows — project scaffolding, JupyterLab+MCP on GPU nodes, and cluster info detection.

## Quick Install

```bash
npx add-skill kdkyum/slurm-skills -a claude-code
```

## Available Skills

| Skill | Description | Invocation |
|-------|-------------|------------|
| `slurm-info-summary` | Detect and cache SLURM cluster info (partitions, GPUs, memory, QOS). Auto-adapts to any SLURM cluster. | `/slurm-info-summary` |
| `init-project` | Scaffold a notebook-based research project with CLAUDE.md, pyproject.toml, MCP config, and report skill. Uses cluster info from `slurm-info-summary`. | `/init-project [description]` |

## Setup

1. Install skills:
   ```bash
   npx add-skill kdkyum/slurm-skills -a claude-code
   ```

2. Install jlab-mcp (one-time):
   ```bash
   uv tool install git+https://github.com/kdkyum/jlab-mcp.git
   ```

3. Run `/slurm-info-summary` to detect your cluster configuration (one-time per cluster).

4. Create and initialize a project:
   ```bash
   mkdir my-project && cd my-project
   # then in Claude Code: /init-project "my research description"
   ```

5. Launch JupyterLab on a GPU node:
   ```bash
   uv sync
   jlab-mcp start    # in a separate terminal — submits SLURM job and waits
   ```

6. Start Claude Code in the project directory. The `jlab-mcp` MCP server connects automatically.

## How It Works

### Cluster-Adaptive Configuration

`slurm-info-summary` runs once to detect your cluster's partitions, GPU types, memory, and QOS limits. `init-project` reads this cached summary to generate `.mcp.json` with correct SLURM environment variables for your specific cluster — no manual editing of partition names, GPU types, or memory limits.

### Token-Efficient Templates

`init-project` uses file templates in `assets/` that are copied via `cp` + `sed` substitution, avoiding expensive token consumption from inline file generation.

## Architecture

```
slurm-skills/
├── skills/
│   ├── init-project/
│   │   ├── SKILL.md               # Skill definition
│   │   └── assets/                # File templates (cp + sed)
│   │       ├── CLAUDE.md.template
│   │       ├── pyproject.toml.template
│   │       ├── .mcp.json.template
│   │       └── report-SKILL.md
│   └── slurm-info-summary/
│       ├── SKILL.md               # Skill definition
│       └── scripts/
│           └── gather-slurm-info.sh
└── README.md
```

## What `init-project` Creates

```
my-project/
├── CLAUDE.md                # Claude Code guidance
├── pyproject.toml           # UV + hatchling, research deps
├── .mcp.json                # jlab-mcp config (cluster-specific env vars)
├── notebooks/               # Jupyter notebooks
├── src/my_project/          # Python package
├── attachements/            # Saved figures
├── research_notes/          # Experiment reports
└── .claude/skills/report/   # Project-local report skill
```

## License

MIT — See [LICENSE](LICENSE)
