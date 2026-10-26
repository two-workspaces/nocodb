# Deploy and Host NocoDB on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/nocodb-production?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=nocodb-production)

[NocoDB](https://nocodb.com/) is an open-source Airtable alternative. It turns a database into a collaborative spreadsheet: tables with grid, kanban, gallery, form and calendar views, linked records, lookups and formulas, comments, automations, and a REST API for every table. This template runs NocoDB 2026.10.0 from the official image on its own Postgres 17, with the super admin account created on first boot.

## About Hosting NocoDB

The stack is two services: NocoDB and Postgres.

- **Upstream's image, unchanged.** NocoDB runs the official `nocodb/nocodb` image at a fixed release (2026.10.0), not `latest`. There is no wrapper: everything is configured with environment variables. Upgrading means changing the image tag.
- **Super admin ready on first boot.** NocoDB creates the super admin from `NC_ADMIN_EMAIL` and the generated `NC_ADMIN_PASSWORD`, so the sign-up page never hands the instance to whoever opens it first.
- **Postgres 17 for metadata and data.** NocoDB keeps its own metadata (bases, views, users) in Postgres, and new bases you create store their tables there too.
- **Attachments on a volume.** Files you upload to attachment fields are kept in NocoDB's data directory on its own volume, so they survive redeploys.
- **Lean by default.** NocoDB's JavaScript heap is capped at 512 MB, which keeps a fresh instance near 0.5 GB of RAM.
- **Healthchecked.** Railway's healthcheck calls `/api/v1/health`, which answers once NocoDB has started and connected to Postgres.

## Common Use Cases

- A team database for CRM, inventory, content calendars or hiring pipelines, without per-seat fees
- Forms that collect data straight into a table, and kanban or calendar views for the team working on it
- A spreadsheet interface and REST API over an existing Postgres or MySQL database
- Internal tools and lightweight back offices that non-developers can edit

## Dependencies for NocoDB Hosting

- Postgres 17 (included, private network only)
- A volume for attachments (included)

### Deployment Dependencies

- [NocoDB documentation](https://nocodb.com/docs)
- [NocoDB environment variables](https://nocodb.com/docs/self-hosting/environment-variables)
- [NocoDB on GitHub](https://github.com/nocodb/nocodb)
- [Template source on GitHub](https://github.com/two-workspaces/nocodb)

### Implementation Details

**Sign in.** Enter your email in `NC_ADMIN_EMAIL` when you deploy. When the deploy is green, open your NocoDB domain and sign in with that email and the password in the NocoDB service's `NC_ADMIN_PASSWORD` variable.

**Changing the admin password or email.** NocoDB applies `NC_ADMIN_EMAIL` and `NC_ADMIN_PASSWORD` on every boot, so a password changed in the app is reset on the next deploy. Change the variables instead and redeploy.

**Who can sign up.** Anyone can open the sign-up page, but a new account only gets the org-level viewer role and sees no bases until you invite it. To close sign-ups, go to Account settings, Settings and turn on invite-only sign-up.

**Email.** Invites and password resets need an email provider. Railway allows outbound SMTP only on the Pro plan; set `NC_SMTP_FROM`, `NC_SMTP_HOST`, `NC_SMTP_PORT`, `NC_SMTP_USERNAME`, `NC_SMTP_PASSWORD` and `NC_SMTP_SECURE` on the NocoDB service. Without email, invited users get a link you copy and send yourself.

**Custom domains.** Add the domain to the NocoDB service in Railway, then set `NC_PUBLIC_URL` to `https://<your domain>` and redeploy.

**Upgrading.** Change the image tag on the NocoDB service and redeploy. NocoDB migrates its metadata on start, so back up Postgres first.

**Back up.** Postgres holds metadata and base data, and the NocoDB volume holds attachments.

**Resources.** A fresh deploy uses about 520 MB of RAM for NocoDB and about 75 MB for Postgres, so it needs the Hobby plan or above. `NODE_OPTIONS=--max-old-space-size=512` caps NocoDB's heap; without it NocoDB idles near 0.9 GB. Raise the cap for big CSV imports or many users.

## Why Deploy NocoDB on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying NocoDB on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
