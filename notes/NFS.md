#### `/etc/exports`
```ini
# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#

# dev folder
/nfs/dev 10.0.0.0/16(rw,sync,all_squash,insecure,root_squash,no_subtree_check,anonuid=1001)

# docs folder
/nfs/docs 10.0.0.0/16(rw,sync,all_squash,insecure,root_squash,no_subtree_check,anonuid=1001)

# media folder
/nfs/media 10.0.0.0/16(rw,sync,all_squash,insecure,root_squash,no_subtree_check,anonuid=1001)
```

#### Packages
server: `nfs-kernel-server`
client: `nfs-common`

#### Update config file
`sudo exportfs -a`

#### Params
- `/nfs/media`: Shared directory.
- `10.0.0.0/16`: Allowed IP range.
- `rw`: Read-write permissions.
- `sync`: Write changes to disk before responding.
- `all_squash`: Map all client requests to anonymous user.
- `insecure`: Allow connections from unprivileged ports.
- `root_squash`: Map root user requests to anonymous user.
- `no_subtree_check`: Disable subtree checking.
- `anonuid=1001`: Anonymous user UID.


[[linux]]
