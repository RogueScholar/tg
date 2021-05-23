# Using Lua scripts to enhance telegram-cli

To use Lua with telegram-cli involves the creation of a well-formed Lua script.
You can specify it in the configuration file with the `lua_script` key or from
the command line on invocation with the `-s [script file]` flag.

## Script functions

### To be provided

It should provide several functions, these being the minimum required:

- **on_binlog_replay_end()** - This is called when replays of old events are
  completed. Any updates prior to this call were already received by the client
  some time ago.
- **on_get_difference_end()** - This is called after the first `get_difference`
  call, to indicate all updates were received after the last client execution.
- **on_our_id(_our\_id_)** - This is called to supply requests for details about
  the id of the currently logged-in user.
- **on_msg_receive(_msg_)** - This is called when the client receives a new
  message (`!!` may be called before on_binlog_replay_end, in which case it is
  an old msg).
- **on_msg_history(_msg_)** - This is called when a message is printed from the
  conversation history.
- **on_user_update(_user_,_what\_changed_)** - This is called when querying
  what, if anything, has changed about a user's info since it was last queried
  where `what_changed` is an array of strings.
- **on_chat_update(_user_,_what\_changed_)** - This is called when the client
  receives an announcement of updated info about a user, where `what_changed` is
  an array of strings.
- **on_secret_chat_update(_user_,_what\_changed_)** - This is called when the
  client receives an announcement of updated info about a user it has a secret
  (encrypted) conversation open with, where `what_changed` is an array of
  strings.

### Available to be called

Also, you can call several built-in functions from within the script. Each of
their argument series are appended with two arguments common to all of them:

- `cb_function`
- `cb_extra`

They may return `false` immediately if their contents are malformed, or return
`true` and call `cb_function` in the future, where `cb_function` itself would
have either two (2) or three (3) arguments, in the following order:

- `cb_extra`, a string
- `success`, a boolean (`0` or `1`)
- `result` (if applicable), a string

If you want to pass a particular message to a function, you should pass its
`id`. If you want to pass a peer to a function, you should pass it's
`print_name`. Or you can pass the strings `user#id`, `chat#id`, or
`secret_chat#id` with the numeric id appended as a peer, which works better in
some cases. One such example is if you want to postpone, but are concerned that
`print_name` might change in the meantime.

### Function list

> Arguments shown precede the `cb_function` and `cb_extra` common to all.

- get_contact_list ()
- get_dialog_list ()

- rename_chat (chat, new_name)
- chat_set_photo (chat, file)

- send_typing (peer)
- send_typing_abort (peer)

- send_msg (peer, text)
- fwd_msg (peer, msg)

- send_photo (peer, file)
- send_video (peer, file)
- send_audio (peer, file)
- send_document (peer, file)
- send_text (peer, file)

- load_photo(msg)
- load_video(msg)
- load_video_thumb(msg)
- load_audio(msg)
- load_document(msg)
- load_document_thumb(msg)

- chat_info (chat)
- channel_info (channel)
- user_info (user)

- get_history (peer, limit)

- chat_add_user (chat, user)
- chat_del_user (chat, user)

- add_contact (phone, first_name, last_name)
- rename_contact (phone, first_name, last_name)

- msg_search (peer, text)
- msg_global_search (text)

- mark_read (peer)

- set_profile_photo (file)

- create_secret_chat (user)
- create_group_chat (user, name)

- delete_msg (msg)
- restore_msg (number)

- status_online ()
- status_offline ()

- send_location (peer, latitude, longitude)

### Meta functions

Two other functions exist to handle introspection:

- **postpone (_cb\_function_, _cb\_extra_, _timeout_)** - This will call your
  `cb_function` in the future after a specified number of seconds has elapsed
  (the number of seconds may be doubled).
- **register_interface_function (_name_, _cb\_function_, _cb\_arg_,
  _description_, _arg1\_type_, _arg2\_type_, ...)** - This adds a function to
  the client's Readline interface.
