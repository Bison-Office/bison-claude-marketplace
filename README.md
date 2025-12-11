# Contributing

Our plugin system makes it easy to share Claude skills, commands, hooks, and agents with the whole team. Each plugin is fully independent and completely optional, so you’re free to build your own personal plugins without worrying about impacting others. If a plugin becomes popular, we may eventually make it a default for our teams.

We’d love to see more examples, experiments, and ideas—so feel free to create and share your own plugins!

In the future, we’ll introduce a more structured approach to maintaining plugins in this repository. For now: explore, try things out, and have fun.

## Using plugins

1. Register marketplace for plugin discovery
   1. Auto registered in the code base - add a `.claude/settings.json` in the repository root
    ```json
    {
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


## Creating Plugins

You can create plugins in two ways:

Follow the official guide:
`https://code.claude.com/docs/en/plugin-marketplaces#create-your-own-marketplace`

or

Take a look at the existing plugins in this repository and use them as examples.

Happy building!