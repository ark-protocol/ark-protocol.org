# ark-protocol.org

This is the source code for the [ark-protocol.org](https://ark-protocol.org), a community-driven resource covering all things related to the Ark protocol.

Ark is a layer 2 protocol for making off-chain bitcoin transactions, offering a payments system where users can make bitcoin transactions at very low cost without requiring extensive setup like opening channels.

This site aims to be a neutral source of information about the Ark protocol, its various implementations, and how it compares to other bitcoin layer 2 solutions. While the primary maintainer is from [Second](https://second.tech), we welcome contributions from the entire bitcoin community!

## Local development

The website is built using [Hugo](https://gohugo.io/) static site generator with the Relearn theme.

### Prerequisites

You need to install Hugo, which is the static site generator that powers this website.

### Installing Hugo

**On Linux:**
- Ubuntu/Debian: `sudo apt install hugo`
- CentOS/RHEL: `sudo yum install hugo` (or `sudo dnf install hugo` on newer versions)
- Fedora: `sudo dnf install hugo`
- Arch: `sudo pacman -S hugo`
- Or using Snap: `sudo snap install hugo`

**On macOS:**
- Using Homebrew (recommended): `brew install hugo`
- If you don't have Homebrew, install it first:
  ```bash
  /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
  ```

Verify Hugo is installed: `hugo version`

### Setting up the Hugo theme

After cloning the repository, you need to initialize the Hugo theme submodule:

```bash
cd /path/to/ark-protocol.org
git submodule init
git submodule update
```

If you encounter theme compatibility errors, ensure you're using a recent version of the theme:
```bash
cd themes/hugo-theme-relearn
git checkout 8.0.1  # or latest stable version
```

> [!TIP]
> When initially cloning the repository, you can use the `--recursive` flag to automatically set up submodules:
> ```bash
> git clone --recursive https://github.com/ark-protocol/ark-protocol.org.git
> ```

### Running the development server

```bash
cd /path/to/ark-protocol.org
hugo server
```

The website will be available at `http://localhost:1313`. Hugo will automatically rebuild and reload the browser when you make changes to any files.

## Making changes

- Content files are in the `content/` folder
- Each `.md` file corresponds to a page on the website  
- Hugo automatically rebuilds and refreshes the browser when you save changes

### Working with feature branches

Make sure you're on your feature branch:
```bash
git branch                              # Check current branch
git checkout your-feature-branch-name   # Switch if needed
```

Make your changes, preview at `http://localhost:1313`, then commit and push when ready.

Press `Ctrl+C` (or `Cmd+C` on Mac) in the terminal to stop the Hugo server.

## Troubleshooting

**Common issues:**

- **"hugo: command not found"** - Hugo isn't installed or isn't in your PATH
- **"Port 1313 already in use"** - Run `hugo server --port 3000` instead
- **"Theme not found" or "Page Not Found"** - The Hugo theme submodule likely isn't initialized:
  ```bash
  git submodule init
  git submodule update
  ```
- **Template errors or deprecated warnings** - Update the theme to a compatible version:
  ```bash
  cd themes/hugo-theme-relearn
  git fetch
  git checkout 8.0.1  # or latest stable version
  ```
- **Changes not appearing** - Check you saved the file and look for terminal errors

**Linux-specific issues:**
- **Package manager Hugo version too old** - Some distributions have outdated Hugo packages. Download directly from [Hugo releases](https://github.com/gohugoio/hugo/releases) if needed
- **Permission denied on `/usr/local/bin`** - Use `sudo` when moving the Hugo binary: `sudo mv hugo /usr/local/bin/`

**Mac-specific issues:**
- **Homebrew permission errors** - Try `sudo chown -R $(whoami) $(brew --prefix)/*`
- **macOS blocking Hugo binary** - Allow in System Preferences → Security & Privacy  
- **Missing Xcode tools** - Run `xcode-select --install` before installing Homebrew
- **Apple Silicon compatibility** - Homebrew handles this automatically; manual downloads need universal binaries
- **PATH issues** - Add `/usr/local/bin` to your PATH in `~/.zshrc`

## Contributing

1. Stop the Hugo server (`Ctrl+C` or `Cmd+C`)
2. Commit your changes: `git add . && git commit -m "Your commit message"`
3. Push to your feature branch: `git push origin your-feature-branch-name`
4. Create a pull request on GitHub

## Project structure

- `content/` - Markdown files for website pages
- `hugo.toml` - Hugo configuration
- `layouts/` - Custom HTML templates
- `static/` - Static assets (images, diagrams)
- `themes/hugo-theme-relearn/` - Hugo theme

## License

This work is dedicated to the public domain under CC0.
