# My Neovim setup

My discovery of vim a couple of years ago was quite the revalation, a way to use my terminal to code! When I first tried it the wall of learning vim commands and vim movement was quite intimidating and it took me quite a while to start learning.

After trying vim, something a bit more pre-configured like space-vim I finally landed on Neovim and something just clicked. I have been using Neovim for the last year and I'm pretty happy with my IDE in the terminal.

This is my current Neovim configuration file, p.s I'm using Vim Plug as my plugin manager.

## Programming, Language and Formating Plugins

```vim
Plug 'tmhedberg/SimpylFold'
Plug 'sheerun/vim-polyglot'
Plug 'neoclide/coc.nvim', {'branch': 'release'}
Plug 'jiangmiao/auto-pairs'
Plug 'machakann/vim-highlightedyank'
Plug 'psf/black', {'branch': 'main'}
Plug 'fatih/vim-go', {'do': ':GoUpdateBinaries'}
```

- SimpylFold is for adding python code folding to neovim
- Vim polyglot is a collection of language packs for Neovim
- Coc.nvim extensions to make vim as smart as VSCode, including intellisense
	- Coc prettier for linting javascript
	- Coc html django for formating django template files
	- Coc pyright for formating and linting python files
- Auto pairs

## Making the terminal an IDE

```vim
Plug 'scrooloose/nerdtree'
Plug 'ryanoasis/vim-devicons'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
Plug 'dracula/vim'
Plug 'arcticicestudio/nord-vim'
```

## Tools and nice to haves

```vim
Plug 'vimwiki/vimwiki'
Plug 'junegunn/goyo.vim'
Plug 'junegunn/limelight.vim'
Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && yarn install' }
Plug 'nvim-treesitter/nvim-treesitter'
Plug 'nvim-orgmode/orgmode'
Plug 'ellisonleao/glow.nvim'
Plug 'jghauser/mkdir.nvim'
Plug 'arnarg/todotxt.nvim'
Plug 'MunifTanjim/nui.nvim'
```

## Further configuration

```vim
filetype plugin indent on
syntax on
set splitbelow
set splitright
set number
set autowrite
```

```vim
let g:go_highlight_fields = 1                                                   
let g:go_highlight_functions = 1
let g:go_highlight_function_calls = 1
let g:go_highlight_extra_types = 1
let g:go_highlight_operators = 1 
let g:go_fmt_autosave = 1
let g:go_fmt_command = "goimports"
let g:go_auto_type_info = 1
```

```vim
let g:NERDTreeShowHidden = 1
let g:NERDTreeMinimalUI = 1
let g:NERDTreeIgnore = []
let g:NERDTreeStatusline = ''
```

```vim
let mapleader = ','
let g:vimwiki_list = [{'path': '~/...', 'syntax': 'markdown', 'ext': '.md'}]
```

```vim
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
autocmd! User GoyoEnter Limelight
autocmd! User GoyoLeave Limelight!
```

```vim
aug python
	au!
	autocmd BufWritePre *.py Black
aug END
```

```vim
nnoremap <silent> <C-n> :NERDTreeToggle<CR>
nnoremap <C-J> <C-W><C-J>
nnoremap <C-K> <C-W><C-K>
nnoremap <C-L> <C-W><C-L>
nnoremap <C-H> <C-W><C-H>

imap jj <Esc>
inoremap <expr> <Tab> pumvisible() ? "\<C-n>" : "\<Tab>"
inoremap <expr> <S-Tab> pumvisible() ? "\<C-p>" : "\<S-Tab>"

" Code action on <leader>a
vmap <leader>a <Plug>(coc-codeaction-selected)<CR>
nmap <leader>a <Plug>(coc-codeaction-selected)<CR>

" Format action on <leader>f
vmap <leader>f  <Plug>(coc-format-selected)
nmap <leader>f  <Plug>(coc-format-selected)
" Goto definition
nmap <silent> gd <Plug>(coc-definition)
" Open definition in a split window
nmap <silent> gv :vsp<CR><Plug>(coc-definition)<C-W>L

```

```vim
colorscheme dracula

nmap <Leader>td :ToDoTxtTasksToggle<CR>
nmap <Leader>ta :ToDoTxtCapture<CR>

lua << EOF

-- Load custom tree-sitter grammar for org filetype
require('orgmode').setup_ts_grammar()

-- Tree-sitter configuration
require'nvim-treesitter.configs'.setup {
  -- If TS highlights are not enabled at all, or disabled via `disable` prop, highlighting will fallback to default Vim syntax highlighting
  highlight = {
    enable = true,
    disable = {'org'}, -- Remove this to use TS highlighter for some of the highlights (Experimental)
    additional_vim_regex_highlighting = {'org'}, -- Required since TS highlighter doesn't support all syntax features (conceal)
  },
  ensure_installed = {'org'}, -- Or run :TSUpdate org
}

require('orgmode').setup({
  org_agenda_files = {'~/Documents/org/*', '~/my-orgs/**/*'},
  org_default_notes_file = '~/Documents/org/refile.org',
})

require('todotxt-nvim').setup({
  todo_file = "~/Documents/todo.txt",
  sidebar = {
    width = 20,
    position = "bottom",
    },
  capture = {
    prompt = "> ",
    width = "75%",
    position = "80%",

    }
})
EOF

```
