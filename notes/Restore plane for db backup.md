### Prerequisites:

1. Access to server machine 
2. The **SQL dump file** (e.g., `backup_*.sql`) you want to restore from. 
### Step-by-Step Guide:
1. **Push backup file into restore directory**
```bash
# Copy file to directory. PROJECT_DIR is root dir of project
cp backup_*.sql PROJECT_DIR/backup/restore-backup/ 

# restart backup container for apply restore 
# backup-dev is name of backup container
cd PROJECT_DIR
docker restart backup-dev 
```

Note: *If file was deleted after container restarting, its mean at restore was successfull*


[[work]]
[[vmware]]