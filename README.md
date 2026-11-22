# SENAITE LIMS service for Kubernetes on Wodby

Run SENAITE LIMS as a reusable Kubernetes application service with Wodby.

This repository defines the Wodby service manifests and operational
configuration for SENAITE LIMS.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Wodby stacks using this service

- [SENAITE LIMS application stack](https://github.com/wodby/stack-senaite)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `senaite` |
| Type | Application service |
| Versions | `2.6` by default |
| Workloads | `main` (Deployment, primary) |
| Containers | `senaite` using `wodby/senaite` |
| Endpoints | `senaite`: HTTP 8080 (main) |
| Service links | SENAITE database (ZEO) (`zeo`), required; SMTP server (`sendmail`), optional |
| Application build | Not buildable from application source |
| Helm | chart `oci://registry-1.docker.io/wodby/stateless`; version `0.4.0` |
| Configuration and operations | 5 settings, 1 actions, 1 cron schedules |

## Use this service

Use this service through [SENAITE LIMS application stack](https://github.com/wodby/stack-senaite), or reference `senaite` from a
custom Wodby stack.

A service is a reusable component and does not deploy by itself. The stack
defines its links, settings, versions, resources, and relationship to the rest
of the application.

## Python 2.7

SENAITE 2.x runs on Plone 5.2 and Python 2.7, which no longer gets fixes from
the Python project. The `wodby/senaite` image uses Ubuntu 22.04's Python 2.7
package, which still gets security updates, and includes SENAITE's hotfix for
CVE-2026-54569. Vulnerability scans still report findings in Python 2 packages.

## First administrator

The first deployment creates the site with an administrator from the
required Admin password setting and the Admin username and Admin email
settings, so nobody else can set the site up. The password needs at least 12
characters. Changing the settings later doesn't change the account.

## Upgrades

A newer SENAITE applies its upgrade steps after the deployment. Back up the
SENAITE database service first; moving back to an older version is not
supported.

## Maintain a custom version

1. Fork this repository.
2. Edit the service manifest and referenced files.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, volume, config, and
derivative names stable unless dependent stacks and app-level overrides are
updated at the same time.

Validate the manifests with:

```bash
wodby service validate-manifest service.yml --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/) and the [managed services index](https://github.com/wodby/services).
