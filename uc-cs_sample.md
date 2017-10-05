### 2.9 UC CS\_08: Restore Configuration from a Backup File

**System**: Central server

**Level**: User task

**Component:** Central server

**Actor**: CS administrator

**Brief Description**: CS administrator restores the central server
configuration from a backup file.

**Preconditions**: A backup file is saved in the system configuration.

**Postconditions**: -

**Trigger**: CS administrator wants to restore the central server
configuration to a previously backed up state.

**Main Success Scenario**:

1.  CS administrator selects to restore the central server configuration
    from a backup file saved in the system configuration.

2.  System prompts for confirmation.

3.  CS administrator confirms.

4.  System runs the restore script that

    a.  verifies that the file is a valid backup file;

    b.  verifies the label of the backup file:

    -   verifies that the server type in the label corresponds to
        the type of the server that is being restored;

        *Note: System verifies only the server type and ignores the rest of the information in the label in case the restore script is called from the CLI with the -F option.*
    
    -   verifies that the server software version in the label is compatible
        with the installed software version of the server that is being
        restored;

    -   verifies that the instance identifier in the label corresponds to
        the instance identifier of the central server that is being
        restored;

    -   verifies that the node name in the label corresponds to the node
        name of the central server that is being restored if the restored
        server is a part of a high availability cluster.
            
    c.  Clears the shared memory;
    
    d.  stops all system services, except for xroad-jetty;
    
    e.  creates a pre-restore backup of the system configuration (step 2 of 2.8) to /var/lib/xroad/conf\_prerestore\_backup.tar (the pre-restore backup file is overwritten on each restore);
    
    f.  deletes the content of the following directories:

    -   /etc/xroad/

    -   /etc/nginx/sites-enabled/
    
    g.  restores the contents of the directories
    
    - /etc/xroad/

    - /etc/nginx/sites-enabled/
    
    from the backup file;
        
    h.  writes the database dump from the backup file to /var/lib/xroad/dbdump.dat;
    
    i.  restores the database data from the dump file /var/lib/xroad/dbdump.dat.
    
    j.  starts the system services that were previously stopped.

5.  System displays the message “Configuration restored successfully
    from file 'X'.” (where X is the file name of the backup file) and
    the output of the restore script.

6.  System logs the event “Restore configuration” to the audit log.

**Extensions**:

- 3a. CS administrator cancels the restoring of the configuration from the backup file.
    - 3a.1. Use case terminates.

- 4a. Restoring the central server configuration failed.
    - 4a.1. System displays the error message “Restoring configuration from file 'X' failed.” (where “X” is the file name of the backup file) and the output of the restore script.
    - 4a.2. System logs the event “Restore configuration failed” to the audit log.
    - 4a.3. Use case terminates.

**Related information**:

-   The backup files are located at /var/lib/xroad/backup.

-   The audit log is located at /var/log/xroad/audit.log. The data set
    of audit log records is described in the document “X-Road: Audit Log
    Events” \[[SPEC-AL](#Ref_SPEC-AL)\].
