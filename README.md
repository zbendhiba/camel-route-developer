# Camel Developer Skills

A collection of [Claude Skills](https://docs.anthropic.com/en/docs/claude-code/skills) for Apache Camel developers.

## Available Skills

| Skill | Description |
|-------|-------------|
| [camel-route-developer](./camel-route-developer/) | Develop Camel integration routes in YAML DSL using the Camel MCP server and Camel CLI |

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- [JBang](https://www.jbang.dev/) installed
- Camel CLI installed via JBang: `jbang app install camel@apache/camel`
- [Camel MCP server](https://github.com/apache/camel-mcp-server) configured in your project (`.mcp.json`)
- (Optional) [Kaoto Visual Editor](https://kaoto.io/) for visual route editing

## How to Install

Copy the skill folder to your `.claude/skills/` project directory:

```bash
.claude
└── skills
    └── camel-route-developer
        └── SKILL.md
```

> You can install the skill globally if you copy it to `$HOME/.claude/skills/`

```bash
# Clone the repository
git clone https://github.com/zbendhib/camel-developer-skills.git

# Copy to your global skills directory
cp -r camel-developer-skills/camel-route-developer ~/.claude/skills/
```

## How to Use It?

### Method 1: Direct invocation

```bash
# In Claude Code, simply type:
/camel-route-developer
```

The skill will guide you through:
1. Understanding your integration requirement
2. Looking up components and EIPs from the Camel catalog
3. Writing a validated YAML DSL route
4. Providing run instructions with Camel CLI

### Method 2: With initial context

If you already know what you need, provide it directly:

```
/camel-route-developer

I want to create a route that reads CSV files from a directory,
transforms them to JSON, and sends them to a Kafka topic.

Constraints:
- Use file and kafka components
- Unmarshal CSV, marshal to JSON
- Add error handling with dead letter channel
```

### Skill Workflow

```
1. REQUIREMENT GATHERING
   ↓ (understand source, target, transformations)

2. CATALOG LOOKUP (via Camel MCP server)
   ↓ (component docs, EIP docs, data formats)

3. ROUTE WRITING
   ↓ (YAML DSL, .camel.yaml file)

4. VALIDATION
   ↓ (schema validation, endpoint URI checks)

5. SECURITY REVIEW (when applicable)
   ↓ (hardening recommendations)

6. RUN INSTRUCTIONS
   ↓ (camel run, camel export)
```

## How It Works

These skills leverage the **Camel MCP server** for all Camel catalog lookups, component documentation, and route validation. No web searches are performed — everything comes from the official Camel catalog.

Routes are written in **YAML DSL** with the `.camel.yaml` extension, making them fully compatible with the **Kaoto Visual Editor** for drag-and-drop editing.

## Contributing

To add a new skill:

1. Create a new folder in kebab-case (e.g., `camel-testing-helper`)
2. Add a `SKILL.md` file following the [Claude Skills specification](https://docs.anthropic.com/en/docs/claude-code/skills)
3. Update the table above
4. Submit a pull request

## License

Apache-2.0
