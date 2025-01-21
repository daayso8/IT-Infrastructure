# Backup SLA
## Coverage

We back up services that satisfy at least one of these criteria:

    are primary source of truth for particular data
    contain customer and/or client data
    are not feasible (or very costly) to restore by other means

Services that are backed up:

MySQL
InfluxDB

## Schedule

MySQL backups are created every daily at 22:55 UTC; it takes up to 5 minutes to create and store the backup.

InfluxDB backups are created daily at 22:15; it takes up to 5 minutes to create and store the backup.


All backups are started automatically by Cron Jobs.

Backup RPO (recovery point objective) is:

    24 hours for MySQL database
    24 hours for InfluxDB database


## Storage

MySQL and InfluxDB backups are uploaded to the backup server at `rsync://<username>@backup/<backup>`.


Backup data from both servers will be synchronized to encrypted AWS S3 bucket in future (work in progress).

## Retention

MySQL backups are stored for 30 days; 30 versions (recovery points) are available to restore.

InfluxDB backups are stored for 30; 30 versions are available to restore.

Usability checks

MySQL backups are verified every weekly by manually restoring and checking integrity.

InfluxDB backups are verified every weekly by manually restoring and checking integrity.

##Restore process

Service is recovered from the backup in case of an incident, and when service cannot be restored in any other way.

`mysql -u <username> -p agama < /home/backup/mysql/agama.sql`
`influxd restore -portable -db telegraf /home/backup/influxdb`

RTO (recovery time objective) is:

    1 hour for MySQL and InfluxDB
