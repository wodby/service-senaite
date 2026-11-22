# SENAITE LIMS on Wodby

What Wodby sets up for SENAITE on this service. SENAITE runs from the `wodby/senaite` image: SENAITE 2.6 on Plone 5.2 and Python 2.7, with SENAITE's security hotfix for CVE-2026-54569 (`SenaiteHotfix20260602`) included. Each replica is a client of the SENAITE database service (`senaite-zeo`), which holds the database and the uploaded files, so the service can run more than one replica. It runs as a non-root user with a read-only root file system.

SENAITE 2.x needs Python 2.7, which no longer gets fixes from the Python project. The image is built on Ubuntu 22.04, whose Python 2.7 package still gets security updates; vulnerability scans still report findings in it and in Plone's Python 2 packages.

## Linked services

| Link | Variables |
| --- | --- |
| SENAITE database (`zeo`, required) | `ZEO_ADDRESS` |
| SMTP server (`sendmail`, optional) | `SMTP_HOST`, `SMTP_PORT`, `SENAITE_EMAIL_FROM` |

SENAITE keeps its mail settings in the site (**Site setup > Mail**). With the SMTP link, each deployment points them at the linked server while the site still has Plone's default (`localhost`) or the server it linked before, and sets the sender address to `noreply@` the service's domain when none is set. A server entered in the mail settings is kept.

## Site and administrator

SENAITE is served at the root of the service's domain. The image rewrites each request to the site at `/senaite`, with the scheme and host Wodby's router received, so links use the public HTTPS address. Zope's root and its management interface are not reachable through the domain.

The **Create or upgrade the site** action runs after every deployment. On the first one, the database is empty: it creates the site with the Language and Time zone settings and an administrator from the Admin username, Admin password and Admin email settings. The password needs at least 12 characters; a shorter one stops the deployment before anything is created. Changing these settings later doesn't change the site or the account; manage users in SENAITE. The sign-in cookie is sent only over HTTPS.

## Upgrades

A newer image runs Plone's migration and the upgrade steps of SENAITE's add-ons in the same action. Back up the SENAITE database service first; moving back to an older version is not supported.

## Database maintenance

The **Pack the database** cron job runs every Sunday at 03:30 and removes object revisions older than a week, and files nothing uses. Undo works only within that week. Run `senaite-pack DAYS` in a SENAITE container to pack by hand.

## Check the result

From inside the environment, `/.healthz` on port 8080 answers when Zope runs, even before the site exists. The service is probed with it.
