# Buildable Moodle application stack for Kubernetes on Wodby

Deploy Moodle built from your Git repository on Kubernetes with Wodby: your
repository pins Moodle's version and holds your own plugins and themes.

This repository defines the Wodby stack manifests and default service
composition for buildable Moodle.

<!-- wodby:generated:start -->

## Stack contract

- [Browse Wodby application stacks](https://wodby.com/stacks)
- [Wodby stack documentation](https://wodby.com/docs/2.0/stacks/)
- [Stack manifest reference](https://wodby.com/docs/2.0/stacks/template/)

## Service definitions

- [Buildable Moodle service](https://github.com/wodby/service-moodle-buildable)
- [Nginx for buildable Moodle service](https://github.com/wodby/service-moodle-buildable-nginx)
- [MariaDB service](https://github.com/wodby/service-mariadb)
- [NFS provisioner service](https://github.com/wodby/service-nfs-provisioner)
- [OpenSMTPD service](https://github.com/wodby/service-opensmtpd)
- [Valkey service](https://github.com/wodby/service-valkey)

## What's included

| Component / service | Default configuration |
| --- | --- |
| Moodle<br>`moodle` | required; enabled by default; derivatives: `cron`; volumes: `moodledata` 20 GB; links: `db` → `mariadb`, `moodledata` → `moodledata-nfs`, `sendmail` → `opensmtpd`, `redis` → `valkey` |
| Nginx<br>`nginx` | required; enabled by default; links: `backend` → `moodle` |
| MariaDB<br>`mariadb` | optional; enabled by default; versions: `12.3`, `11.8` (default), `11.4`; volumes: `data` 10 GB |
| Moodle data NFS storage<br>`moodledata-nfs` | optional; enabled by default; volumes: `data` 25 GB |
| OpenSMTPD<br>`opensmtpd` | optional; enabled by default |
| Valkey<br>`valkey` | optional; disabled by default |

Enabled optional services are selected by default but can be excluded when an
app is created. Disabled optional services are available but not selected by
default. Required services cannot be excluded.

## Validate the stack manifest

```bash
wodby stack validate-manifest stack.yml --org <org-id>
```

<!-- wodby:generated:end -->

## Deploy this stack

Connect a Git repository started from the
[Moodle boilerplate](https://github.com/wodby/moodle-boilerplate):

- `.moodle-version` names the Moodle release to run, such as `5.3.1`.
- `public/` holds your plugins and themes at their place in Moodle's tree, such
  as `public/local/myplugin`.

The pipeline puts the release and your plugins together, then builds the Moodle
and Nginx images from the same code. Upgrading Moodle means changing
`.moodle-version` and deploying; the deployment upgrades the database and
installs or upgrades your plugins. You decide when to upgrade, so watch
Moodle's releases for security fixes. To have Wodby keep Moodle up to date
instead, use the [Moodle stack](https://github.com/wodby/stack-moodle).

Enter the administrator's password, and optionally a username and email, in the
Moodle service's settings when you create the app. The first deployment
installs Moodle with that account; Moodle's web installer is not used. A
password that doesn't meet Moodle's password policy must be changed at the
first sign-in.

Moodle's cron runs in the `cron` worker. Uploaded files are kept on the shared
`moodledata` volume. Enable Valkey to keep sessions out of the data volume.

## Maintain a custom version

1. Fork this repository.
2. Edit the stack manifest.
3. Import the repository as a [Git-backed stack](https://wodby.com/docs/2.0/stacks/create/#create-a-git-backed-stack).

When replacing or renaming a stack service, update every related link target
and derivative reference. Stack-local names and referenced service names are
distinct identifiers.
