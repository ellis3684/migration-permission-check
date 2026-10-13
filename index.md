---
title: Documentation
---

# Migration Permission Check

Find out who **lost or gained access** to each Confluence space when you moved from Data Center to Cloud.

Atlassian's migration tools copy space permissions but don't verify them afterwards. Users whose emails changed, groups that were renamed, and anonymous access that wasn't carried over all leave people locked out or let people in who shouldn't be. This app compares your Data Center permissions with what Confluence Cloud has now, space by space and person by person, and shows every difference.

- [Before you start](#before-you-start)
- [1. Export permissions from Data Center](#1-export-permissions-from-data-center)
- [2. Run the check in Confluence Cloud](#2-run-the-check-in-confluence-cloud)
- [3. Read the results](#3-read-the-results)
- [What is compared](#what-is-compared)
- [Troubleshooting](#troubleshooting)
- [Privacy and security](#privacy-and-security)

## Before you start

You need:

- **Confluence Cloud administrator** access, to open the app and read every space's permissions.
- **Read access to the Confluence Data Center database**, or to a restored backup of it, to run one `SELECT` query.

The best time to export is just before you migrate. If you've already migrated, run the query against a backup taken before the migration.

## 1. Export permissions from Data Center

Run this read-only query against your Data Center database and save the result as **CSV with the header row**. It works on PostgreSQL, MySQL, SQL Server and Oracle. The same query is shown on the app's page with a copy button.

```sql
-- Migration Permission Check: Data Center space permission export.
--
-- Run this against your Confluence Data Center database BEFORE migrating
-- (or against a restored backup of it), save the result as CSV with the
-- header row, and upload the CSV to the Migration Permission Check page in
-- Confluence Cloud.
--
-- It only reads. It works on PostgreSQL, MySQL, SQL Server and Oracle.
-- One row per space permission grant:
--   * group grants have group_name set,
--   * user grants have username / display_name / email set,
--   * anonymous grants have neither (that's how Confluence stores them).
-- A user in several directories can appear on more than one row; the app
-- de-duplicates them.
--
-- Examples:
--   PostgreSQL:  psql -d confluence --csv -f space-permissions.sql -o space-permissions.csv
--   MySQL:       mysql confluence -B < space-permissions.sql | sed 's/\t/,/g' > space-permissions.csv
--                (tab-separated output; fine unless names contain commas,
--                 otherwise export from MySQL Workbench as CSV)

SELECT
    s.SPACEKEY        AS space_key,
    s.SPACENAME       AS space_name,
    sp.PERMTYPE       AS permission,
    sp.PERMGROUPNAME  AS group_name,
    um.username       AS username,
    u.display_name    AS display_name,
    u.email_address   AS email
FROM SPACEPERMISSIONS sp
JOIN SPACES s
    ON s.SPACEID = sp.SPACEID
LEFT JOIN user_mapping um
    ON um.user_key = sp.PERMUSERNAME
LEFT JOIN cwd_user u
    ON u.lower_user_name = um.lower_username
WHERE s.SPACESTATUS = 'CURRENT'
ORDER BY s.SPACEKEY, sp.PERMTYPE;
```

Only current spaces are exported; archived spaces are skipped. One row is one permission grant: group grants have `group_name` set, user grants have the user columns set, and anonymous access has neither.

## 2. Run the check in Confluence Cloud

1. Go to **Settings** (the gear icon) → **Migration Permission Check**. The first time, Confluence asks you to allow the app to read spaces on your behalf.
2. Under **Data Center export**, choose the CSV from step 1.
3. Optional but recommended: under **Cloud user export**, choose the user export from **Atlassian Administration → Users → Export users**. Confluence Cloud often hides email addresses; with this file every account is matched by email instead of by display name.
4. Click **Check permissions**. Large sites take a moment; the page shows progress as each space is checked.

## 3. Read the results

The summary tiles count spaces that lost access, permissions lost and gained, spaces missing from Cloud, Data Center users with no Cloud match, and spaces that couldn't be checked.

Each space then lists every person, group or role whose permissions changed:

| Column | Meaning |
| --- | --- |
| **Who** | The user, group, anonymous access, or Cloud role, with how it was matched. |
| **Lost** | Permissions they had in Data Center and don't have in Cloud. |
| **Gained** | Permissions they have in Cloud that they didn't have in Data Center. |

Space badges:

- **N permissions lost**: someone lost access. Start here.
- **Not in Cloud**: no Cloud space has this key. It wasn't migrated, or its key changed.
- **Not checked**: Confluence returned an error for this space (usually your account can't read its permissions).
- **Nothing lost** / **Unchanged**: no losses.

Use **Only show spaces with lost access** to focus on problems, and **Download CSV** for the full report to share or track fixes.

## What is compared

Direct space permissions granted to users, groups and anonymous access. Data Center permissions map to Cloud like this:

| Data Center | Cloud |
| --- | --- |
| `VIEWSPACE` | View space |
| `EDITSPACE` | Add pages |
| `EDITBLOG` | Add blog posts |
| `COMMENT` | Add comments |
| `CREATEATTACHMENT` | Add attachments |
| `REMOVEPAGE` | Delete pages |
| `REMOVEBLOG` | Delete blog posts |
| `REMOVECOMMENT` | Delete comments |
| `REMOVEATTACHMENT` | Delete attachments |
| `REMOVEOWNCONTENT` | Delete own content |
| `SETPAGEPERMISSIONS` | Add/delete restrictions |
| `EXPORTSPACE` | Export space |
| `SETSPACEPERMISSIONS` | Admin space |
| `REMOVEMAIL` | Not carried over: Cloud has no mail archives |

**How people are matched.** Groups match by name. Users match by email address (from the optional user export, or from Confluence where the person's profile shows it), then by display name if exactly one Cloud account has that name. Data Center users with no match are listed rather than dropped, since they usually mean an account wasn't migrated or its email changed.

**Built into Cloud.** Cloud adds principals Data Center doesn't have, such as Confluence admins and app accounts. They're marked and hidden by default; tick **Show Cloud built-in principals** to see them. Gains for "All licensed users" are always shown, because they can expose a space more widely than before.

**Not compared yet:** page restrictions, access inherited through group membership, global (site-wide) permissions, and Cloud-only permissions such as whiteboards and databases.

## Troubleshooting

**"You don't have permission to read this space's permissions."** Your account isn't an admin of that space. Confluence admins can recover space permissions from *Settings → Space permissions*, then run the check again.

**Every user shows as "not matched".** Confluence is hiding email addresses. Add the Cloud user export in step 2.

**The CSV is rejected.** Make sure it has the header row and the column names from the query (`space_key, space_name, permission, group_name, username, display_name, email`). Exports from MySQL's command line are tab-separated; save as CSV from a SQL client instead if names contain commas.

**A space shows "Not in Cloud" but it was migrated.** Its space key changed during migration. Compare the Data Center and Cloud keys by hand for that space.

## Privacy and security

The app runs entirely on Atlassian Forge. Your files are read in your browser, nothing is stored, and no data leaves Atlassian. See the [privacy policy](privacy.html) and [security policy](security.html).

## Support

Questions or problems: [{{ site.contact_email }}](mailto:{{ site.contact_email }}). See [Support](support.html).
