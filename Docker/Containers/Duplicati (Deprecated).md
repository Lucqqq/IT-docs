# Duplicati

Dupliacti is a [[Docker]]-Container that backups configured directories. It allows for managing multiple version, encryption and automation. 

Duplicati is set up using the following [[Docker-Compose]]-file:
```yml
version: "2.1"
services:
  duplicati:
    image: lscr.io/linuxserver/duplicati:latest
    container_name: duplicati
    environment:
      - PUID=0
      - PGID=0
      - TZ=Europe/Berlin
      - CLI_ARGS= #optional
    volumes:
      - /path/to/config:/config
      - /path/to/backups:/backups
      - /path/to/backups2:/backups2
      - /path/to/source:/source:ro #read only
    #ports:
     # - 8200:8200
    restart: unless-stopped
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.duplicati.entrypoints=http"
      - "traefik.http.routers.duplicati.rule=Host(`duplicati.domain.com`)"
      - "traefik.http.middlewares.duplicati-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.duplicati.middlewares=duplicati-https-redirect"
      - "traefik.http.routers.duplicati-secure.entrypoints=https"
      - "traefik.http.routers.duplicati-secure.rule=Host(`duplicati.domain.com`)"
      - "traefik.http.routers.duplicati-secure.tls=true"
      - "traefik.http.routers.duplicati-secure.service=duplicati"
      - "traefik.http.services.duplicati.loadbalancer.server.port=8200"
      - "traefik.docker.network=proxy"
      - 'traefik.http.routers.duplicati-secure.middlewares=authelia@docker'

networks:
  proxy:
    external: true
```

These are the currently configured backups:
## Docker
```json
{
  "CreatedByVersion": "2.0.6.3",
  "Schedule": {
    "ID": 1,
    "Tags": [
      "ID=1"
    ],
    "Time": "2022-12-02T23:01:00Z",
    "Repeat": "1D",
    "LastRun": "2022-12-01T23:01:00Z",
    "Rule": "AllowedWeekDays=Monday,Tuesday,Wednesday,Thursday,Friday,Saturday,Sunday",
    "AllowedDays": [
      "mon",
      "tue",
      "wed",
      "thu",
      "fri",
      "sat",
      "sun"
    ]
  },
  "Backup": {
    "ID": "1",
    "Name": "Docker",
    "Description": "",
    "Tags": [],
    "TargetURL": "file:///backups/",
    "DBPath": "/config/DDVZFQWDXH.sqlite",
    "Sources": [
      "/source/docker/"
    ],
    "Settings": [
      {
        "Filter": "",
        "Name": "encryption-module",
        "Value": "aes",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "compression-module",
        "Value": "zip",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "dblock-size",
        "Value": "1GB",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "retention-policy",
        "Value": "1W:1D,4W:1W,12M:1M",
        "Argument": null
      }
    ],
    "Filters": [],
    "Metadata": {
      "LastErrorDate": "20221202T103421Z",
      "LastErrorMessage": "Detected files associated with non-existing blocksets!",
      "LastBackupDate": "20221125T230100Z",
      "BackupListCount": "7",
      "TotalQuotaSpace": "490974396416",
      "FreeQuotaSpace": "278117969920",
      "AssignedQuotaSpace": "-1",
      "TargetFilesSize": "14575136647",
      "TargetFilesCount": "51",
      "TargetSizeString": "13,57 GB",
      "SourceFilesSize": "24053349764",
      "SourceFilesCount": "59484",
      "SourceSizeString": "22,40 GB",
      "LastBackupStarted": "20221125T230100Z",
      "LastBackupFinished": "20221125T230355Z",
      "LastBackupDuration": "00:02:55.1603460",
      "LastCompactDuration": "00:00:01.4447760",
      "LastCompactStarted": "20221125T230351Z",
      "LastCompactFinished": "20221125T230353Z"
    },
    "IsTemporary": false
  },
  "DisplayNames": {
    "/source/docker/": "docker"
  }
}
```

