# MySQL Restoration
Look at Grafana MySQL dashboard and check what host is Read/Write

SSH to the primary MySQL Server and run as user backup:
`sudo su backup`

Restore backups from backup server:
`duplicity --no-encryption full /home/backup/mysql/ rsync://<username>@/mysql`

Verify the file and run as user root:
`ls -l /home/backup/restore/mysql`
`sudo su`

Drop database and restore contents of agama database and verify mysql status: 
`mysql -e 'DROP DATABASE IF EXISTS agama; CREATE DATABASE agama;'`
`rm -rf /home/backup/restore/mysql/*`
`sudo -u backup duplicity --no-encryption restore rsync://<username>@backup/mysql /home/backup/restore/mysql`
`mysql agama < /home/backup/restore/mysql/agama.sql`
`systemctl status mysql` should see active(running)

Verify contents:
- Check AGAMA Webpage
- `mysql -e "SELECT * FROM agama.item"`

# InfluxDB Restoration
SSH into vm3 and verify influxdb is active(running):
`sudo systemctl status influxdb`

Switch to user backup:
`sudo su backup`

Restore backups from backup server:
`duplicity --no-encryption restore rsync://<username>@backup/influxdb /home/backup/restore/influxdb`

Verify the file and run as user root:
`ls -l /home/backup/restore/influxdb`
`sudo su`

Remove the current telegraf database and restore the database with the backup data
`systemctl status telegraf stop`
`influx -execute 'DROP DATABASE telegraf`
`influxd restore -portable -database telegraf /home/backup/restore/influxdb`

Verify contents:
`influx`
`show databases`
`use telegraf`
`show measurements`

Don't forget to start service telegraf and check the Syslog dashboard:
`systemctl start telegraf`
`Grafana > Dashboard > syslog`