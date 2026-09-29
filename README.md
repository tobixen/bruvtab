# BruvTab

![GitHub](https://img.shields.io/github/license/pschmitt/bruvtab)
![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/pschmitt/bruvtab)
[![PyPI version](https://badge.fury.io/py/bruvtab.svg)](https://badge.fury.io/py/bruvtab)
[![Mozilla Add-on](https://img.shields.io/amo/v/469b5c80160a48cda84c)](https://addons.mozilla.org/en-US/firefox/addon/469b5c80160a48cda84c/)

Control your browser's tabs from the terminal.

BruvTab is a fork of [brotab](https://github.com/balta2ar/brotab), originally created by Yuri Bochkarev.

## Features

* **Cross-browser**: Support for Firefox, Chrome, Chromium, and Brave.
* **Unified interface**: List and manage tabs from multiple browsers in a single view.
* **Scriptable**: Ideal for use with `fzf`, `rofi`, or custom shell scripts.
* **JSON support**: Global `--json` flag with pretty output and terminal colors.
* **Search & Index**: Index tab contents and search them using SQLite FTS5.
* **Rich Output**: Colorized help and tables using the `rich` library.
* **Media control**: See which tabs are playing or muted, and play, pause, mute or unmute them.
* **Flexible tab selection**: Refer to tabs by ID, by a title or URL fragment, or with `--playing` / `--muted`.
* **Browser targeting**: Restrict any command to one browser with `--firefox`, `--chrome`, `--chromium`, `--brave` or `--client`.

## Usage

```txt
Usage: bruvtab [-h] [--target TARGET_HOSTS] [--client CLIENT_SELECTOR] [--firefox] [--chrome]
               [--chromium] [--brave] [-j] [--debug] [--no-wrap]
               {move,list,tabs,close,activate,active,play,pause,mute,unmute,screenshot,search,query,index,new,open,navigate,update,words,text,html,dup,windows,clients,install} ...

bruvtab (bruvtab = Browser Tabs) is a command-line tool that helps you manage browser tabs. It can
help you list, close, reorder, open and activate your tabs.

Positional Arguments:
  {move,list,tabs,close,activate,active,play,pause,mute,unmute,screenshot,search,query,index,new,open,navigate,update,words,text,html,dup,windows,clients,install}
    move                Move tabs around. This command lists available tabs and runs the editor.
                        In the editor you can 1) reorder tabs -- tabs will be moved in the browser
                        2) delete tabs -- tabs will be closed 3) change window ID of the tabs --
                        tabs will be moved to specified windows
    list (tabs)         List available tabs. The command will request all available clients
                        (browser plugins, mediators), and will display browser tabs in the
                        following format: "<prefix>.<window_id>.<tab_id><Tab>Page title<Tab>URL"
    close               Close specified tab IDs. Tab IDs should be in the following format:
                        "<prefix>.<window_id>.<tab_id>". You can use "list" command to obtain tab
                        IDs (first column)
    activate            Activate given tab ID. Tab ID should be in the following format:
                        "<prefix>.<window_id>.<tab_id>"
    active              Display active tab for each client/window in the following format:
                        "<prefix>.<window_id>.<tab_id>"
    play                Play audio/video elements in a tab. Defaults to active tabs.
    pause               Pause audio/video elements in a tab. Defaults to the first currently
                        audible tab.
    mute                Mute a browser tab. Defaults to the first currently audible tab.
    unmute              Unmute a browser tab. Defaults to active tabs.
    screenshot          Return base64 screenshot in json object with keys: 'data' (base64 png),
                        'tab' (tab id of visible tab), 'window' (window id of visible tab), 'api'
                        (prefix of client api). Optionally target a specific tab ID.
    search              Search across your indexed tabs using sqlite fts5 plugin.
    query               Filter tabs using chrome.tabs api.
    index               Index the text from browser's tabs. Text is put into sqlite fts5 table.
    new                 Open new tab with the Google search results of the arguments that follow.
                        One positional argument is required: <prefix>.<window_id> OR <client>. If
                        window_id is not specified, URL will be opened in the active window of the
                        specifed client
    open                Open URLs from arguments or stdin (one URL per line). The optional first
                        argument is <prefix>.<window_id> OR <client>. If window_id is not
                        specified, URLs will be opened in the active window of the specified
                        client. If no client is specified, the first ready client is used. If
                        window_id is 0, URLs will be opened in new window.
    navigate            Navigate to URLs. There are two ways to specify tab ids and URLs: 1.
                        stdin: lines with pairs of "tab_id<tab>url" 2. arguments: bruvtab navigate
                        <tab_id> "<url>", e.g. bruvtab navigate b.20.1 "https://google.com". Stdin
                        has the priority.
    update              Update tabs state, e.g. URL. There are two ways to specify updates: 1.
                        stdin, pass JSON of the form: [{"tab_id": "b.20.130", "properties":
                        {"url": "http://www.google.com"}}] Where "properties" can be anything
                        defined here:
                        https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API
                        /tabs/update. 2. arguments, e.g.: bruvtab update -tabId b.1.862
                        -url="http://www.google.com" +muted
    words               Show sorted unique words from all active tabs of all clients or from
                        specified tabs. This is a helper for webcomplete plugin that helps
                        complete words from the browser
    text                Show text from all tabs or from specified tabs
    html                Show html from all tabs or from specified tabs
    dup                 Display reminder on how to show duplicate tabs using command-line tools
    windows             Display available prefixes and window IDs, along with the number of tabs
                        in every window
    clients             Display available browser clients (mediators), their prefixes and address
                        (host:port), native app PIDs, and browser names
    install             Configure browser settings to use bruvtab mediator (native messaging app)

Options:
  -h, --help            show this help message and exit
  --target TARGET_HOSTS
                        Target hosts IP:Port
  --client, --browser CLIENT_SELECTOR
                        Target client prefix or browser name
  --firefox             Target Firefox clients
  --chrome              Target Chrome clients
  --chromium            Target Chromium clients
  --brave               Target Brave clients
  -j, --json            Pretty JSON output (colored on terminals)
  --debug               Print debug details for command failures
  --no-wrap             Disable wrapping of table columns
```

### Examples

```bash
# List tabs whose title or URL contains "github"
bruvtab list github

# Which tabs are making noise right now?
bruvtab list --playing

# Mute the first audible tab, or close all of them
bruvtab mute --playing
bruvtab close --playing

# Pause media in a tab picked by title/URL fragment
bruvtab pause youtube

# Screenshot a specific tab after waiting two seconds
bruvtab screenshot --wait 2 --raw b.1.862 > tab.png
```

## Installation

### Standard (`uv`)

1. Install command-line client:

```bash
uv tool install bruvtab
```

2. Install native app manifests:

```bash
bruvtab install
```

3. Install Browser extension:

* **Firefox**: Prefer the signed XPI attached to each GitHub release, or use the flake's self-hosted XPI output (`firefoxAddon`, alias `firefoxXpi`). The release workflow publishes listed AMO builds and signs separate self-distributed XPIs for GitHub releases.
* **Chrome/Brave/Chromium**: Prefer the Chrome Web Store build once published. The release workflow also uploads Chrome CRX/ZIP artifacts and a self-hosted update manifest to GitHub releases.

### Shell completions

BruvTab ships dynamic completions for **bash** and **zsh** via `argcomplete`.

For `pipx`, `uv tool`, or `pip` installs, enable them in your shell:

```bash
eval "$(register-python-argcomplete --shell bash bruvtab)"
eval "$(register-python-argcomplete --shell zsh bruvtab)"
```

The Nix package installs completion files into the standard locations:

* `share/bash-completion/completions/bruvtab`
* `share/zsh/site-functions/_bruvtab`

### NixOS / Home Manager

Add `bruvtab` as an input to your flake. For Chromium/Home Manager, you can either pin the packaged CRX from the flake output or point Chrome at the published update manifest URL.

Using the packaged CRX directly:

```nix
{ config, pkgs, inputs, ... }:
let
  bruvtabPkg = inputs.bruvtab.packages.${pkgs.system}.bruvtab;
  bruvtabCrx = inputs.bruvtab.packages.${pkgs.system}.chromeCrx;
  bruvtabFirefoxAddon = inputs.bruvtab.packages.${pkgs.system}.firefoxAddon;
  # The Extension ID is calculated from the private key generated during build
  extensionId = builtins.readFile "${bruvtabCrx}/extension-id";
in
{
  programs.firefox.profiles.default.extensions.packages = [
    bruvtabFirefoxAddon
  ];

  programs.chromium = {
    enable = true;
    extensions = [
      {
        id = extensionId;
        crxPath = "${bruvtabCrx}/bruvtab.crx";
        inherit (bruvtabPkg) version;
      }
    ];
    nativeMessagingHosts = [ bruvtabPkg ];
  };
}
```

Using the published update manifest URL for auto-updates from GitHub releases:

```nix
{
  programs.chromium.extensions = [
    {
      id = "edpgjheobdplebiikjgjgpmonakingef";
      updateUrl = "https://github.com/pschmitt/bruvtab/releases/latest/download/bruvtab-chrome-updates.xml";
    }
  ];
}
```

## Development

### Setup

```bash
# Setup virtual environment and install dependencies
uv sync --all-extras
```

```bash
# Install native manifests for testing
bruvtab install --tests
```

### Testing

Run all tests:

```bash
just test-all
```

Or run individual suites:

```bash
just unit-test
just smoke-test
just integration-test  # Requires Docker
```

## Related Projects

* [TabFS](https://github.com/osnr/TabFS) -- mounts tabs info a filesystem using FUSE
* [dudetab](https://github.com/CRImier/dudetab) -- collection of useful scripts on top of bruvtab
* [ulauncher-bruvtab](https://github.com/brpaz/ulauncher-bruvtab) -- Ulauncher extension for bruvtab
* [cmp-bruvtab](https://github.com/pschmitt/cmp-bruvtab) -- bruvtab completion for nvim-cmp

## Authors

* **Philipp Schmitt** ([@pschmitt](https://github.com/pschmitt)) - Maintainer
* **Yuri Bochkarev** ([@balta2ar](https://github.com/balta2ar)) - Original Creator

## License

MIT
