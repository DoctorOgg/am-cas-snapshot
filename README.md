am-cas-snapshot
=========

This is a plugin script which allows AMANDA to back up a Cassandra Database.

Sources
-------
* AMANDA script API: http://wiki.zmanda.com/index.php/Script_API

Usage
-----
This script will be called by the AMANDA plugin API, snd should not be invoked directly.  The configuration file "amanda.conf" defines the input arguments in the "script-tool" and "dumptype" sections.

For example,

```
define script-tool script-cas-snapshot {
    comment "Execute Cassandra snapshot script"
    plugin "am-cas-snapshot"
    execute-where client
    execute-on pre-dle-backup, post-dle-backup
    property "log" "/var/log/am-cas-snapshot.log"
}

define dumptype dump-cas {
    comp-user-tar
    comment "Cassandra snapshot "
    script "script-cas-snapshot"
    auth "bsdtcp"
}
```

Will execute this command, as seen in the amada sendbackup log file:
```
    /usr/libexec/amanda/application/am-cas-snapshot am-cas-snapshot POST-DLE-BACKUP \
        --execute-where client --config DailyBackup --host example.domain.net \
        --level 0 --port 27018 --log /var/log/am-cas-snapshot.log
```

# SUDO config
```
User_Alias   BACKUP  = amandabackup
Cmnd_Alias   CAS_SNAPSHOT  = /usr/bin/nodetool snapshot
Cmnd_Alias   CAS_CLEAR_SNAPSHOT = /usr/bin/nodetool clearsnapshot
BACKUP     ALL     = (ALL) NOPASSWD: CAS_SNAPSHOT,CAS_CLEAR_SNAPSHOT
```
