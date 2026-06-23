## Prerequisites

We assume you know how to use a terminal. If you're on Windows, please use WSL if possible. If you have questions about anything, please ask.

## Getting started with your device

First, go to <https://trmnl.hpi.church> in your browser and register.
This is going to be the server which will serve images to your TRMNL.

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

```
gem install trmnlp
```

which will install the development tool we need. Now, log in using the token we created earlier. Run

```sh
trmnlp login --server https://trmnl.hpi.church
```

<!-- terminal with the last four commands -->

then paste the token and hit Enter.

## Creating your first plugin

Change into any directory, then run

```sh
trmnlp init your-brand-new-plugin
```

<!-- code editor showing plugin (file explorer, basic files) -->

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

<!-- image of preview -->

Once you're done with your changes, run

```sh
trmnlp push
```

which will upload the plugin to the server. Now, you should be able to see your plugin at <https://trmnl.hpi.church/plugins> (alternatively, open the URL `trmnlp push` printed):

<!-- image of plugin page -->

<!-- TODO: add screen to playlist -->
