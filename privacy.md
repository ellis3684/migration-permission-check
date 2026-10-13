---
title: Privacy policy
---

# Privacy policy

*Migration Permission Check for Confluence Cloud. Last updated {{ site.policy_date }}.*

This policy explains what data the Migration Permission Check app ("the app") handles and what happens to it. The app is published by {{ site.partner }} ("we", "us") on the Atlassian Marketplace.

## The short version

- The app runs entirely on Atlassian's Forge platform. It has no servers of its own and sends no data outside Atlassian.
- The files you upload are read **in your browser** and are never sent to us or to the app's backend.
- The app stores nothing: no database, no Forge storage, no cookies, no analytics.
- We cannot see your data. We never receive it.

## What the app reads

When a Confluence administrator runs a check, the app handles:

1. **Your Data Center permission export** (a CSV or JSON file you choose). It contains space keys and names, permission types, group names, and usernames, display names and email addresses of people with space permissions. The file is parsed in your browser. Only the space keys are passed to the app's backend, so it can look those spaces up in Confluence Cloud.
2. **An optional Cloud user export** from Atlassian Administration (a CSV containing account names and email addresses). It is parsed in your browser and never leaves it.
3. **Your Confluence Cloud space permissions.** The app's backend function, hosted by Atlassian on Forge, calls the Confluence REST API on your behalf (as the signed-in admin, so it never sees more than you could). It reads space permissions, group names, and account display names, account types and email addresses where profile visibility allows. These results are returned to your browser and not kept.

The comparison and the downloadable CSV report are produced in your browser. The report is saved only where you choose to save it.

## What the app stores

Nothing. When you close or reload the page, everything the check loaded is gone. Uninstalling the app leaves no data behind, because there is none to delete.

## Logs

If a Confluence API call fails, the app's backend writes the HTTP status and the endpoint path (without query parameters) to Forge logs, which are hosted by Atlassian. The logs contain no names, email addresses, account IDs or permission data. Atlassian also records platform metadata about app invocations, as described in [Atlassian's privacy policy](https://www.atlassian.com/legal/privacy-policy).

## Sharing and third parties

We do not sell, share or transfer any data, because the app sends no data to us or to anyone else. The only service involved is Atlassian, which hosts Confluence Cloud and the Forge platform the app runs on.

## Data residency

The app has no storage of its own. The backend function runs on Atlassian's Forge platform, and the data it reads stays within Atlassian's infrastructure.

## Your rights

Because we never receive or hold personal data through the app, there is nothing for us to access, correct or delete. Questions about data in your Confluence site go to your site administrator or to Atlassian.

If you contact us for support by email, we use your email address and what you send us only to answer you, and we delete it when it is no longer needed for that.

## Changes

If a future version of the app changes how data is handled, we will update this page before that version is released and change the date at the top.

## Contact

{{ site.partner }} · [{{ site.contact_email }}](mailto:{{ site.contact_email }})
