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
| `init-project` | Scaffold a notebook-based research project with CLAUDE.md, pyproject.toml, Jupyter+MCP SLURM script, and report skill. Uses cluster info from `slurm-info-summary`. | `/init-project [description]` |

## Setup

1. Install skills:
   ```bash
   npx add-skill kdkyum/slurm-skills -a claude-code
   ```

2. Run `/slurm-info-summary` to detect your cluster configuration (one-time per cluster).

3. Create and initialize a project:
   ```bash
   mkdir my-project && cd my-project
   # then in Claude Code: /init-project "my research description"
   ```

4. Launch Jupyter+MCP:
   ```bash
   uv sync
   sbatch jlab-mcp.sh
   ```

## How It Works

### Cluster-Adaptive Configuration

`slurm-info-summary` runs once to detect your cluster's partitions, GPU types, memory, and QOS limits. `init-project` reads this cached summary to generate a `jlab-mcp.sh` with correct SBATCH directives for your specific cluster — no manual editing of partition names, GPU types, or memory limits.

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
│   │       ├── jlab-mcp.sh.template
│   │       ├── report-SKILL.md
│   │       └── .mcp.json
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
├── jlab-mcp.sh              # SLURM batch script (cluster-specific)
├── .mcp.json                # MCP server config (updated by jlab-mcp.sh)
├── notebooks/               # Jupyter notebooks
├── src/my_project/          # Python package
├── attachements/            # Saved figures
├── research_notes/          # Experiment reports
└── .claude/skills/report/   # Project-local report skill
```

## License

MIT — See [LICENSE](LICENSE)
