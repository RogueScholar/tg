# Telegram messenger CLI

Command-line interface for [Telegram](https://telegram.org), using libreadline
to create a TUI interface. It is a client implementation of the TGL library.

> Note: This is a fork of [`telegram-cli`](https://github.com/vysheng/tg).

## API/Protocol documentation

[Telegram API Documentation](https://core.telegram.org/api) and
[MTproto Protocol Documentation](https://core.telegram.org/mtproto)

## Upgrading to version > 1.0

First of all, the binary is now in the `./bin` folder and is named
`telegram-cli`. So be careful not to use the old binary.

Second, the configuration folder is now `${HOME}/.telegram-cli`.

Third, the database is not compatible with older versions, so you'll have to
login again.

Fourth, in `peer_name`, octothorpes '#' are substituted with '@'. (Doesn't apply
to appending '#%d' to workaround two peers having the same name.)

## Installation

Clone this GitHub repository with the `--recursive` switch to clone the
submodules too:

```shell
git clone --recursive https://github.com/RogueScholar/tg.git && cd tg
```

### Python Support

Python support is limited to Python 3.5 or later. Other versions may work, but
are not tested.

### Linux and BSDs

Install the library dependencies:

- `libreadline` _(required)_
- `openssl` _(required)_
- `libconfig` _(required if using a configuration file)_
- `liblua` _(optional)_
- `libpython` _(optional)_
- `libjansson` _(optional)_

If you do not want to build against one or more of the optional dependencies,
pass one or more of these flags to the configure script: `--disable-libconfig`,
`--disable-liblua`, `--disable-python` or `--disable-json`.

On Ubuntu/Debian this can be accomplished using:

```shell
sudo apt install devscripts libconfig-dev libevent-dev libgcrypt-dev \
libjansson-dev liblua5.3-dev libpython3-dev libreadline-dev libssl1.0-dev \
lua5.3 lua-lgi make zlib1g-dev
```

To build and install the packaege, run:

```shell
dpkg-buildpackage -b
sudo dpkg -i ../telegram-cli_*_amd64.deb
```

On Gentoo:

```shell
sudo emerge -av sys-libs/readline dev-libs/libconfig dev-libs/openssl \
dev-lang/lua dev-libs/libevent dev-libs/jansson dev-lang/python
```

On Fedora:

```shell
sudo dnf install lua-devel openssl-devel libconfig-devel readline-devel \
libevent-devel jansson-devel python-devel libgcrypt-devel
```

On CentOS:

```shell
sudo yum install lua-devel openssl-devel libconfig-devel readline-devel \
libevent-devel jansson-devel python-devel
```

On Archlinux:

```shell
yay -S telegram-cli-git
```

On Milislinux:

```shell
mps kur telegram-cli
```
On FreeBSD:

```shell
pkg install telegram-cli
```

On OpenBSD:

```shell
pkg_add libconfig libexecinfo lua python
```

On openSUSE:

```shell
sudo zypper in lua-devel libconfig-devel readline-devel libevent-devel \
libjansson-devel python-devel libopenssl-devel
```

Then,

```shell
./configure
gmake -j$(nproc)
```

### Other methods to install on Linux

On Gentoo: Use the ebuild provided.

On Arch: Try the
[AUR package](https://aur.archlinux.org/packages/telegram-cli-git)

### MacOS X

The client depends on the
[GNU Readline Library](https://tiswww.case.edu/php/chet/readline/rltop.html) and
[libconfig](https://hyperrealm.github.io/libconfig/), which are not included in
MacOS X by default. You'll have to install these libraries manually.

If using [Homebrew](https://brew.sh/), then:

```shell
brew tap ivoputzer/tg
brew install tg
```

> Thanks to
> [@jfontan](https://github.com/vysheng/tg/issues/3#issuecomment-28293731) for
> this solution.

But if using [MacPorts](https://www.macports.org), this instead:

```shell
sudo port install libconfig-hr
sudo port install readline
sudo port install lua51
sudo port install python34
sudo port install libevent
export \
CFLAGS="-I/usr/local/include -I/opt/local/include -I/opt/local/include/lua-5.1"
export LDFLAGS="-L/usr/local/lib -L/opt/local/lib -L/opt/local/lib/lua-5.1"
./configure && make -j$(nproc)
```

...or the old-fashioned way:

```shell
brew install libconfig readline lua python libevent jansson
export \
CFLAGS="-I/usr/local/include -I$READLINEPATH/include -I$OPENSSLPATH/include" \
CPPFLAGS="-I/usr/local/opt/openssl/include -I$READLINEPATH/include -W" \
LDFLAGS="-L/usr/local/lib -L$READLINEPATH/lib -L$OPENSSLPATH/lib" \
OPENSSLPATH="$(brew --prefix openssl)" READLINEPATH="$(brew --prefix readline)"
./configure && make -j$(nproc)
```

You might have to manually pass the location of OpenSSL to the configure script,
which can be learned from Homebrew like so:

```shell
$ brew info openssl
  openssl: stable 1.0.2e (bottled) [keg-only]
  Poured from bottle /usr/local/Cellar/openssl/1.0.2e_1 (465 files, 11.9M)
```

In this case, the configure script should be run as:

```shell
./configure --with-openssl=/usr/local/Cellar/openssl/1.0.2e_1
```

In other configurations, OpenSSL might reside in `/usr/local/opt`, so you'd use:

```shell
./configure --with-openssl=/usr/local/opt/openssl
```

If you get a Lua error on Sierra, you can configure a compilation without Lua
using:

```shell
./configure --disable-liblua
```

Finally, after the configure script exits successfully, start the build with:

```shell
make -j$(nproc)
```

### Docker

The setup for dockerizing the app is
[here](https://github.com/semenyukdmitry/telegram_cli_docker).

### Other UNIX-likes

If you manage to launch it on other UNIX-like environment, you go girl...and
please let me know. :wink:

### Contacts

If you would like to ask a question, you can write to me on Telegram or to the
GitHub repository (or both). To contact me via Telegram, you should use the
`import_card` method with `000653bf:0738ca5d:5521fbac:29246815:a27d0cda` as the
argument.

### Usage

```shell
./bin/telegram-cli -k <public-server-key>
```

By default, the public key is stored in tg-server.pub in the same folder as the
binary, or in `/etc/telegram-cli/server.pub`. If not, specify where to find it
with `-k`:

```shell
./bin/telegram-cli -k tg-server.pub
```

The client supports <kbd>Tab</kbd>-completion and command history.

A "Peer" refers to the name of the contact or dialog and can be accessed by Tab-
completion as well. For user contacts, the peer name is `FirstName_LastName`
with all whitespace replaced by underscores. For encrypted chats, an exclamation
point is prepended (as in `!FirstName_LastName`) and if two or more peers happen
to have the same name, an octothorpe '#' followed by a sequential Arabic numeral
is appended to the peer name, such as `A_B`, `A_B#1`, `A_B#2`, and so on.

### Supported commands

#### Messaging

- **msg** \<peer\> Text - sends message to this peer
- **fwd** \<user\> \<msg-seqno\> - forward message to user. You can see message
  numbers starting client with -N
- **chat_with_peer** \<peer\> starts one on one chat session with this peer.
  /exit or /quit to end this mode.
- **add_contact** \<phone-number\> \<first-name\> \<last-name\> - tries to add
  contact to contact-list by phone
- **rename_contact** \<user\> \<first-name\> \<last-name\> - tries to rename
  contact. If you have another device it will be a fight
- **mark_read** \<peer\> - mark read all received messages with peer
- **delete_msg** \<msg-seqno\> - deletes message (not completly, though)
- **restore_msg** \<msg-seqno\> - restores delete message. Impossible for secret
  chats. Only possible short time (one hour, I think) after deletion

#### Multimedia

- **send_photo** \<peer\> \<photo-file-name\> - sends photo to peer
- **send_video** \<peer\> \<video-file-name\> - sends video to peer
- **send_text** \<peer\> \<text-file-name> - sends text file as plain messages
- **send_document** \<peer\> \<document-file-name\> - sends document to peer
- **load_photo** \<msg-seqno\> - loads photo to download dir
- **load_video** \<msg-seqno\> - loads video to download dir
- **load_video_thumb** \<msg-seqno\> - loads video thumbnail to download dir
- **load_audio** \<msg-seqno\> - loads audio to download dir
- **load_document** \<msg-seqno\> - loads document to download dir
- **load_document_thumb** \<msg-seqno\> - loads document thumbnail to d/l dir
- **view_photo** \<msg-seqno\> - loads photo to download dir and opens it in the
  default viewer
- **view_video** \<msg-seqno\> - loads video to download dir and opens it in the
  default player
- **view_video_thumb** \<msg-seqno\> - loads video preview to download dir and
  opens it in the default player
- **view_audio** \<msg-seqno\> - loads audio to download dir and opens it in the
  default player
- **view_document** \<msg-seqno\> - loads document to download dir and opens it
  in the default viewer
- **view_document_thumb** \<msg-seqno\> - loads document preview to download dir
  and opens it in the default viewer
- **fwd_media** \<msg-seqno\> send media in your message. Use this to prevent
  sharing info about author of media (though, it is possible to determine
  user_id from media itself, it is not possible get access_hash of this user)
- **set_profile_photo** \<photo-file-name\> - sets user avatar; photo should be
  square, or server will cut out the biggest central square part automatically

#### Group chat options

- **chat_info** \<chat\> - prints info about chat
- **chat_add_user** \<chat\> \<user\> - add user to chat
- **chat_del_user** \<chat\> \<user\> - remove user from chat
- **rename_chat** \<chat\> \<new-name\>
- **create_group_chat** \<chat topic\> \<user1\> \<user2\> \<user3\> ... -
  creates a groupchat with users, use chat_add_user to add more users
- **chat_set_photo** \<chat\> \<photo-file-name\> - sets group chat photo. Same
  limits as for profile photos.

### Channels

- **channel_get_admins** \<channel\> [limit=100][offset=0] - Gets channel admins
- **channel_get_members** \<channel\> [limit=100][offset=0] - Gets channel
  members
- **channel_info** \<channel\> - Prints info about channel (id, members, admin,
  etc.)
- **channel_invite** \<channel\> \<user\> - Invites user to channel
- **channel_join** \<channel\> - Joins to channel
- **channel_kick** \<channel\> \<user\> - Kicks user from channel
- **channel_leave** \<channel\> - Leaves from channel
- **channel_list** [limit=100][offset=0] - List of last channels
- **channel_set_about** \<channel\> \<about\> - Sets channel about info.
- **channel_set_admin** \<channel\> \<admin\> \<type\> - Sets channel admin. 0 -
  not admin, 1 - moderator, 2 - editor
- **channel_set_photo** \<channel\> \<filename\> - Sets channel photo. Photo
  will be cropped to square
- **channel_set_username** \<channel\> \<username\> -Sets channel username info.

#### Search

- **search** \<peer\> pattern - searches pattern in messages with peer
- **global_search** pattern - searches pattern in all messages

#### Secret chat

- **create_secret_chat** \<user\> - creates secret chat with this user
- **visualize_key** \<secret_chat\> - prints visualization of encryption key.
  You should compare it to your partner's one
- **set_ttl** \<secret_chat\> \<ttl\> - sets ttl to secret chat. Though client
  does ignore it, client on other end can make use of it
- **accept_secret_chat** \<secret_chat\> - manually accept secret chat (only
  useful when starting with -E key)

#### Stats and various info

- **user_info** \<user\> - prints info about user
- **history** \<peer\> [limit] - prints history (and marks it as read). Default
  limit = 40
- **dialog_list** - prints info about your dialogs
- **contact_list** - prints info about users in your contact list
- **suggested_contacts** - print info about contacts, you have max common
  friends
- **stats** - just for debugging
- **show_license** - prints contents of GPLv2
- **help** - prints this help
- **get_self** - get our user info

#### Card

- **export_card** - print your 'card' that anyone can later use to import your
  contact
- **import_card** \<card\> - gets user by card. You can write messages to him
  after that.

#### Other

- **quit** - quit
- **safe_quit** - wait for all queries to end then quit
- run `telegram-cli -q` to logout from account

#### Troubleshooting

- If you get this error: `get error FAIL: 38: can not parse arg #1`, it maybe be
  due to an unresolved username. You should use
  `resolve_username channel/group/user_name` before running an action with it.
  _[See this issue for more info](https://github.com/vysheng/tg/issues/823)_
