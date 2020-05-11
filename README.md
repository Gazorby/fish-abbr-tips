# fish-abbreviation-tips [![Generic badge](https://img.shields.io/badge/Version-v0.5.0-<COLOR>.svg)](https://shields.io/)

[![asciicast](https://asciinema.org/a/322043.svg)](https://asciinema.org/a/322043)

Help you remembering abbreviations by displaying tips when you can use them

## 🚀 Install

Install using fisher :

```console
fisher add Gazorby/fish-abbreviation-tips
```
## 🔧 Usage

Just use your shell normally and enjoy abbreviations tips!

### Adding / removing abbreviations
The plugin automatically track changes when adding/removing abbreviations using `abbr` command (see [behind the scenes](#-behind-the-scenes)), so you won't see tips anymore for an abbreviation that has been erased, and you will get new ones for newly added abbreviations

## 🛠 Configuration

Configuration is done through environment variables.

To avoid spamming your `config.fish`, you can set environment variables using `set -U` once, to make them persistent across restarts and share them across fish's instances

### Default configuration

```fish
ABBR_TIPS_PROMPT "\n💡 \e[1m{{ .abbr }}\e[0m => {{ .cmd }}"
ABBR_TIPS_ALIAS_WHITELIST # Not set

ABBR_TIPS_REGEXES '(^(\w+\s+)+(-{1,2})\w+)(\s\S+)' '(^( ?\w+){3}).*' '(^( ?\w+){2}).*' '(^( ?\w+){1}).*'
# 1 : Test command with arguments removed
# 2 : Test the firsts three words
# 3 : Test the firsts two words
# 4 : Test the first word
```

### Tips prompt

`ABBR_TIPS_PROMPT`

By default, tips will showing up like this :

```console
💡 ga => git add
```

But you customize it using the prompt environment variable. The plugin will replace `{{ .abbr }}` with the abbreviation and `{{ .cmd }}` with the corresponding command.


⚠️ tips are displayed using `echo -e` (interpretation of backslash escapes)


### Alias whitelist

`ABBR_TIPS_ALIAS_WHITELIST`

By default, the plugin ignore user defined functions (aliases), because your aliases names are likely to unique, so there wouldn't be an abbreviation with the same name.

But, in some cases, you may write aliases which wrap exisiting commands to add some hooks before/after the actual command execution. In this special case, as your aliases probably don't alter the original command, you may also have abbreviations using these aliases, so you don't want to ignore them.

To do that, just add alias to the environment variable

### Regexes

`ABBR_TIPS_REGEXES`

If the command dosn't match an abbreviation exactly, then it is tested against some regexes to try extracting a possible abbreviation.

For example, you could have an abbreviation like this :
```console
gcm => git commit -m
```
So you want a tip when typing `git commit -m "my commit"`, but the command doesn't match exactly `git commit -m`.
To tackle this, we have a default regex that will match commands with arguments removed, so your `git commit -m "my commit"` will be tested as `git commit -m`.

You can add such regexes to the `ABBR_TIPS_REGEXES` list, and they will be tested in the order in which they have been added (see [default configuration](#default-configuration)). Keep in mind that only the *first matching group* will be tested (so you must have at least one per regex)

## 🎥 Behind the scenes
In order to not slow down your prompt, the plugin store abbreviations and their corresponding commands in lists (actually simulating a dictionary, as [fish doesn't support dict yet](https://github.com/fish-shell/fish-shell/issues/390)) to avoid iterating over all abbreviations each time you type a command. So retrieving an abbreviation from a command is fast as it doesn't involve any loop.

The plugin will create lists once during installation by calling `_abbr_tips_init` in background (more precisely in spawned shell, because [fish doesn't put functions in background](https://github.com/fish-shell/fish-shell/issues/238)). Then, lists will get updated when you add or remove abbreviation using `abbr` builtin.

## 👍 Inspiration

Inspired by [zsh-fast-alias-tips](https://github.com/sei40kr/zsh-fast-alias-tips) and [alias-tips](https://github.com/djui/alias-tips) zsh plugins


## 📝 License
[MIT](https://github.com/Gazorby/fish-abbreviation-tips/blob/master/LICENSE)
