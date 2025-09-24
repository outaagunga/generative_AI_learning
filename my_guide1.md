
Nice — I’ll turn this into a **careful, code-along Module 1** you can run on a Linux box (I’ll assume Ubuntu/Debian as the primary distro and give alternatives). I’ll not skip any steps: every command you need is shown, why we run it is explained, and there’s a verification step so you know it worked. I’ll also give a short preview of the next modules at the end.

---

# Module 1 — Preparing your Linux environment for **Jac** (code-along)

**Goal:** by the end of this module you will have a working Python 3.12+ virtual environment, Jac installed, VS Code and the Jac extension installed, and you’ll run your first `test.jac` Hello World.

> Key facts up front: Jac requires **Python 3.12 or newer** and is installed with `pip` (the docs recommend `python -m pip install -U jaclang`). I’ll also show the optional `jaclang[all]` variant that bundles extras. ([jac-lang.org][1])

---

## 0) Open a terminal and follow along

Everything below is terminal / shell commands unless I say otherwise. Copy-paste each block, run it, and read the small explanation that follows.

---

## 1) Update your system packages

Ubuntu / Debian:

```bash
sudo apt update && sudo apt upgrade -y
```

Fedora:

```bash
sudo dnf upgrade --refresh -y
```

Arch:

```bash
sudo pacman -Syu
```

Why: keeps package manager metadata and system libs current; avoids weird build/tool conflicts.

---

## 2) Install or ensure **Python 3.12+** is available

Jac requires **Python 3.12+**. If your distro already provides `python3` as 3.12+, you can use it. If not, either install the distro package for 3.12 or use `pyenv` (I show both common options).

**Check your current python version**

```bash
python3 --version
# expected: Python 3.12.x  (if <3.12, install 3.12)
```

### Option A — (Ubuntu / Debian) install Python 3.12 via apt (if available)

```bash
# On newer Ubuntu releases:
sudo apt install -y python3 python3-venv python3-distutils python3-pip
```

### Option B — using deadsnakes PPA (older Ubuntu LTS)

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install -y python3.12 python3.12-venv python3.12-distutils
```

### Option C — use pyenv (works on any distro; good for parallel versions)

```bash
# install prerequisites (Ubuntu example)
sudo apt install -y build-essential curl libssl-dev zlib1g-dev \
  libbz2-dev libreadline-dev libsqlite3-dev wget llvm libncurses5-dev libncursesw5-dev \
  xz-utils tk-dev libffi-dev liblzma-dev

# install pyenv
curl https://pyenv.run | bash

# restart shell or source your shell rc file (e.g. ~/.bashrc) then:
pyenv install 3.12.4       # install a 3.12.x release
pyenv global 3.12.4        # make it the default for your shell
python --version
```

Why: Jac explicitly requires Python 3.12+. If you use the wrong Python, installation will fail. (Docs: Jac getting started). ([jac-lang.org][1])

---

## 3) Create and activate a **virtual environment** (important — don’t skip)

Always install Jac inside a venv so you don’t conflict with system packages.

```bash
# create a venv in your home folder named jac-env
python3.12 -m venv ~/jac-env

# activate it (bash/zsh)
source ~/jac-env/bin/activate

# upgrade pip inside the venv
python -m pip install --upgrade pip setuptools wheel
```

You should now see `(jac-env)` at the start of your prompt. From now on, `pip` and `python` refer to the venv.

Why: avoids permission issues and ensures `jac` CLI is available in your project shell.

---

## 4) Install Jac (recommended: the stable package)

Two common patterns:

**Simple / recommended**

```bash
python -m pip install -U jaclang
```

**If you want optional extras (plugins, Jaseci integration, etc.)**

```bash
python -m pip install -U "jaclang[all]"
```

Both are supported; the official quickstart and repos show `pip install jaclang` as the main step, and some Jaseci repositories reference the `[all]` extra for bundled extras. ([GitHub][2])

---

## 5) Verify the `jac` CLI and run Hello World

After installation (and with the venv activated) run:

```bash
# check the tool
jac --version

