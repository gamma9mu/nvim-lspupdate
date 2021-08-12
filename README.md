**IMPORTANT!** v0.9.0 is the last version written in **Lua**. Past that, the project
has moved to [**Fennel**](https://fennel-lang.org/). If you want to continue using
this project, you will need a [**Hotpot**](https://github.com/rktjmp/hotpot.nvim).

---

# Neovim LSP Update

![Test](https://github.com/alexaandru/nvim-lspupdate/workflows/Test/badge.svg)
![Open issues](https://img.shields.io/github/issues/alexaandru/nvim-lspupdate.svg)
![License](https://img.shields.io/badge/License-MIT-blue.svg)

Updates installed (or auto installs if missing) LSP servers, that are already
configured in your `init.vim`.

It does NOT handle (auto)removals, i.e. if you had a LSP
server installed and then removed it's config, it will NOT
remove it. Only installs and/or updates are supported.

You can see it below in action:

https://user-images.githubusercontent.com/85237/129180498-11457175-5cfb-4085-a2a0-51745f56ee70.mp4

## Dependencies

- [Neovim 0.5+](https://github.com/neovim/neovim/releases/tag/v0.5.0)
- [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig)
- [Hotpot](https://github.com/rktjmp/hotpot.nvim)

NOTE: your user must be able to perform installs of packages corresponding to the LSPs
you will be using. I.e. if you install `npm` based LSPs, then you must be able to
run `npm i -g ...` successfully, **WITHOUT** sudo. That is the best practice anyway,
you should in general install "global" packages (whether we talk about NodeJS, Ruby,
Python or whatever) under your user and not at system level.

Please refer to http://npm.github.io/installation-setup-docs/installing/a-note-on-permissions.html
for setup instructions for NodeJS (which covers many of the LSPs available). For
LSPs in other languages, please refer to their own documentation for installing,
and best practices for setting up the environment.

## Install

Add `alexaandru/nvim-lspupdate` plugin to your `init.vim`, using your favorite
package manager, e.g.:

```
packadd nvim-lspupdate
```

## Config

I wrote this plugin as I did NOT want to manage installs manually anymore,
therefore I made it so that it requires NO configuration. It should work
out of the box for the supported configurations ([see status](#status)).

You can however override any (or all) of the commands used (which you can
[see here](fnl/lspupdate/config.fnl#L115)) by defining a `g:lspupdate_commands`
dictionary in your `init.vim`, i.e.:

```VimL
let g:lspupdate_commands = {'pip': 'pip install -U %s'}
```

Any commands you define will be added to the existing commands (and override
them) so you only need to define the ones you actually want to modify, not the
whole table.

Please refer to the comments in `config.lua` above mentioned for the keys
and values (commands) to use in that dictionary.

## Usage

See [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig#quickstart) on
how to setup the LSP servers' configuration. Once you have them configured
(or any time later, for updates), then run:

```
:LspUpdate [dry]
```

and it will install any missing LSP servers as well as update the existing ones,
where possible (see below).

Hint: use `:checkhealth lspconfig` before/after to verify that the LSPs were
installed.

If the dry parameter is passed, then commands are only printed but not actually run.

The installs are async (using jobstart()) with one exception (gh_bin install type,
see below) so you can just fire & forget, and sometime later, check with `:messages`.

When all the jobs are completed it will print an "All done!" message.

## Status

Mix of **`stable`**, `beta` and <s>not (yet) supported</s>.

About 2/3 of the servers have [a config and a command](fnl/lspupdate/config.fnl)
defined. Of those, the following were battle tested by me or others: **`npm`**,
**`pip`**, **`go`**, **`cargogit`**, **`r`**, **`gem`** and **`gh_bin`**. These
are what I consider **`stable`**.

The others that do have a config and a command defined, but were not yet
tested (`cargo`, `nix`, etc.) I would consider `beta`. In theory, they may
work, just need beta testers :) If you do use them and they work, please
let me know so I can update this README accordingly.

Those that do not even have a config or command defined are, obviously,
not supported.

### Github binary releases

We now have support for Github binary releases (gh_bin "pseudo command") with
`terraform-ls` being the first to be tested successfully, on Linux.

To support this type of update, there are additional dependencies/requirements:

- `curl` (used to download the .zip files);
- `unzip` (used to unzip them).
- the repo name, .zip file prefix and binary file inside the .zip are identical
  (i.e. hashicorp/terraform-ls has terraform-ls.\*.zip assets which include a
  terraform-ls binary, well except for Windows, which also has .exe suffix).

## Roadmap

In no particular order:

- make the config configurable by end users (so they can override
  particular entries and use alternate sources/versions/etc.);
- ensure it runs well on all of {Linux,MacOS,Windows};
- integration tests.
