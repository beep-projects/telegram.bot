# telegram.bot   <img src="resources/telegram.bot.logo.png" alt="telegram.bot" title="telegram.bot" align="right" style="filter: brightness(var(--value, 200%)" data-type="%" height="180"/>
[![GitHub license](https://img.shields.io/github/license/beep-projects/telegram.bot)](https://github.com/beep-projects/telegram.bot/blob/main/LICENSE) [![shellcheck](https://github.com/beep-projects/telegram.bot/actions/workflows/shellcheck.yml/badge.svg)](https://github.com/beep-projects/telegram.bot/actions/workflows/shellcheck.yml) [![GitHub forks](https://img.shields.io/github/forks/beep-projects/telegram.bot)](https://github.com/beep-projects/telegram.bot/network) [![GitHub stars](https://img.shields.io/github/stars/beep-projects/telegram.bot)](https://github.com/beep-projects/telegram.bot/stargazers) ![GitHub repo size](https://img.shields.io/github/repo-size/beep-projects/telegram.bot)  

[![Scc Count Badge](https://sloc.xyz/github/beep-projects/telegram.bot/?category=code)](https://github.com/beep-projects/telegram.bot/) [![Scc Count Badge](https://sloc.xyz/github/beep-projects/telegram.bot/?category=blanks)](https://github.com/beep-projects/telegram.bot/) [![Scc Count Badge](https://sloc.xyz/github/beep-projects/telegram.bot/?category=lines)](https://github.com/beep-projects/telegram.bot/) [![Scc Count Badge](https://sloc.xyz/github/beep-projects/telegram.bot/?category=comments)](https://github.com/beep-projects/telegram.bot/) [![Scc Count Badge](https://sloc.xyz/github/beep-projects/telegram.bot/?category=cocomo)](https://github.com/beep-projects/telegram.bot/) ![visitors](https://visitor-badge.glitch.me/badge?page_id=beep-projects.telegram.bot)



`telegram.bot` is a bash script to send Telegram messages on [Debian](https://www.debian.org/)-based distributions.

Goal is to have the installation foot print as low as possible, but dependencies are [curl](https://curl.se/) and [jq](https://stedolan.github.io/jq/).

This script is based on https://github.com/NicolasBernaerts/debian-scripts/tree/master/telegram 



# Contents

- [Usage](#usage)
- [Install/Update](#install-update)
- [Uninstall](#uninstall)
- [Commands](#commands)
- [Custom configurations](#custom-configurations)
- [Contributing](#contributing)
- [License](#license)



## Usage

[(Back to Contents)](#contents)

For using `telegram.bot`, you need a **Telegram** account and app. See [telegram.org](https://telegram.org/) on how to set this up.  Once you have Telegram installed, you need to create a **bot**. This can be done by talking to **[@BotFather](https://core.telegram.org/bots#6-botfather)** in your Telegram app. [Follow this guide](https://core.telegram.org/bots#6-botfather), or google for it. The **API token** is a string like `110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw` that is required to authorize your requests for communication via the Bot API.  Once you have obtained an **API token** to authorize your bot, you can start using `telegram.bot`.

First thing you should do is to test if your API token is valid

```bash
beep@projects:~$ telegram.bot --test_token 110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw
beep@projects:~$ 
```

The test will finish silently, if the API token is valid, and it will return the Telegram server's reply, if the API token is invalid, e.g. `{"ok":false,"error_code":401,"description":"Unauthorized"}`

Once you have an API token for your bot, you need a **chat id** that the bot should talk to. The chat id is not easy to find, but `telegram.bot` will help you. You have two options. You either make the bot to chat with you directly, then you need your personal chat id. Or you make the bot to chat with a group, then you have to add the bot first to that group. One note about bots in groups: By default, bots can only read commands in groups, where a command is a single words (`[a-z_0-9]{1,32}`) with a prefixed `/`, e.g. `/help`. If you want that your bot can receive all messages of a group, you have to promote it to be a group admin. What ever your choice is, send a message to the group, or to your bot and call the following to get list of chat ids the bot got updates from.

```bash
beep@projects:~$ telegram.bot --get_chatid 60 --bottoken 110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw
[
  {
    "chatid": -1337818101234,
    "name": "condocam.ai"
  },
  {
    "chatid": -575749100,
    "name": "SystaPi"
  },
  {
    "chatid": 8339234211,
    "name": "Beep"
  }
]
```

WIth that information, you are ready to go!

```bash
beep@projects:~$ telegram.bot --chatid 8339234211 --bottoken 110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw --text "Hello World"
```



## Install/Update

[(Back to Contents)](#contents)

The script has build in functionality to copy itself to `/usr/local/bin`, which should be in the path of most Linux distributions. It also resolves its dependencies on `curl` and `jq` via apt, which binds it to Debian-based distributions. For updating the script, you can run the same routine as for installation, or just copy the new version of the script on your own to `/usr/local/bin`.

```bash
wget https://github.com/beep-projects/telegram.bot/telegram.bot
chmod 755 telegram.bot
sudo ./telegram.bot --install
```



## Uninstall

[(Back to Contents)](#contents)

To uninstall the script, you just have to remove it from `/usr/local/bin`. 
You have to decide if you also want to remove `curl` and `jq` and remove these packages on your own.

```bash
sudo rm /usr/local/bin/telegram.bot
sudo apt --purge jq curl
```



## Commands

[(Back to Contents)](#contents)

The script makes use of the official [Telegram Bot API](https://core.telegram.org/bots/api). Please check the API for commands that you would like to get added to `telegram.bot` and start a [new discussion](https://github.com/beep-projects/telegram.bot/discussions/new) for it.

```bash
beep@projects:~$ telegram.bot --help
  telegram.bot: Tool to send a message to a Telegram User or Channel.
  Message is sent from a Telegram Bot and can contain icon, text, image and/or document.
  Main parameters are :
    -h/-?/--help           display this help and exit
    --install              install dependencies, copy telegram.bot to /usr/local/bin and exit (run as root)
    --get_chatid  <timeout> calls getUpdates on the bot for <timeout> seconds and returns the chart id for the first found message
    -test/--test_token     Test if the provided --bottoken is valid
    -del/--delete_commands Delete all commands for the bot identified by --bottoken
    -set/--set_commands "command1=description1" "command2=description2" ... Set the commands for this bot
    -t/--text <text>       Text of the message (use - for piped text)
    -f/--file <file>       File holding the text of the message
    -p/--photo <file>      Image to display
    -d/--document <file>   Document to transfer
  Options are :
    --title <title>        Title of the message
    --html                 Use HTML mode for text content (markdown by default)
    --disable_preview      Don't create previews for links, image and/or document
    --silent               Send message in silent mode (no user notification on the client)
    -q/--quiet             Don't print message to stdout
    -v/--verbose           explain what is being done
    --print_logo           print the telegram.bot logo
    -cid/--chatid <chat-id>    Recipient User or Channel ID
    -bt|-token|--bottoken <bot-token>     Bot Token of your Telegram bot
  Optional icons are :
    --success              Add a success icon
    --warning              Add a warning icon
    --error                Add an error icon
    --question             Add a question mark icon
    --icon <code>          Add an icon by UTF code (ex 1F355)
  Here is an example of piped text :
    echo 'text to be displayed' | telegram --success --text -

```



## help

[(Back to Commands)](#commands)

Displays the help text on command usage

```telegram.bot --help``

## test bot token

[(Back to Commands)](#commands)

