	[![MIT Licence](https://badges.frapsoft.com/os/mit/mit.svg?v=103)](https://opensource.org/licenses/mit-license.php)

	# Vim for quick and lightweight realtime LaTeX authoring

	- [Header](#vim-for-quick-and-lightweight-realtime-latex-authoring)
	  * [Installation](#installation)
		+ [Python 3](#python-3)
		+ [Vim](#vim)
		+ [Plugins And .vimrc](#plugins-and-vimrc)
		+ [Vimtex Dependencies](#vimtex-dependencies)
		+ [The Snippets! (finally)](#the-snippets-finally)
		+ [Zathura](#zathura)
	  * [Usage](#usage)
	  * [Final Notes](#final-notes)
	  * [Links (docs & solved issues from which this readme is compiled)](#links-section-docs-and-solved-issues-w-answers-from-which-this-readme-has-been-compiled)

	This serves as a thorough introduction to getting setup for all aspects of a Vim workflow for speedy LaTeX authoring using Vimtex with UltiSnip snippets and Zathura PDF viewer. Install instructions are specific for MacOS but these tools can also be installed on any Linux or Windows computer. Thanks to Gilles Castel, a Math undergrad from Belgium who came up with most snippets used, the Vim plugin setup and using Zathura in tandem. His repository with the original snippets is [Found Here](https://github.com/gillescastel/latex-snippets), and his blog post showcasing how various snippets work is [Found Here](https://castel.dev/post/lecture-notes-1/).

	* Vim text editor serves as the foundation of the workflow, with Vimtex plugin for enabling LaTeX editing along with the UltiSnips plugin that enables shortcuts that expand to code snippets.

	* Zathura PDF viewer is used in tandem with Vim in a manner that changes to the LaTex are automatically reflected in the PDF output. The combination of these two tools allows for lightweight and speedy real time output on your LaTeX markdown.

	## Installation 
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	The Vim that comes with MacOS will not suffice for this, a newer Vim and most of the other tools required will be installed with brew package manager. If you do not already have brew installed, proceed after [installing via their official instructions](https://brew.sh/).

	### Python 3
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	Many users who work with LaTeX nowadays tend to have the Anaconda distribution installed already with their PATH variable prefixed by `"install_location"/anaconda3/bin`; in this case you already have Python 3. If you don't have Anaconda, you may have still installed Python separately, check Python at your terminal and see if you get a version 3.X.X, and also check for the existence of pip3. ONLY IF these tools are not there, you'll need to run:
	```sh
	brew install python
	```
	At this point, python3 and pip3 are symlinked into `/usr/local/bin` and you can proceed.

	### Vim
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	Before installing Vim through brew, run `alias python=python3`. Now simply `brew install vim` and `unalias python`. Afterwards quit all your terminals; opening a new one and running `vim --version | grep python` should give you `+python3` in the subsequent output. UltiSnips which uses Python interpolation for more powerful editable snippets requires a Vim instance bound to a Python 3.

	### Plugins And .vimrc
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	Copy over all text in the following .vimrc into your own at `~/.vimrc` and save. 

	```vim
	set tabstop=4
	set shiftwidth=4
	autocmd TextChanged,TextChangedI <buffer> silent write
	set backspace=indent,eol,start

	" Plugins will be downloaded under the specified directory.
	call plug#begin('~/.vim/plugged')

	" Declare the list of plugins.
	Plug 'sirver/ultisnips'
		let g:UltiSnipsExpandTrigger = '<tab>'
		let g:UltiSnipsJumpForwardTrigger = '<tab>'
		let g:UltiSnipsJumpBackwardTrigger = '<s-tab>'

	Plug 'lervag/vimtex'
		set nocompatible
		let &rtp = '~/.vim/plugged/vimtex,' . &rtp
		let &rtp .= ',~/.vim/plugged/vimtex/after'
		filetype plugin indent on
		syntax enable
		let g:vimtex_latexmk_continuous=0
		let g:tex_flavor='latex'
		let g:Tex_DefaultTargetFormat='pdf'
		let g:vimtex_view_enabled=1
		let g:vimtex_view_automatic=1
		let g:vimtex_view_general_viewer='zathura'
		let g:vimtex_view_method='zathura'
		let g:vimtex_quickfix_mode=0
		let g:vimtex_compiler_latexmk = { 
		  \  'callback' : 0,
		  \}

	Plug 'KeitaNakamura/tex-conceal.vim'
		set conceallevel=1
		let g:tex_conceal='abdmg'
		hi Conceal ctermbg=none

	Plug 'dylanaraps/wal'

	" List ends here. Plugins become visible to Vim after this call.
	call plug#end()

	setlocal spell
	set spelllang=en_us
	inoremap <C-l> <c-g>u<Esc>[s1z=`]a<c-g>u
	colorscheme wal
	set background=dark
	set rtp+=~/.vim/UltiSnips/greek
	```
	BEFORE running the plugin install command, if you don't have the `iterm2` terminal app installed and further definitely don't want to either, omit the following two lines for Vim font (first line in plugin section, second at bottom):
	```vim
	Plug 'dylanaraps/wal'
	colorscheme wal
	```

	Next, open `vim` from terminal and type `:PlugInstall`; every plugin mentioned in the .vimrc will be installed into `~/.vim/plugged` as the location is specified in the .vimrc file.

	(Again skip following to "For a few details..." if no iterm) A dependency of the `dylanaraps/wal` plugin must be installed, run `sudo pip3 install pywal` then `wal --theme base16-nord` . Copy the font file `colors-wal.vim` from the directory `~/.cache/wal`, into directory `~/.vim`, but also ensure to rename the file to `wal.vim`. Simply running: 
	```sh
	mv ~/.cache/wal/colors-wal.vim ~/.vim/wal.vim
	```
	after the `wal --theme` command will accomplish all of this.

	For a few details about the non-plugin aspects of the .vimrc, the first two lines set tab size to the size of 4 spaces since I find the default 8 to be obnoxious, and towards the end the `inoremap` is a spellcheck for typing normal English paragraphs in LaTeX documents, misspelled words are highlighted and you can hit `Tab` to correct them. Also, the `set rtp` sets Vim's runtimepath to include another directory you will add later (besides the main LaTeX snippets you will add) to include LaTeX snippets for Greek letters. A gif showing `inoremap` spelling correction in action can be found in Gilles' blog post mentioned at [The Top](#vim-for-quick-and-lightweight-realtime-latex-authoring).

	### Vimtex Dependencies
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	Vimtex requires additional binaries in order to work, that will compile the LaTeX document on user input and output a PDF which you will have open in Zathura side by side. First get the BasicTex distribution which is 90Mb, this is the [Direct Download Link](http://tug.org/cgi-bin/mactex-download/BasicTeX.pkg); Right-Click & Save As. The page the BasicTex download link is available at is [Found Here](https://www.tug.org/mactex/morepackages.html), which itself is linked in this [Doc Page](https://latextools.readthedocs.io/en/latest/install/) for LatexTools. Go through the install process; it will unpack the binaries into `/usr/local/texlive/2019basic/bin/x86_64-darwin`. Run `cd` into that directory, because there is still one more binary `latexmk` which they strangely decided not to include in the pkg file, I say strange since it does the actual compiling! In the directory, call its `tlmgr` binary to install `latexmk` -> `sudo tlmgr install latexmk`. And end it off with the necessary soft link: 
	```sh
	sudo ln -s /usr/local/texlive/2019basic/bin/x86_64-darwin/latexmk /usr/local/bin
	```
	You don't need to do this following step (another soft link) UNLESS you later get the error `bibtex not executable` while in the Usage section (mostly doesn't happen); if you do then remember to come here and repeat the soft link but change `latexmk` to `bibtex`. 

	### The Snippets! (finally)
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	The snippets are prohibitively long to directly include in this markdown file, but they are in this same Gist. Scroll down and see the files `tex.snippets` and `greek/tex.snippets`. The former goes in `~/.vim/UltiSnips` directory, and the latter goes in `~/.vim/UltiSnips/greek/UltiSnips`; you'll have to `mkdir greek/UltiSnips` (which makes both, nested) from inside `~/.vim/UltiSnips` first.

	### Zathura
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	The following instructions come from [this repository](https://github.com/zegervdv/homebrew-zathura). Run `brew tap zegervdv/zathura`, then `brew install zathura` and `brew install zathura-pdf-poppler`. Next you'll need to link the plugins into place:
	```sh
	$ mkdir -p $(brew --prefix zathura)/lib/zathura
	$ ln -s $(brew --prefix zathura-pdf-poppler)/libpdf-poppler.dylib $(brew --prefix zathura)/lib/zathura/libpdf-poppler.dylib
	```

	## Usage
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	Close all open terminals and open a new one, and open up a `.tex` file with `vim <filename.tex>`. 
	Start off a basic document with:
	```latex
	\documentclass{article}
	beg
	```
	You'll immediately notice that as you type beg, it automatically expands to:
	```latex
	\begin{}

	\end{}
	```
	with the cursor inside the curly brackets at `\begin`. Here, type `document`, which will add the same word inside the `\end` tag, and hitting `Tab` afterwards will bring you inside the two tags. Type some text, then to try another snippet first `Esc` out of Insert mode and hit `v` for Visual mode, and select the text you just typed by `option+click` at the beginning and another `option+click` at the end. With the text highlighted, hit `Tab` which deletes the text, type `lr` then hit `Tab` again which will bring back the text surrounded by `\left(` and `\right)` parentheses. This begins to show you the full power of the UltiSnip plugin, which also lets you use regex as part of Python interpolation in snippet expansion. How text selected in Visual mode can be captured to be used in a snippet is [Explained Here](http://vimcasts.org/episodes/ultisnips-visual-placeholder/). 
	Hit `Esc` and then `\ll`, this will start the Vimtex compiler in continuous mode, then in another window go to the same directory where your new `name.tex` file is and run `zathura name.pdf &` and `disown`. Watch as each keystroke re-renders the PDF, enjoy :) .

	## Final Notes
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	I've modified Gilles' `subscript2` snippet in the main tex.snippets file such that x_\ also expands to x_{\} and leaves the cursor inside so you can type greek letters. So por ejemplo, `x_\` + `th` + `Tab` expands to `x_{\theta}`. Notice there was no `Tab` for `x_\`, this is because the subscript notation itself is configured to autoexpand since the headline of the snippet ends in `A`, only the greek letters require a Tab to prevent undesired autoexpansion as MANY other LaTeX keywords can start with the same a-z letter of alphabet so `\"letter"` would infuriatingly give you a greek letter when you meant to type another keyword. 

	Any snippet (such as the subscript ones) you see with `context math()` as its first line will only work inside math contexts, which Gilles has defined as: 

	1. inside a `dm` block:

	(typing `dm` autoexpands to 
	```latex
	[/

	/]
	```
	which is multiline math), 

	OR 

	2. inside a`mk` block:

	(typing `mk` autoexpands to 
	```latex
	$ $
	```
	which is single line math) .

	There is truly SO MUCH you can do with UltiSnips, Gilles also has a sympy snippet where with the `sympy` module installed via pip, you can do symbolic integration and differentiation and it outputs LaTeX. So `sympy` + `Tab` + `integrate(tanh(x),x)` + `Tab` gives you:
	```latex
	x - \log{\left(\tanh{\left(x \right)} + 1 \right)}
	```
	!!!!

	## Links Section (docs and solved issues w/ answers, from which this readme has been compiled)
	[back to header](#vim-for-quick-and-lightweight-realtime-latex-authoring)

	https://www.reddit.com/r/vim/comments/7c7wd9/vim_vimtex_zathura_on_macos/

	https://stackoverflow.com/questions/40077211/e185-cannot-find-color-scheme

	https://github.com/morhetz/gruvbox/issues/219

	https://github.com/junegunn/vim-plug/issues/325

	https://github.com/dylanaraps/pywal/wiki/Getting-Started

	https://github.com/dylanaraps/wal.vim

	https://github.com/dylanaraps/pywal/wiki/Customization

	https://github.com/lervag/vimtex/issues/1420

	https://latextools.readthedocs.io/en/latest/install/

	https://mg.readthedocs.io/latexmk.html

	https://gist.github.com/LucaCappelletti94/920186303d71c85e66e76ff989ea6b62

	https://github.com/lervag/vimtex/issues/1420

	https://latextools.readthedocs.io/en/latest/install/

	https://github.com/lervag/vimtex/issues/1420

	https://github.com/lervag/vimtex/issues/940

	https://github.com/lervag/vimtex/issues/663

	http://www.math.cmu.edu/~gautam/sj/blog/20140310-zathura-fsearch.html

	https://gitter.im/SirVer/ultisnips

	https://github.com/SirVer/ultisnips/issues/1107

	https://github.com/SirVer/ultisnips/issues/1022

	https://github.com/SirVer/ultisnips/issues/850

	https://superuser.com/questions/1115159/how-do-i-install-vim-on-osx-with-python-3-support

	https://jdhao.github.io/2020/01/05/ultisnips_python_interpolation/

	http://witkowskibartosz.com/blog/python_snippets_in_vim_with_ultisnips.html#.Xnw9gtP7TRY

	https://germaniumhq.com/2019/02/07/2019-02-07-Vim-Ultimate-Editing:-UltiSnips/

	http://vimcasts.org/episodes/ultisnips-python-interpolation/

	https://wraihan.com/posts/vimtex-and-zathura/
