# Contributing

Our plugin system makes it easy to share Claude skills, commands, hooks, and agents with the whole team. Each plugin is fully independent and completely optional, so you’re free to build your own personal plugins without worrying about impacting others. If a plugin becomes popular, we may eventually make it a default for our teams.

Read more: `https://code.claude.com/docs/en/plugin-marketplaces`

We’d love to see more examples, experiments, and ideas—so feel free to create and share your own plugins!

In the future, we’ll introduce a more structured approach to maintaining plugins in this repository. For now: explore, try things out, and have fun.

## Use claude to improve claude!

1. run `claude` in repository root
2. `/improve-claude [plugin name]` - use this command, which will ask you what you want to expand or how you want for claude to act differently - and it will guide you through creating you own plugin/skill/command following all best practices.
   1. `/create-{component}` - for more granular control (when you know that you want to create a skill/command etc..)

## Resouces

* Overall documentation: `https://code.claude.com/docs/en/sub-agents`
* Skill best practices: `https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices`

## Adding marketplace to a repository by default

1. Register marketplace for plugin discovery
   1. Auto registered in the code base - add a `.claude/settings.json` in the repository root
    ```json
    {
        "enabledPlugins": { },
        "extraKnownMarketplaces": {
            "team-tools": {
                "source": {
                    "source": "github",
                    "repo": "Bison-Office/bison-claude-marketplace"
                }
            }
        }
    }
    ```
    2. `/plugin marketplace add Bison-Office/bison-claude-marketplace`
 2. `claude -> /plugin` - all available plugins should be ready for installation 
    1. For plugins that should be always enabled when working on the codebase, you can expand `enabledPlugins: { "pricing-engine@bison-tools": true/false }` - this will install the plugin automatically and disabled/enable it based on true/false flag given


## Creating Plugins

You can create plugins in two ways:

Follow the official guide:
`https://code.claude.com/docs/en/plugin-marketplaces#create-your-own-marketplace`

or

Take a look at the existing plugins in this repository and use them as examples.

Happy building!

### Guidelines - draft

Each plugin should strive to be indepdendent from one and other

