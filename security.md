---
title: Security policy
---

# Security policy

*Migration Permission Check for Confluence Cloud. Last updated {{ site.policy_date }}.*

## How the app is built

- **Runs on Atlassian.** The app is built on Atlassian Forge and uses only Atlassian-hosted compute. It declares no external network access (egress) and no remote backend, so it cannot send your data anywhere outside Atlassian.
- **No storage.** The app keeps no data between uses: no Forge storage, no database, no files.
- **Least privilege.** The app requests three read-only Confluence scopes and nothing that writes:
  - `read:space:confluence` to list spaces and read their permissions,
  - `read:confluence-groups` to turn group IDs into group names,
  - `read:confluence-user` to show account names, account types and visible email addresses.
- **Acts as the user.** Every Confluence API call is made as the signed-in administrator, never as the app, so a check can never read more than that person could see in Confluence.
- **Admin-only.** The app's only page sits in Confluence administration settings.
- **Browser-side processing.** Uploaded files are parsed and compared in the administrator's browser. Only space keys and space IDs are sent to the backend function.
- **Read-only export.** The Data Center export is a plain `SELECT` query that you can read in full before running it.

## Reporting a vulnerability

Email [{{ site.contact_email }}](mailto:{{ site.contact_email }}) with the subject "Security". Please include steps to reproduce. We acknowledge reports within 3 business days, keep you informed while we fix them, and credit you if you wish. Please don't publicly disclose an issue before a fix is released.

## Fixes

Security fixes are released as Forge app updates, which Atlassian rolls out to every installed site automatically for changes that need no new permissions.

## Dependencies

The app depends only on Atlassian's Forge libraries (`@forge/api`, `@forge/bridge`, `@forge/resolver`) at runtime. Dependencies are kept current and checked with `npm audit` before each release.