## Nextcloud
```json
{
  "CreatedByVersion": "2.0.6.3",
  "Schedule": {
    "ID": 4,
    "Tags": [
      "ID=6"
    ],
    "Time": "2022-12-01T04:05:00Z",
    "Repeat": "1W",
    "LastRun": "0001-01-01T00:00:00Z",
    "Rule": "AllowedWeekDays=Tuesday",
    "AllowedDays": [
      "tue"
    ]
  },
  "Backup": {
    "ID": "6",
    "Name": "Nextcloud",
    "Description": "",
    "Tags": [],
    "TargetURL": "file:///backups2/",
    "DBPath": "/config/TPITXYIQQV.sqlite",
    "Sources": [
      "/source/nextcloud/"
    ],
    "Settings": [
      {
        "Filter": "",
        "Name": "encryption-module",
        "Value": "aes",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "compression-module",
        "Value": "zip",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "dblock-size",
        "Value": "1GB",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "keep-versions",
        "Value": "3",
        "Argument": null
      }
    ],
    "Filters": [],
    "Metadata": {
      "LastBackupDate": "20221201T140359Z",
      "BackupListCount": "1",
      "TotalQuotaSpace": "3936687546368",
      "FreeQuotaSpace": "2548118908928",
      "AssignedQuotaSpace": "-1",
      "TargetFilesSize": "173310614113",
      "TargetFilesCount": "325",
      "TargetSizeString": "161,41 GB",
      "SourceFilesSize": "188267129194",
      "SourceFilesCount": "81639",
      "SourceSizeString": "175,34 GB",
      "LastBackupStarted": "20221201T140356Z",
      "LastBackupFinished": "20221201T200723Z",
      "LastBackupDuration": "06:03:27.1254380"
    },
    "IsTemporary": false
  },
  "DisplayNames": {
    "/source/nextcloud/": "nextcloud"
  }
}
```

## Nextcloud-External
```json
{
  "CreatedByVersion": "2.0.6.3",
  "Schedule": {
    "ID": 3,
    "Tags": [
      "ID=5"
    ],
    "Time": "2022-12-08T04:05:00Z",
    "Repeat": "1W",
    "LastRun": "2022-12-01T04:05:00Z",
    "Rule": "AllowedWeekDays=Thursday",
    "AllowedDays": [
      "thu"
    ]
  },
  "Backup": {
    "ID": "5",
    "Name": "Nextcloud_external",
    "Description": "",
    "Tags": [],
    "TargetURL": "file:///backups/nextcloud/",
    "DBPath": "/config/MPKMVDTPGP.sqlite",
    "Sources": [
      "/source/nextcloud/"
    ],
    "Settings": [
      {
        "Filter": "",
        "Name": "encryption-module",
        "Value": "aes",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "compression-module",
        "Value": "zip",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "dblock-size",
        "Value": "1GB",
        "Argument": null
      },
      {
        "Filter": "",
        "Name": "keep-versions",
        "Value": "3",
        "Argument": null
      }
    ],
    "Filters": [],
    "Metadata": {
      "LastBackupDate": "20221201T040501Z",
      "BackupListCount": "1",
      "TotalQuotaSpace": "490974396416",
      "FreeQuotaSpace": "89558073344",
      "AssignedQuotaSpace": "-1",
      "TargetFilesSize": "173328914731",
      "TargetFilesCount": "327",
      "TargetSizeString": "161,43 GB",
      "SourceFilesSize": "188255192279",
      "SourceFilesCount": "81565",
      "SourceSizeString": "175,33 GB",
      "LastBackupStarted": "20221201T040500Z",
      "LastBackupFinished": "20221201T051303Z",
      "LastBackupDuration": "01:08:03.7633650",
      "LastRestoreDuration": "07:19:45.4445720",
      "LastRestoreStarted": "20221128T080454Z",
      "LastRestoreFinished": "20221128T152440Z",
      "LastCompactDuration": "00:00:04.0717780",
      "LastCompactStarted": "20221201T051248Z",
      "LastCompactFinished": "20221201T051252Z"
    },
    "IsTemporary": false
  },
  "DisplayNames": {
    "/source/nextcloud/": "nextcloud"
  }
}
```
