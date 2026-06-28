## Prerequisites

We assume you know how to use a terminal. If you're on Windows, please use WSL if possible. If you have questions about anything, please ask.

## Getting started with your device

First, go to <https://trmnl.hpi.church> in your browser and register.
This is going to be the server which will serve images to your TRMNL. An admin will have to approve your account:

![Waiting for approval](images/confirmation.png)

Once approved, log in again. You will now be able to register a device.

<!-- TODO: register device -->

After registering your device, click on your profile in the top right and head to Settings -> API Tokens. Generate a token named "trmnlp" and set it aside.

## Development setup

We're going to be developing plugins using `trmnlp`. To get started, you need to install Ruby 4 on your machine.

Try running

```sh
ruby -v
```

to see whether you have Ruby installed. If this returns version 4, you're done, otherwise install it using your package manager. If you're on Windows, use <https://rubyinstaller.org>.

After installing, the `gem` command (the Ruby package manager) should become available. Run

```sh
gem install trmnl_preview
```

which will install the development tool we need. Now, log in using the token we created earlier. Run

```sh
trmnlp login --server https://trmnl.hpi.church
```

then paste the token and hit Enter.

![Shell showing the output of last four commands](images/trmnlp-setup.png)

## Creating your first plugin

Change into any directory, then run

```sh
trmnlp init your-brand-new-plugin
```

![Terminal open with plugin file list and settings.yml](images/plugin-skeleton.png)

which will create a plugin in `your-brand-new-plugin/`. Change into the new directory and edit `src/settings.yml`. Change the line that says

```yml
name: My Plugin
```

to something else, like

```yml
name: My amazing plugin!
```
Then, run

```sh
trmnlp serve
```

and open the URL it prints (<http://localhost:4567>) in your browser. This will preview your plugin inside the browser and update as you change it. For example, try changing the "Hello, TRMNL!" inside `src/full.liquid` to "Hello, TRMNL x Spark!".

![Preview rendered by trmnlp](images/preview.png)

Once you're done with your changes, run

```sh
trmnlp push
```

which will upload the plugin to the server. Now, you should be able to see your plugin at <https://trmnl.hpi.church/plugins> (alternatively, open the URL `trmnlp push` printed):

![The plugin overview page](images/plugins.png)

Click the name of your plugin. This will open the following page:

![Per plugin page](images/plugin-detail.png)

Here, you can view and edit the code of the plugin, but more importantly, you can add it to playlists. Click the large "add to playlist" button.

![Add to playlist modal](images/playlist.png)

Check the box with your device's name, and create a playlist if one doesn't exist already. Leave the scheduling options as-is and keep the layout as full. Give it any name, like Hackathon.

Turn on your trmnl. Now, the image on it should update to the plugin you created. The dashboard view will update with and only with the device. For previewing a plugin, use `trmnlp` or the preview button on the plugin page.

![The dashboard view](images/applied.png)

## Resources and references

|          |      |            |
|----------|------|------------|
| trmnl framework | <https://trmnl.com/framework> | Complete documentation by trmnl on how to design the dashboards + examples |
| trmnlp | <https://github.com/usetrmnl/trmnlp> | Dev tool we installed earlier |
| trmnl-liquid | <https://github.com/usetrmnl/trmnl-liquid> | TRMNL's custom Liquid filters and tags (date and currency formatting, and more). |
| Liquid docs | <https://shopify.github.io/liquid/> | The base templating language. Loops, conditionals, and filters. |
| Agent Skills | <https://github.com/usetrmnl/trmnl-agent-skills> | TRMNL's official AI skill |

### Building plugins with an LLM

If you're using an AI assistant to write your plugin, you should use the agent skill above, otherwise the agent will probably invent utility classes or fall back to Tailwind.

See the agents skill repo above for installation instructions. If your harness is unsupported, copy all files in `skills/trmnl/references` directly to your repository and instruct your agent to read it.

If you are asked about configuring an MCP server then refuse; the MCP server is part of trmnl.com and not available with BYOS deployments.

## Tips and tricks

- TRMNLs utility classes might look like Tailwind, but they are not. Tailwind classes will be ignored.
  - Use the framework's grayscale classes (`bg--black`, `bg--gray-60`, `bg--gray-30`, `bg--white`) instead of hex colors. The panels here are 2-bit (4 shades of gray); the framework also handles 1-bit and 4-bit devices and falls back via dithering. See <https://help.trmnl.com/en/articles/12386214-grayscale-1-bit-2-bit-4-bit-in-framework>.
- The HTML output rendered by `trmnlp serve` is not entirely accurate, you can turn on a more accurate but slower PNG output in the top bar. This requires imagemagick to be installed globally.
- Use [serverless transforms](https://help.trmnl.com/en/articles/14130649-serverless) to modify incoming API data if needed. Please note that runtime is restricted to 30s per transform, that Ruby is unsupported and that no external libraries are available (in Python, use `urllib` instead of `requests`)
  - When testing with `trmnlp`, transforms require the appropriate interpreter to be installed.
- For Home Assistant or other polled APIs, put the token in `polling_headers` as `Authorization: Bearer <TOKEN>`.

## Glossary

| Term | Acronym | Definition |
| -- | -- | -- |
| Dithering | | Noise filter used to create an illusion of more color depth |
| Bring your own server | BYOS | Term used by trmnl to describe self-hostable open-source servers compatible with their devices |
| Larapaper | | BYOS server written in Laravel, which is what we're using for this hackathon |
| Liquid | | Templating language used by plugins |
| Templating language | | Markup language with control structures. Usually directly transpiles to another markup language, often HTML |

