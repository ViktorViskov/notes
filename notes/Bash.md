### Aliases `.bash_aliases`
```bash
# apt
alias au='sudo apt update'
alias ai='sudo apt install'

# pyenv
alias mkv='python3 -m venv env'
alias py='python'
alias acv='source env/bin/activate'
alias pipi='pip install -r requirements.txt'
```
### For loop in bash
```shell
max=10
for i in `seq 2 $max`
do
    echo "$i"
done
```

### Check for arguments (if not one)
```bash
if [ $# -ne 1 ]; then
    echo "Usage: ./$0 <arg>"
    exit
fi
```
### Chech for arguments
### File name as creating date
```bash
current_date=$(date +%d-%m-%Y_%H-%M-%S)
file_name="$current_date.txt"

echo "Hello "> $file_name
```
[[aliases]]