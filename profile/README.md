# Rhylthyme

Rhylthyme ("real time") is a declarative language and runtime for schedules that a **person** executes: several lines of work running in parallel, dependencies between them, steps whose length is fixed, bounded or open-ended, and a few shared resources that only so many steps can use at once. A program is JSON; the runtime plays it live with a clock, cues and manual gates; an MCP server lets an AI agent author, validate and publish one as tool calls.

The same schema serves four verticals:

| Site | For |
|---|---|
| [kitchen.rhylthyme.com](https://kitchen.rhylthyme.com) | cooking several dishes so they finish together |
| [lab.rhylthyme.com](https://lab.rhylthyme.com) | bench protocols with overlapping incubations and shared instruments |
| [events.rhylthyme.com](https://events.rhylthyme.com) | run-of-show and cue sheets |
| [gym.rhylthyme.com](https://gym.rhylthyme.com) | workouts, supersets and rest intervals |

General entry point: [rhylthyme.com](https://www.rhylthyme.com). Documentation: [docs.rhylthyme.com](https://docs.rhylthyme.com).

## For AI agents (MCP)

A remote [Model Context Protocol](https://modelcontextprotocol.io/) server exposes validation, timing analysis, catalog search, import and publication as annotated tools, plus the schema, an authoring guide and example programs as resources.

```
https://mcp.rhylthyme.com/mcp            generic
https://mcp.rhylthyme.com/kitchen/mcp    + cook_recipe, whats_for_dinner
https://mcp.rhylthyme.com/lab/mcp        + run_protocol, random_protocol, Benchling import
https://mcp.rhylthyme.com/events/mcp     + plan_event, random_event_template
https://mcp.rhylthyme.com/gym/mcp        + start_workout, surprise_workout
```

**Claude Code plugin.** One install connects the hosted server and adds a skill that teaches Claude to write a schedule well (extract the steps before relating them, validate, check for conflicts, work back from a deadline):

```
/plugin marketplace add rhylthyme/rhylthyme-mcp
/plugin install rhylthyme@rhylthyme
```

**Any other MCP client.** Add an endpoint URL as a connector: Claude (Settings → Connectors → Add custom connector), ChatGPT (developer mode → create a connector), Cursor (`{"url": "https://mcp.rhylthyme.com/mcp"}`), or

```bash
claude mcp add --transport http rhylthyme https://mcp.rhylthyme.com/kitchen/mcp
```

Clients that can only launch a command: `pip install rhylthyme-mcp` gives a `rhylthyme-mcp` stdio bridge to the hosted server. No MCP client at all: a single JSON-RPC `POST` works with no handshake, and `llms.txt` on every rhylthyme.com host says how.

Streamable HTTP, stateless. No account or API key is needed for validation, analysis, publishing a timeline or the public catalog; a personal library and recorded runs use your Rhylthyme account through OAuth 2.1. See [rhylthyme-mcp](https://github.com/rhylthyme/rhylthyme-mcp) for the tool reference.

## Repositories

| Repository | What it is |
|---|---|
| [rhylthyme-spec](https://github.com/rhylthyme/rhylthyme-spec) | JSON Schema for programs and environments, annotated with OWL-Time vocabulary. On PyPI as `rhylthyme-spec`. |
| [rhylthyme-cli-runner](https://github.com/rhylthyme/rhylthyme-cli-runner) | The `rhylthyme` command a person types: validate a program file offline, run it in a terminal UI with timers, record runs and calibrate durations from them; `analyze`, `publish` and `generate` call the MCP server. Also the Claude skill's source and the prompt-evaluation harness. On PyPI as `rhylthyme-cli-runner`. |
| [rhylthyme-timeline](https://github.com/rhylthyme/rhylthyme-timeline) | `@rhylthyme/timeline`: zero-dependency timing engine and SVG Gantt renderer with dependency arrows. Tested for parity with the Python validator. |
| [rhylthyme-examples](https://github.com/rhylthyme/rhylthyme-examples) | Example programs and environment definitions across the verticals. |
| [rhylthyme-mcp](https://github.com/rhylthyme/rhylthyme-mcp) | The server an AI assistant talks to: source of the hosted MCP server at `mcp.rhylthyme.com` (self-hostable), the Claude plugin marketplace, and the `rhylthyme-mcp` PyPI package, a stdio bridge to the hosted server. |
| [rhylthyme-docs](https://github.com/rhylthyme/rhylthyme-docs) | Source of docs.rhylthyme.com. |
| [paper](https://github.com/rhylthyme/paper) | The preprint: the language, the runtime, the MCP server, and an evaluation of seven language models authoring schedules from text. |

rhylthyme-mcp and rhylthyme-cli-runner are easy to confuse: the first is what an assistant calls, the second is what you run yourself on a program file, and the second is one of the first's clients. Each README has a side-by-side table.

The web application and the importers (Spoonacular, TheMealDB, protocols.io, Cooklang, Opentrons, Benchling) live in `rhylthyme-server`, which is being prepared for public release.

## Quick start

```bash
pip install rhylthyme-cli-runner rhylthyme-spec
git clone https://github.com/rhylthyme/rhylthyme-examples
rhylthyme validate rhylthyme-examples/programs/breakfast_schedule.json
rhylthyme run      rhylthyme-examples/programs/breakfast_schedule.json
```

A minimal program:

```json
{
  "schemaVersion": "0.1.0",
  "programId": "eggs-and-toast",
  "name": "Eggs and toast",
  "tracks": [
    { "trackId": "eggs", "name": "Eggs", "steps": [
      { "stepId": "whisk", "name": "Whisk", "task": "prep",
        "duration": { "type": "fixed", "seconds": 60 },
        "startTrigger": { "type": "programStart" } },
      { "stepId": "cook", "name": "Cook", "task": "stove",
        "duration": { "type": "variable", "minSeconds": 120, "maxSeconds": 240, "defaultSeconds": 180 },
        "startTrigger": { "type": "afterStep", "stepId": "whisk" } } ] },
    { "trackId": "toast", "name": "Toast", "steps": [
      { "stepId": "toast", "name": "Toast", "task": "toaster",
        "duration": { "type": "fixed", "seconds": 180 },
        "startTrigger": { "type": "afterStep", "stepId": "cook", "event": "start", "offsetSeconds": 60 } } ] }
  ],
  "resourceConstraints": [
    { "task": "prep", "maxConcurrent": 1 },
    { "task": "stove", "maxConcurrent": 2 },
    { "task": "toaster", "maxConcurrent": 1 }
  ]
}
```

Steps in a track run one after another; parallel work goes in separate tracks; every `task` needs a resource constraint; durations and offsets take seconds or strings like `"5m"`.

## License

Apache-2.0 throughout: the specification, tools, examples, timeline engine and MCP server.
