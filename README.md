Sublime3ClojureSetup
====================

Adapted off of https://github.com/jasongilman/SublimeClojureSetup and updated for Sublime Text 3. Currently only tested on Mac machine.


## Prerequisites

  * [Sublime Text 3](https://www.sublimetext.com/3)
  * [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
  * [Leiningen 2](http://leiningen.org/)
    * Use version [2.7.1](https://github.com/technomancy/leiningen/releases/tag/2.7.1) if your repository does not support TLS
    * After installation, create `~/.lein/profiles.clj` and add the following contents:

```
{:user {:dependencies [[org.clojars.gjahad/debug-repl "0.3.3"]]
        :injections [(use '[clojure.pprint :only [pp pprint]])
                     (use 'alex-and-georges.debug-repl)]
        :plugins [[lein-ancient "0.6.15"]]}}
```

See
  * https://clojars.org/org.clojars.gjahad/debug-repl => 0.3.3
  * https://clojars.org/lein-ancient => 0.6.15
for the latest versions and update as necessary


## Setup

### Install Packages

Install [Package Control](https://packagecontrol.io/installation#st3), the Sublime Text package manager, and use it to install the following packages.

  * SublimeREPL
  * lispindent
  * BracketHighlighter (as of 8/3/18 only available on ST3)
  * EnhancedClojure
  * paredit

### Sublime Preference Files

#### Fix match brackets

Add this code to your user preferences

  * Add the following lines to `~/Library/Application\ Support/Sublime\ Text\ 3/Packages/User/Preferences.sublime-settings`

```
// This needs to be disabled since we're using Bracket Highlighter for highlighting brackets
"match_brackets": false
```

#### Fix identification of Clojure symbols

Sublime Text doesn't correctly identify Clojure symbols.

  * Create `~/Library/Application\ Support/Sublime\ Text\ 3/Packages/User/Clojure.sublime-settings` with the following contents.

```
{
  "extensions":
  [
    "cljs"
  ],
  "word_separators": "\\()\"',;@$%^&|[]{}`~"
}
```

#### Disable auto-pairing of single quotes

Clojure uses single quote characters by themselves like `(def my-literal-list '(1 2 3))`. Sublime Text will automatically close single quotes unless disabled. Turn it off by following these steps:

  1. Navigate to `Preferences > Key Bindings` in the Sublime Text menu bar
  2. Copy the entire Default keymap and past into the empty User keymap file in the split screen.
  3. Comment out lines 296 to 329 under the `Auto-pair single quotes` comment


#### Setup Keybindings

Additionally in the User keymap file, append the contents of the [`clojure_keybindings.sublime-keymap`](clojure_keybindings.sublime-keymap) in this repository.

**NOTE:** remove the array brackets when appending since the User keymap already contains the list of keybindings.

##### Keybindings

  * Bindings interacting with the REPL (Require a single Clojure REPL open and running.)
    * __alt + super + l__ - Starts a new Clojure REPL. Make sure to have cusor in an open Clojure file.
    * __ctrl + d__ - Exit the repl
    * __ctrl + l__ - Clear the repl
    * __alt + cmd + r__ - Refresh all code in project by running user/reset. It will fail if a project does not define the user/reset function.
    * __alt + cmd + shift + r__ - The super refresh. If you delete files or rename things sometimes clojure.tools.namespace will have issues. This runs (clojure.tools.namespace.repl/clear) prior to calling reset.
    * __alt + cmd + b__ - Transfer text from the current block to the repl and executes it.
        * This is useful when you have sample code in a clojure file open next to a REPL. Put your cursor within a block of the sample code and invoke the keystroke. The closest block around or near the cursor will be executed in the REPL within the namespace of the file it comes from.
    * __alt + cmd + s__ - Transfers selected text to the repl and executes it. Similar to alt + cmd + b.
    * __alt + cmd + x__ - Runs tests from current test file. Refreshes code in project first.
      * Invoke the keystroke with your cursor in the test file. It will run all the tests in the file in the REPL.
    * __alt + cmd + t__ - Runs a selected test var. Configurable whether this does a refresh first or not.
    * __alt + cmd + a__ - Runs all the tests in the project. Refreshes code in project first. The REPL will still be responsive while tests are running. You can cancel the running tests by refreshing the repl with alt + cmd + r.
    * __alt + cmd + d__ - Print documentation of the selected function.
      * Select a function name in a file including the namespace if part of the call and hit the keystroke.
    * __alt + cmd + c__ - Print source code of the selected function.
    * __alt + cmd + n__ - Print the vars in the selected namespace or namespace alias.
    * __alt + cmd + shift + n__ - Print the documentation of the vars in the selected namespace or namespace alias.
    * __alt + cmd + o__ - Opens the file and scrolls to the line where the selected var is defined. Also works on selected namespaces and with code in jar files.
      * Assumes the `subl` command line argument works. See https://www.sublimetext.com/docs/3/osx_command_line.html to set it up.
    * __alt + cmd + p__ - Pretty print the value that was last returned in the repl.


##### Add Leiningen to SublimeREPL Path

Let SublimeREPL know where Leiningen is installed by navigating to
```
~/Library/Application\ Support/Sublime\ Text\ 3/Packages/SublimeREPL/SublimeREPL.sublime-settings
```
and modifying the `default_extend_env` attribute on line 6 to include the PATH to `lein` installation.
```
"default_extend_env":
  {
    "PATH": "<replace_with_lein_dir>":{PATH}:/usr/local/bin
  }
```
If [Homebrew](https://brew.sh/) was used ot install `lein`, your `<replace_with_lein_dir>` value will be `/usr/local/bin/lein`. If manual installation was used, run
```
which lein
```


#### Configure Lisp Indentation Settings

Configure the Lisp indentation settins by copying the `lispindent.sublime-settings` file from the cloned version of this repository into the following filepath:
```
~/Library/Application\ Support/Sublime\ Text\ 3/Packages/User/
```


#### Speed up text transfer in SublimeREPL

Change `~/Library/Application\ Support/Sublime\ Text\ 3/Packages/SublimeREPL/config/Clojure/Main.sublime-menu` line 22 from
```
"osx":  ["lein", "repl"]
```
to
```
"osx":  ["lein", "trampoline", "run", "-m", "clojure.main"]
```
This greatly improves the speed at which text is sent from a Clojure window to the REPL. Based on answer [here](http://stackoverflow.com/questions/20835788/is-it-normal-to-have-really-slow-text-transfer-in-sublime-text-2-with-the-clojur).


#### Clojure Helpers

The [`ClojureHelpers.py`](ClojureHelpers.py) file provides some helper functions that make working with Sublime REPL and Clojure a little better. These are associated with key bindings. You can also add your own helpers to this file.

  * Copy `ClojureHelpers.py` from the cloned version in this repository to `~/Library/Application\ Support/Sublime\ Text\ 3/Packages/User/`.


#### Clojure Snippets

The [`clojure_snippets.sublime-snippet`](clojure_snippets.sublime-snippet) file contains some useful completions.

  * Copy `clojure_snippets.sublime-snippet` from the cloned version in this repository to `~/Library/Application\ Support/Sublime\ Text\ 3/Packages/User/clojure_snippets.sublime-snippet`.

