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

## Tests

The specs in [vim-suite](https://github.com/vimoured/vim-suite) define what it
does: `test/vim-place.vim`.
