# docker-rclone
# Creds to bcardiff for the work for this docker container (this is a forked version)
# reason for forking is the need for an updated version of RClone which fixes bugs, and all other forks run much older rclone

Docker image to perform a [rclone](http://rclone.org) copy based on a cron schedule, with [healthchecks.io](https://healthchecks.io) monitoring.

rclone is a command line program to copy files and directories to and from:

* Google Drive
* Amazon S3
* Openstack Swift / Rackspace cloud files / Memset Memstore
* Dropbox
* Google Cloud Storage
* Amazon Drive
* Microsoft OneDrive
* Hubic
* Backblaze B2
* Yandex Disk
* SFTP
* FTP
* HTTP
* The local filesystem


## Usage

### Configure rclone

rclone needs a configuration file where credentials to access different storage
provider are kept.

By default, this image uses a file `/config/rclone.conf` and a mounted volume may be used to keep that information persisted.

A first run of the container can help in the creation of the file, but feel free to manually create one.

```
$ mkdir config
$ docker run --rm -it -v $(pwd)/config:/config robostlund/rclone-copy
```

### Perform copy in a daily basis

A few environment variables allow you to customize the behavior of the copy:

* `COPY_SRC` source location for `rclone copy` command
* `COPY_DEST` destination location for `rclone copy` command
* `CRON` crontab schedule `0 0 * * *` to perform copy every midnight
* `CRON_ABORT` crontab schedule `0 6 * * *` to abort copy at 6am
* `FORCE_COPY` set variable to perform a copy upon boot
* `CHECK_URL` [healthchecks.io](https://healthchecks.io) url or similar cron monitoring to perform a `GET` after a successful copy
* `COPY_OPTS` additional options for `rclone copy` command. Defaults to `-v`
* `TZ` set the [timezone](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) to use for the cron and log `America/Argentina/Buenos_Aires`

```bash
$ docker run --rm -it -v $(pwd)/config:/config -v /path/to/source:/source -e COPY_SRC="/source" -e COPY_DEST="dest:path" -e TZ="America/Argentina/Buenos_Aires" -e CRON="0 0 * * *" -e CRON_ABORT="0 6 * * *" -e FORCE_COPY=1 -e CHECK_URL=https://hchk.io/hchk_uuid robinostlund/rclone-copy
```

See [rclone copy docs](https://rclone.org/commands/rclone_copy/) for source/dest syntax and additional options.
