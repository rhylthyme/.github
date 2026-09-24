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

## Make one in a minute: taco night

Carnitas braise "until they shred", which nobody can put a number on, and the
salsa, the margaritas, the tortillas and the table all have to land with them.
One blender, two burners. Here is that dinner as a live timeline:

```bash
python3 -m venv rhylthyme-env && source rhylthyme-env/bin/activate   # Python 3.12 or newer
pip install rhylthyme
rhylthyme publish https://raw.githubusercontent.com/rhylthyme/.github/main/profile/examples/taco-night.json --image taco-night.png --open
```

(Or install nothing: put `uvx --python 3.12 --from rhylthyme-cli-runner` in
front of it.) No account, no API key. It prints a summary, a text chart and

```
Live timeline: https://kitchen.rhylthyme.com?share=c83cdcd3da455fc8
Picture: taco-night.png
```

Open the link on your phone: an ingredient checklist that doubles as a
shopping list, an itinerary with the method under each step, and a timeline
with timers and audio cues. When the pork finally shreds you end the braise
and everything after it moves. The
picture:

![Taco night for six: five tracks converging on the end of an open-ended braise](https://raw.githubusercontent.com/rhylthyme/.github/main/profile/images/taco-night.png)

The hatched bar is the braise, which ends when you say so. The dashed arrows
are steps that start a set time *before* it is due to end (char the tomatoes 30
minutes out, juice the limes 12 minutes out), so if the pork needs another
quarter of an hour, the margaritas wait with it. Now make it yours: save
[the program](https://github.com/rhylthyme/.github/blob/main/profile/examples/taco-night.json), add guacamole or take away a
burner, and `rhylthyme publish taco-night.json`. Or start from a recipe you
did not write: `rhylthyme import https://www.seriouseats.com/the-best-chili-recipe --publish`
turns a page from any of about 580 recipe sites into a timeline (TheMealDB,
Spoonacular, CookLang, protocols.io, Opentrons and Benchling work the same
way). Other ways to make
one: working back from a deadline in a terminal, [rhylthyme-cli-runner](https://github.com/rhylthyme/rhylthyme-cli-runner#a-timeline-in-five-commands-a-birthday-party);
by asking Claude or ChatGPT, [rhylthyme-mcp](https://github.com/rhylthyme/rhylthyme-mcp#try-it-ask-for-a-workout).

## Quick start: import a recipe or protocol

Start from something already written: a recipe page, a protocols.io protocol,
an Opentrons `.py` file, a CookLang file, a Benchling protocol.

```bash
pip install rhylthyme
rhylthyme import https://www.bbcgoodfood.com/recipes/classic-lasagne --publish   # any of ~580 recipe sites
rhylthyme import 52772 -i themealdb                                               # a TheMealDB id -> teriyaki_chicken_casserole.json
rhylthyme import https://raw.githubusercontent.com/Opentrons/Protocols/develop/protocols/007992/rna_isolation.ot2.apiv2.py   # an Opentrons protocol
rhylthyme importers                                                               # what is installed
```

`import` validates the program, writes `<programId>.json`, and with
`--publish` prints a live-timeline URL. `rhylthyme render <file>.json -o fig.png`
draws it. Details: [rhylthyme-importers](https://github.com/rhylthyme/rhylthyme-importers).

## Quick start: from an AI assistant (MCP)

In Claude Code:

```
/plugin marketplace add rhylthyme/rhylthyme-mcp
/plugin install rhylthyme@rhylthyme
```

then ask: *"Plan Thanksgiving for 8 with one oven, eating at 6 pm."* In
Claude, ChatGPT or Cursor, add `https://mcp.rhylthyme.com/mcp` as a
connector instead. No account is needed; the answer is a live-timeline link.

## Quick start: run lab instruments (galago-tools)

Steps can drive real instruments through
[galago-tools](https://github.com/sciencecorp/galago-tools), Science
Corporation's open-source (Apache-2.0) drivers for shakers, incubators, plate
readers, liquid handlers and robot arms. A step names a galago command, and it
ends when the instrument replies:

```bash
pip install "rhylthyme[galago]"
rhylthyme run protocol.json --workcell lab.json   # simulated unless --live
```

![rhylthyme run driving three simulated galago tools](https://raw.githubusercontent.com/rhylthyme/rhylthyme-galago/main/docs/images/terminal-running.png)

Details, a three-tool example and screenshots: [rhylthyme-galago](https://github.com/rhylthyme/rhylthyme-galago).

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
| [rhylthyme-importers](https://github.com/rhylthyme/rhylthyme-importers) | Importers that turn outside sources into programs: recipes from TheMealDB, Spoonacular, CookLang and about 580 recipe sites; protocols from protocols.io, Opentrons `.py` files and Benchling; slide decks. `rhylthyme import <url>` or `rhylthyme-import`. On PyPI as `rhylthyme-importers`. |
| [rhylthyme-timeline](https://github.com/rhylthyme/rhylthyme-timeline) | `@rhylthyme/timeline`: zero-dependency timing engine and SVG Gantt renderer with dependency arrows. Tested for parity with the Python validator. On PyPI as `rhylthyme-timeline` (`rhylthyme render`, runs on Node). |
| [rhylthyme-galago](https://github.com/rhylthyme/rhylthyme-galago) | Runs instrument steps on lab instruments through [galago-tools](https://github.com/sciencecorp/galago-tools) (Science Corporation, Apache-2.0): `rhylthyme run --workcell`, simulated unless `--live`, with failure handling and command checks. On PyPI as `rhylthyme-galago`; `pip install "rhylthyme[galago]"`. |
| [rhylthyme-examples](https://github.com/rhylthyme/rhylthyme-examples) | Example programs and environment definitions across the verticals. |
| [rhylthyme-mcp](https://github.com/rhylthyme/rhylthyme-mcp) | The server an AI assistant talks to: source of the hosted MCP server at `mcp.rhylthyme.com` (self-hostable), the Claude plugin marketplace, and the `rhylthyme-mcp` PyPI package, a stdio bridge to the hosted server. |
| [rhylthyme-docs](https://github.com/rhylthyme/rhylthyme-docs) | Source of docs.rhylthyme.com. |
| [paper](https://github.com/rhylthyme/paper) | The preprint: the language, the runtime, the MCP server, and an evaluation of seven language models authoring schedules from text. |

`pip install rhylthyme` installs rhylthyme-cli-runner, rhylthyme-importers and rhylthyme-timeline together; `pip install "rhylthyme[galago]"` adds rhylthyme-galago. rhylthyme-mcp and rhylthyme-cli-runner are easy to confuse: the first is what an assistant calls, the second is what you run yourself on a program file, and the second is one of the first's clients. Each README has a side-by-side table.

The web application lives in `rhylthyme-server`, which is being prepared for public release.

## Quick start

```bash
pip install rhylthyme          # the rhylthyme command, the importers and the renderer
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

Apache-2.0 throughout: the specification, tools, examples, timeline engine, MCP server and galago integration. rhylthyme-galago includes material derived from galago-tools (Copyright 2025 - Science Corporation, Apache-2.0); see its NOTICE.
