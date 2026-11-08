# vim-place

Where Vim plugins keep their files: inside the repository's git directory, so
they stay out of commits and outlive a work tree, or in a per-directory state
folder outside a repository. Plugins call it; it has nothing to type.

**Vim or Neovim:** tested on Vim 9.1 and newer. It's plain Vimscript, so it may
work on Neovim, but that's untested.

## Install

Needs [vim-fugitive](https://github.com/tpope/vim-fugitive).

```vim
Plug 'tpope/vim-fugitive'
Plug 'vimoured/vim-place'
```

## Functions

| Function | What it does |
| --- | --- |
| `roumail_place#dir(name [, opts])` | The directory for `name`, created if missing: `<git dir>/<name>`, else `<$XDG_STATE_HOME>/<place>/<name>`. |
| `roumail_place#get([bufnr])` | Where a plugin is working, as `{dir, repo}`: the work tree with `repo` 1, else the current directory with `repo` 0. |

Name the directory after your plugin, and call `dir()` each time rather than
storing the path.

| Option | Default |
| --- | --- |
| `bufnr` | The current buffer: whose repository it is. |
| `worktree` | `0`. `1` uses the work tree's own git dir, which git removes with the work tree. |

## Tests

The specs in [vim-suite](https://github.com/vimoured/vim-suite) define what it
does: `test/vim-place.vim`.
