```shell
CURRENT_DATE=$(date +%d-%m-%Y_%H-%M-%S)
FILE_NAME="$CURRENT_DATE"_app.tgz
CURRENT_PATH=$(pwd)
CURRENT_FORLDER=$(basename $CURRENT_PATH)

cd ..
tar --exclude env --exclude .env --exclude .git -czf $FILE_NAME $CURRENT_FORLDER
mv $FILE_NAME $CURRENT_PATH
```

[[bash]]
[[linux]]