### Install script
```bash
curl https://pyenv.run | bash
```

### Config in `.bashrc`
```bash
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

### Extra libs for python compiling
```bash
sudo apt-get install build-essential zlib1g-dev libffi-dev libssl-dev libbz2-dev libreadline-dev libsqlite3-dev liblzma-dev libncurses-dev tk-dev 
```

### Install pyenv
```bash
pyenv install 3.12.4
```

### Activate
```bash
pyenv shell 3.12.4
```

[[python]]