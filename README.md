# oho
Takes your colorful terminal output and converts it to HTML for sharing.
(only html output)


![screenshot example](https://github.com/masukomi/oho/blob/master/docs/screenshot.jpg?raw=true)

That's just a sample. oho supports ANSI 3/4 bit (basic and high intensity), 8 bit, 
& 24 bit color codes as well as ITU's T.416 / 8613-6  color codes! 
That's 16,777,216 possible colors. Make your terminal
output beautiful. oho will handle it just fine.

## Usage

Pipe your colorful terminal output to oho and it will spit out HTML.

I've included a test script in the docs directory for you to try it out with.

Run `docs/colortest.sh` to see what the output looks like in your terminal. 

Then pipe it to `oho` to see the html: `docs/colortest.sh | oho`

Now, save it to a file so that you can open it with your browser: 

`docs/colortest.sh | oho > colotest.html`

Does your terminal have a dark background? Pass the `-d` option to turn on "dark
mode" (a black background with white foreground text). Want to get even more
specific? You can use any valid css color for the foreground and background
colors. See the usage output below.

```text
Usage: <some command> | oho [-d][-v] [-b <background color>] [-f <foreground color>] [-t <page title>] > html_output.html
    -d, --dark                       Dark mode
    -b background, --background=background
                                     Sets the background color. Any CSS color will work.
    -f foreground, --foreground=foreground
                                     Sets the foreground color. Any CSS color will work.
    -s styling, --styling=styling    Additional CSS styling. Will be stuck in a style block.
    -t title, --title=title_string   Sets the html page title.
    -v, --version                    Show the version number
    -h, --help                       Show this help
```

### Keeping the colors
When piping information between command line apps, the terminal escape codes are stripped. For example `git diff HEAD^` would give you a colorized diff of your code, but when you pipe it to another tool - like oho - it becomes plain, colorless, text. To maintain those colors you have two options: `script` and `unbuffer`. `unbuffer` will make your life easier, but `script` is probably already installed on your system.

#### unbuffer
`unbuffer` is part of "Expect", which is [an odd collection of TCL scripts](https://core.tcl-lang.org/expect/index) for automating various tasks.

You can install it with homebrew:

```bash
brew install expect
```
or using apt-get:
```bash
apt-install expect
```

Once it's installed you can preface the script you want to invoke with `unbuffer``, and then pipe that on to oho convert it to HTML.

```bash
unbuffer git --no-pager diff HEAD^ | oho
```

**Note**: Unbuffer works by convincing your tool that it's outputting to
an interactive buffer. Some tools, like git, expect you to page through
long output in interactive mode. You'll need to disable that on calls that
go through unbuffer. With git you just append `--no-pager` immediately after
`git` For example: `git --no-pager diff HEAD^`

#### script
`script` is probably on your system already on your system, and it works well, but it's not really intended for this purpose and thus requires additional arguments to turn off its normal behavior.

```
script -q /dev/null git log --stat -n 4 | oho
      # | |         |                       ^ converts it to html
      # | |         ^ the command to run
      # | ^ we don't want the file it writes
      # ^ don't add status messages
```


#### An alternative to your browser
Saving it to a file and then opening that file in a browser is annoying. There are some hacks you can do to get around it, but [Fenestro](https://fenestro.xyz) is happy to save you that trouble.

```sh
docs/colortest.sh | oho -d | fenestro --name "colortest"
```

Voilà a window opens with your pretty HTML loaded into it. 


## Installation

### macOS & Linux via Homebrew
```sh
brew tap masukomi/homebrew-apps
brew install oho
```

### Building from source

oho is written in [Crystal](https://crystal-lang.org/) so you'll need to install
the crystal compiler. After that you just clone down this repository, `cd` into it and run 

```bash
crystal build src/oho.cr
```

An `oho` executable will be created in the current directory. Just move that
into one of the directories in your `PATH`` and follow the Usage instructions.


## Caveats
There are many, _many_ escape sequences that are used in terminals. Oho supports ANSI 3/4 bit (basic and high intensity), 8 bit, 
& 24 bit color codes as well as ITU's T.416 / 8613-6  color codes. 

"Screen mode" escape sequences are _not_ supported. In general, this isn't going to be an issue.
For example: `^[=5;7h` would tell a terminal to render as 320 x 200 in Black & White mode. 
To support that would require reformatting your text, and making a judgement call about which 
colors should be converted to black, and which to white. Currently oho does not address 
formatting issues or make judgement calls about colors. If, however, you feel like implementing this, 
Pull Requests will be happily accepted.


## Development

If you're adding new functionality or fixing a bug in existing functionality
please include a unit test that exercises the new/changed code.

## Contributing

1. Fork it ( https://github.com/masukomi/oho/fork )
2. Create your feature branch (git checkout -b my_new_feature)
3. Make some changes
4. Confirm all the old and new unit tests still pass (crystal spec)
5. Commit your changes (git commit -am 'Add some feature')
6. Push to the branch (git push origin my_new_feature)
7. Create a new Pull Request

## Questions?

I'm happy to help, just reach out to me on the Fediverse (Mastodon & friends) at [@masukomi@connectified.com](https://connectified.com/masukomi)

## Contributors

- [masukomi](https://github.com/masukomi) masukomi - creator, maintainer
- You!
