# Sidekick.el

```text
------------------------------
------------ ----    ----
************ ****   ****
----         ----  ----
************ *********       SIDEKICK
------------ ---------    --------------
       ***** ****  ****
------------ ----   ----
************ ****    ****
------------------------------
```

Sidekick is an **Emacs extension** that's aim is to provide useful information
about a symbol inside a single window. It's still in it's infancy, and at this
point in time only searches for references using
[ripgrep](https://github.com/BurntSushi/ripgrep). It will however be in active
development and I hope to extend it in the near future to support things such as
getting a symbols documentation and extracting other time saving information.

If you experience any issues or there's a feature you would like to recommend,
please open a [new issue](https://github.com/VernonGrant/sidekick.el/issues/new).

<!-- TODO: change this to a raw path once published -->
![Screenshot of sidekick](/assets/images/sidekick.png?raw=true "Sidekick for Emacs")

## Installation

Important: **ripgrep (rg) is required!** Please make sure you have it installed
and available in your system path, see [Installing
ripgrep](https://github.com/BurntSushi/ripgrep#installation).

#### Manual installation

Clone this repository locally, and add this to your `.emacs`:

```lisp
(add-to-list 'load-path "/path/to/sidekick/")
(require 'sidekick)

;; Set some default bindings.
(global-set-key (kbd "C-c k") 'sidekick-at-point)
(global-set-key (kbd "C-c C-k") 'sidekick-search-for-literal)

;; Optional:
;; Maybe define some custom mode, file assosiations?
(sidekick-set-file-associations "php-mode" "*.{php,twig,blade,phtml}")
```

## Usage

#### Default key bindings

The following keybindings are **available inside the Sidekick** window/mode.

| Key   | Command                              | Description                                                                                 |
|-------|--------------------------------------|---------------------------------------------------------------------------------------------|
| `q`   | **sidekick-quit**                    | Closes the Sidekick window and kills it's related buffer.                                   |
| `g`   | **sidekick-refresh**                 | Reruns the previous Sidekick operations, refreshing the results.                            |
| `n`   | **sidekick-open-next-match**         | Displays the next match, opening it's related buffer and moving to the matching symbol.     |
| `p`   | **sidekick-open-previous-match**     | Displays the previous match, opening it's related buffer and moving to the matching symbol. |
| `RET` | **sidekick-open-match**              | Go's directly to the match's symbol, creating a buffer if needed.                           |
| `o`   | **sidekick-open-match-other-window** | Go's directly to the match's symbol, creating a buffer if needed and forcing other window.  |

You can customize keybindings that are local to the Sidekick window, like this:

```lisp
;; Adding custom Sidekick window local bindings.
(add-hook 'sidekick-mode-hook
  (lambda ()
   (local-set-key (kbd "<up>") 'sidekick-open-previous-match)
   (local-set-key (kbd "<down>") 'sidekick-open-next-match)))
```

#### Unbound commands

| Command                         | Description                                                              |
|---------------------------------|--------------------------------------------------------------------------|
| **sidekick-at-point**           | Uses the symbol at point as the symbol and updates the Sidekick results. |
| **sidekick-focus**              | Will focus on the Sidekick window, if available.                         |
| **sidekick-search-for-literal** | Search for a literal string instead.                                     |

You can define custom key bindings for any of the above commands. See the below
example:

```lisp
(global-set-key (kbd "C-c k") 'sidekick-at-point)
(global-set-key (kbd "C-c C-k") 'sidekick-search-for-literal)
```

## Configuration

#### How the projects root directory is determined

Sidekick searches for files relative to your projects root directory. The root
directory is determined by having one of the following files or folders.

- A file named `.sidekick` *(highest priority)*
- A file named `.projectile`
- A git repository `.git`

#### Current available settings

- **sidekick-min-symbol-length:** The minimum symbol length required in order for
	Sidekick to update. The lowest number possible is 2.
  - **Type:** integer
  - **Default:** `2`
  - **Example**:
	```lisp
	(setq sidekick-min-symbol-length 3)
	```

- **sidekick-take-focus:** If non-nil, automatically focus on the
  sidekick window after each update.
  - **Type:** boolean
  - **Default:** `nil`
  - **Example**:
	```lisp
	(setq sidekick-take-focus t)
	```
- **sidekick-window-width:** The width of the sidekick window in fractional percentage.
  - **Type:** float
  - **Default:** `0.25`
  - **Example**:
	```lisp
	(setq sidekick-window-width 0.2)
	```
- **sidekick-window-side:** The Sidekick window's position, left or right.
  - **Type:** symbol
  - **Default:** `'right`
  - **Example**:
	```lisp
	(setq sidekick-window-side 'left)
	```

- **sidekick-hide-footer:** Hides the Sidekick footer branding.
  - **Type:** boolean
  - **Default:** `nil`
  - **Example**:
	```lisp
	(setq sidekick-hide-footer t)
	```

#### Modifying a mode's associated files

The blow list specifies the default mode - file associations. **If a mode's
provided glob pattern is an empty string, Sidekick will just search for files
withe the same extension as the active buffer**

| Mode            | Files                 | Mode            | Files                  |
|-----------------|-----------------------|-----------------|------------------------|
| c++-mode        | `"*.{cpp,h,hh}"`      | markdown-mode   | `"*.md"`               |
| c-mode          | `"*.{c,cc,h,hh}"`     | php-mode        | `"*.{php,phtml,twig}"` |
| cperl-mode      | `"*.{pl,PL}"`         | python-mode     | `"*.py"`               |
| css-mode        | `"*.{css,sass,scss}"` | ruby-mode       | `"*.rb"`               |
| emacs-lisp-mode | `"*.{el,emacs}"`      | rust-mode       | `"*.rs"`               |
| go-mode         | `"*.go"`              | typescript-mode | `"*.ts"`               |
| java-mode       | `"*.java"`            | web-mode        | `""`                   |
| js-mode         | `"*.{js,es,es6}"`     | xml-mode        | `"*.xml"`              |
| json-mode       | `"*.json"`            | yaml-mode       | `"*.yml"`              |

To add or update an existing mode's file associations, the following helper
function has been provided. See the below usage example:

```lisp
;; Adds a new mode, glob pair.
(sidekick-set-file-associations "example-mode" "*.extension-a")
(sidekick-set-file-associations "some-other-mode" "")

;; Updates an existing mode's file globs.
(sidekick-set-file-associations "php-mode" "*.{php,blade,twig}")
```

## Credits

I feel obliged to tank the organizations and people behind some of the tools and educational
resources that was used to complete this project.

- [Free Software Foundation](https://www.fsf.org/)
- Xah Lee, author of [Xah Emacs Tutorial](http://xahlee.info/emacs/index.html)
- Mickey Petersen, author of [Mastering Emacs](https://www.masteringemacs.org/)
- Andrew Gallant (BurntSushi), creator of [Ripgrep](https://github.com/BurntSushi/ripgrep)
