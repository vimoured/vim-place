# vim-place

A library that gives Vim plugins a place to keep their files. It has no commands
or mappings of its own, so you only need it when another plugin requires it.

Each plugin gets a directory:

- **In a git repository:** inside the git directory, so the files stay out of
  commits and are still there after a work tree is removed.
- **Outside a repository:** in a state folder for that directory, under
  `$XDG_STATE_HOME` (default `~/.local/state`).

**Vim or Neovim:** tested on Vim 9.1 and newer. It's plain Vimscript, so it may
work on Neovim, but that's untested.

## Install

Needs [vim-fugitive](https://github.com/tpope/vim-fugitive).

```vim
Plug 'tpope/vim-fugitive'
Plug 'vimoured/vim-place'
```

## Use

### `roumail_place#dir(name [, opts])`

Returns the directory for `name`, creating it if it's missing:

```vim
let l:dir = roumail_place#dir('my-plugin')
call writefile(l:lines, l:dir . '/notes.txt')
```

Use your plugin's name. Call it each time rather than storing the path, because
the answer depends on the buffer you're in.

| Where | Directory |
| --- | --- |
| In a repository | `<git dir>/<name>`, shared by all its work trees |
| In a repository, with `worktree` set | `<the work tree's git dir>/<name>`, removed with the work tree |
| Outside a repository | `<$XDG_STATE_HOME>/<directory name>-<hash>/<name>` |

`opts` is a dictionary:

| Option | Default | What it does |
| --- | --- | --- |
| `bufnr` | The current buffer | Use this buffer's repository. |
| `worktree` | `0` | `1` keeps the files with the work tree, for files of no use after it's gone. |

### `roumail_place#get([bufnr])`

Returns where a plugin is working, for the current buffer or `bufnr`:

| Key | In a repository | Outside a repository |
| --- | --- | --- |
| `dir` | The work tree | The current directory |
| `repo` | `1` | `0` |

A buffer that isn't in a repository uses the current directory's repository, for
both functions.

## The save contract

A plugin that saves something here (a list, a buffer) to a file gives it two
commands, `:{Thing}Save` and `:{Thing}Load`, which take their arguments as
below. How each stores its thing differs; what you type doesn't. A plugin's
README lists where it deviates, and why.

Browsing the saves isn't part of it: a plugin that offers that shows them as
vim-lists' [list of saves](https://github.com/vimoured/vim-lists#a-list-of-saves).

### `:{Thing}Save[!] [name|path|dir/]`

| Argument | Writes to |
| --- | --- |
| none | the file this thing was last saved to or loaded from, else a new file in the plugin's directory |
| a name | `<the plugin's directory>/<name><ext>`, saved over without `!` |
| a path | that file; another file already there only with `!` |
| a directory | a new file in it |

### `:{Thing}Load [name|path]`

| Argument | Reads |
| --- | --- |
| none | the newest save in the plugin's directory |
| a name | `<the plugin's directory>/<name><ext>` |
| a path | that file |

### Both

- **The plugin's directory** is `roumail_place#dir()`'s for it, scoped to what
  its saves belong to: the repository (the default), a work tree (`worktree`),
  or something the plugin names under its directory, such as a branch
  (`<plugin>/<branch>`). A name, the newest save and a new file are all of
  that scope, and the file a thing was last saved to or loaded from is
  remembered per scope.
- **A name or a path:** a path has a `/` or starts with `~` or `.`
  (`./notes.json`); anything else is a name, typed and shown without its
  extension.
- **A directory:** one that exists, or a path ending in `/`. Missing directories
  are made.
- **A new file** is named for the time, `20261113-142501<ext>`, with `-2`, `-3`
  added for another in the same second.
- **The file a thing was saved to or loaded from** is the one it saves to again
  without an argument, and a path to it needs no `!`.
- **Newest** is by the time the file was last written.
- **A failed write** is an error and changes nothing: the thing isn't taken as
  saved, and isn't tied to the file.
- **Messages** say the file written or read. An error says what's missing: no
  such file, no save of that name, nothing saved.
- **`<Tab>`** completes the saves' names, newest first, and files once the
  argument is a path.
- **vim-place is needed** for a name and for no argument, which use the plugin's
  directory; a path or a directory works without it.

## Tests

The specs in [vim-suite](https://github.com/vimoured/vim-suite) define what it
does: `test/vim-place.vim`.