# create a tiny test file and run it (single line)
echo "with entry { print('Hello world'); }" > test.jac
jac run test.jac
# expected output: Hello world
rm test.jac
```

If you see `Hello world` printed, installation is successful. (This test is directly from the Jac getting started docs.) ([jac-lang.org][1])

---

## 6) Install Git

```bash
sudo apt install -y git        # Debian / Ubuntu
# or
sudo dnf install -y git        # Fedora
# or
sudo pacman -S git             # Arch
git --version
```

Why: you’ll probably clone examples and tutorials.

---

## 7) Install Visual Studio Code (editor)

Two quick options:

**Snap (simple, works on many distros)**

```bash
sudo snap install --classic code
```

**apt (official MS repo) — Ubuntu/Debian**

```bash
# follow VS Code official install steps (or use the .deb). Example:
sudo apt install -y wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /usr/share/keyrings/
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" \
  | sudo tee /etc/apt/sources.list.d/vscode.list
sudo apt update
sudo apt install -y code
```

Open VS Code:

```bash
code .
```

---

## 8) Install the **Jac** extension in VS Code

Open the Extensions view in VS Code and search for **Jac** (published by JaseciLabs). Or install from terminal:

```bash
# from inside your venv or system shell (code CLI)
code --install-extension jaseci-labs.jaclang-extension
```

This extension provides syntax highlighting, completion, formatting and linting (it expects `jac` CLI available in your PATH for full language-server features). ([Visual Studio Marketplace][3])

**If the extension complains it cannot find `jac`**, make sure:

* your venv is activated in the terminal you launched VS Code from, **or**
* the `jac` executable is on your PATH (you can add `~/jac-env/bin` to your PATH in `~/.bashrc` or configure the extension to point to the `jac` binary).

---

## 9) Quick project in VS Code — put it together

1. Create a project folder and open in VS Code:

```bash
mkdir ~/jac-playground && cd ~/jac-playground
code .
```

2. In VS Code create `hello.jac` with this content:

```jac
with entry {
    print("Hello from Jac inside VS Code!");
}
```

3. Back in your terminal (ensure venv activated), run:

```bash
jac run hello.jac
```

You should see the printed message.

---

## 10) Common troubleshooting (don’t skip)

* **`jac: command not found`** — activate your venv `source ~/jac-env/bin/activate`, or run `python -m jac` (not typical) or add `~/jac-env/bin` to PATH.
* **Wrong Python version errors** — ensure `python3.12` is used when creating the venv.
* **Permission / pip compile errors** — ensure you’re in a venv and not using `sudo pip install` for the same environment.
* **VS Code LSP not working** — confirm you installed the Jac extension and you launched Code from a shell where the venv is activated OR set the extension’s `jac.path` setting (if available) to the absolute path of the `jac` binary: `~/jac-env/bin/jac`.

If something fails, run:

```bash
python -m pip show jaclang
which jac
jac --version
```

These show where Jac is installed and whether the `jac` binary is on PATH.

---

## 11) ✅ Verification checklist (copy + run)

Run the following and confirm each prints something sensible:

```bash
python3 --version        # >= 3.12
python -m pip --version
source ~/jac-env/bin/activate
jac --version           # should print jac version
echo "with entry { print('Hello world'); }" > test.jac
jac run test.jac        # prints Hello world
rm test.jac
git --version
code --version
```

---

## 12) What I’ll cover next (module preview)

I’ll continue with step-by-step modules (each with runnable code snippets). suggested next modules:

* **Module 2:** Jac language basics — types, walkers, nodes, and small examples.
* **Module 3:** Running Jac with inline Python and using Jaseci runtime features.
* **Module 4:** Generative AI with Jac — use `byllm` or LLM plugin, connect to a local/remote model and prompt flow.
* **Module 5:** Build a small end-to-end demo (Jac orchestration + simple Streamlit UI with `jac-streamlit`).

---

If you want, I’ll immediately start **Module 2** (Jac basics) in the same code-along style — no pauses. Which would you prefer: go on to Module 2 now, or do you want troubleshooting help for anything from Module 1 first?

[1]: https://jac-lang.org/learn/getting_started/?utm_source=chatgpt.com "Getting started - The Jac Programming Language and Jaseci Stack"
[2]: https://github.com/Jaseci-Labs/jaclang?utm_source=chatgpt.com "jaseci-labs/jaclang: The Jac Programming Language - GitHub"
[3]: https://marketplace.visualstudio.com/items?itemName=jaseci-labs.jaclang-extension&utm_source=chatgpt.com "Jac - Visual Studio Marketplace"
