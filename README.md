# Immich Distribution

> First an important mention, this is **NOT** an official package of [Immich](https://immich.app/). If you like to have Immich installed directly from the Immich project use the official [Docker images](https://immich.app/docs/install/docker-compose). Do not report bugs to upstream _unless_ you are sure that the same bug is present in the upstream images.

<p align="center">
  <img src="docs/site/static/img/immich-dist-snap-gh.png">
</p>

## About this distribution

This is an [Immich](https://immich.app/) Distribution packaged inside a [snap](https://snapcraft.io/docs) package. I have used the excellent [Nextcloud snap](https://github.com/nextcloud-snap/nextcloud-snap/) as an inspiration for this. The package is inspired of the official Immich images. It will be similar, but not identical with the official Docker-based installation. It will ship the same software, but with limited customability. Extra tools are included, they should be non-intrusive and you can ignore them if you prefer.

Please, see the [documentation](https://immich-distribution.nsg.cc/) for more information.

## Installation
[![immich-distribution](https://snapcraft.io/immich-distribution/badge.svg)](https://snapcraft.io/immich-distribution)

For detailed installation instructions with recommendations and hardware requirements see [the installation documentation](https://immich-distribution.nsg.cc/installation/install/). If you are in a hurry, just run `sudo snap install immich-distribution` to get started.

### Updates

Updates are automatic, this is a core concept of Snap. If you do not like this, stay away! If you prefer an easy to maintain and automatic installation that "just works", carry on reading! **Do not expect me to release updates the same day** as an upstream Immich release. I hope to do so in a **reasonable time**. When Immich releases an update I will build and push an update to the candidate channel (my server uses this channel) and try it out for a few days. I will relese the build to the stable channel after a few days, if everyting works.

### Channels

| Channel | Used for |
| ------- | -------- |
| stable  | The default channel, stable release, **use this** |
| candidate | I beleve this is stable, but it's released for testing for a few days |
| beta | Help me to try out new code |
| edge | Development release, will break from time to time |

![](/docs/channel-flow.png)

Automatic updates from "candidate to candidate", "stable to stable" or "stable to candidate" should always work. Otherwise I consider it a bug. The snap may contain migration logic that makes hard to downgrade, this is not supported.

## Included software

| Software | Note |
| -------- | -------- |
| Immich Server | This is the server component of Immich that provides Immich API |
| Immich Microservices | This service provides background processing of assets for Immich Server |
| Immich Machine Learning | Object detection and image classifier, provides and API for Immich |
| Immich Web | The Immich web pages |
| Immich CLI | A CLI tool for bulk upload to Immich |
| Postgres | Relational database used by Immich |
| Redis | Fast key-value database used by Immich |
| Typesense | Search focused database used by Immich |
| HAProxy | Service that proxies traffic to Immich web and server |
| [uacme](https://github.com/ndilieto/uacme) | A Let's Encrypt ACME client used to checkout TLS certificates |

## CLI commands

### immich-distribution.cli

The below example will import all files in `current/` to Immich. No need to specify `--server`, that's added automatically.

```sh
immich-distribution.cli upload --key "mySecrEtKey" -d ~/snap/immich-distribution/current/
```

Note: The CLI is contained so it can't access files outside `/var/snap/immich-distribution` and `~/snap/immich-distribution/`.

### immich-distribution.psql

To connect to the database with psql, run the following command.

```sh
sudo immich-distribution.psql -d immich
```

To connect with an external tool, fetch the database password with `sudo snap get immich-distribution database-password` and connect normally.

```sh
psql -h 127.0.0.1 -U postgres -d immich
```

### immich-distribution.backup

```
immich-distribution.backup [-d] [-a]

-d	Backup database
-a	Backup assets (images, videos, ...)
```

The recommended way to backup Immich is to do a database backup, and then manually backup the assets directly from `/var/snap/immich-distribution/common/upload`.

### immich-distribution.import

```
immich-distribution.import [-d database-backup] [-a assets-backup]

-d	FILE   Import a database backup
-a	FILE   Import assets (images, videos, ...) from an asset backup
```

Note that this will DESTROY and OVERWRITE your current install!

## Enable HTTP (Let's Encrypt)

1. Point a domain name that you control to the Immich server. I assume it's publically available and port 80 and 443 are open to the world.
2. Set the domain with `sudo snap set immich-distribution acme-domain="immich.example.com"`
3. Provide an email used by Let's Encrypt `sudo snap set immich-distribution acme-email="immich@example.com"`
4. Make a Let's Encrypt Account and issue a certificate by running `sudo immich-distribution.lets-encrypt`

This server needs to be available over port 80 on immich.example.com _before_ step 4 is executed.

## Folder Sync

The sync service is an extension written to connect external synchronization services to Immich, like for example Syncthing or Nextcloud. For more information read [SYNC.md](SYNC.md).

## Contribute

Feel free to contribute changes and open issues. I will happily merge changes that do not change the scope of this project. An easy way to help me are:

* Use the software and report issues
* Write well written issues with a lot of information, respond to follow up messages
* Help other people

## Backup

Upstream Immich has the follow nice message on it's webpage. I concur, this is an new experimental package of an new fast moving software. Backup people! Do not blame me if this eats your data.

> ⚠️ The project is under very active development. Expect bugs and changes. Do not use it as the only way to store your photos and videos!

## License

All files in this repository are released under the MIT license.  Upstream Immich is also licensed under the [MIT](https://github.com/immich-app/immich/blob/main/LICENSE) license.
