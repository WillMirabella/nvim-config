# Neovim Setup (Ubuntu Server)

How to set up this config on a fresh Ubuntu machine (e.g. a homelab server accessed over SSH).
LazyVim is bootstrapped by this config through `lazy.nvim`, so it installs itself on first launch.

## 1. Install dependencies

```bash
sudo apt update
sudo apt install -y git curl unzip build-essential ripgrep fd-find

# Ubuntu names the fd binary "fdfind"; LazyVim expects "fd"
mkdir -p ~/.local/bin && ln -s "$(which fdfind)" ~/.local/bin/fd
```

Optional:

- `nodejs npm` if any Mason-installed language servers need Node (e.g. TypeScript, Pyright)
- `lazygit` for LazyVim's git UI

## 2. Install Neovim (official release, not apt)

Ubuntu's apt package is usually too old for current LazyVim, which needs Neovim 0.11+.

```bash
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.tar.gz
sudo rm -rf /opt/nvim-linux-x86_64
sudo tar -C /opt -xzf nvim-linux-x86_64.tar.gz
echo 'export PATH="/opt/nvim-linux-x86_64/bin:$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
nvim --version   # confirm 0.11+
```

- On ARM (`uname -m` shows `aarch64`), use `nvim-linux-arm64.tar.gz` instead (and the matching `/opt` path).
- To update Neovim later, rerun the same commands.

## 3. Clone this config

Back up any existing config first:

```bash
[ -d ~/.config/nvim ] && mv ~/.config/nvim ~/.config/nvim.bak
```

HTTPS (read-only, works for public repos):

```bash
git clone https://github.com/WillMirabella/nvim-config.git ~/.config/nvim
```

SSH (needed for private repos, and for pushing changes):

```bash
ssh-keygen -t ed25519            # then add ~/.ssh/id_ed25519.pub to GitHub → Settings → SSH keys
git clone https://github.com/WillMirabella/nvim-config.git ~/.config/nvim
```

## 4. First launch

```bash
nvim
```

lazy.nvim installs LazyVim and all plugins, and Mason installs language servers.
Let it finish, quit, reopen, then run:

```vim
:checkhealth
```

Fix anything it flags (missing compiler, `fd`, language servers, etc.).

## Notes for using over SSH

- **Nerd Font goes on the client, not the server.** Icons are rendered by the terminal you SSH *from*,
  so install a Nerd Font there (e.g. JetBrainsMono Nerd Font) and set your terminal to use it.
- **Clipboard:** the server has no clipboard. Recent Neovim supports OSC 52, which sends yanks back
  through SSH to the client's clipboard (Windows Terminal, WezTerm, kitty, etc. support it).
  If yanks don't reach your desktop, add this to `lua/config/options.lua`:

  ```lua
  vim.g.clipboard = 'osc52'
  ```
