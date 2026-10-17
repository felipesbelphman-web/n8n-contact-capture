# Contact Capture — n8n & Supabase

A learning project that captures quote requests through an n8n form, builds a personalized message, and stores each request in Supabase.

## Business use case

Small service businesses can collect quote requests in one database instead of manually copying form responses. This version demonstrates data capture and storage; it does not send emails or notifications.

## Workflow

1. **Receive contact form** — collects required `name` and `service` text fields.
2. **Build confirmation message** — preserves the input and adds `message` using n8n expressions.
3. **Save contact to Supabase** — inserts `name`, `service`, and `message` into `public.contacts`.

Example generated message:

> Hi, Sofia! We've received your quote request for Website redesign.

The message is stored in the database. The form currently shows the default submission confirmation, rather than this personalized message.

## Files

- `contact-capture.json`: sanitized workflow template for import into n8n.
- `schema.sql`: table setup and permissions for a dedicated Supabase project.

## Setup

1. Create a dedicated Supabase project with the Data API enabled.
2. Run `schema.sql` in that project's SQL Editor. If the table already exists, check its columns and identity sequence before using the script.
3. Import `contact-capture.json` into n8n.
4. Open **Save contact to Supabase**, connect your own Supabase OAuth account, and select your project in place of `YOUR_SUPABASE_PROJECT_REF`. Confirm the table is `contacts`.
5. Check the mappings: `name` → `{{ $json.name }}`, `service` → `{{ $json.service }}`, and `message` → `{{ $json.message }}`.
6. Execute the workflow and submit the test form while the trigger is listening.
7. Confirm a row appears in Supabase.
8. Publish the workflow and open the **Production URL** from the form trigger. Submit a new sample request without manually executing the workflow; confirm the row in Supabase and inspect n8n Executions.

This export uses OAuth authentication and preserves the node versions of the original workflow. Your n8n installation must support them. The template has not been reimported and executed in a separate instance.

## Validation performed on the original workflow

| Sample name | Service | Verification |
| --- | --- | --- |
| Carlos | Online store | Row saved with generated message |
| Ana | Landing page | Full test form submission saved |
| Sofia | Website redesign | Published form submission saved |

These are fictional sample contacts. Verification was manual, through n8n output and the Supabase Table Editor.

## Security and configuration

- Row Level Security remains enabled.
- The SQL grants SELECT and INSERT to the backend `service_role`; it does not grant public anonymous access.
- Store credentials in n8n's credential manager. Never put access tokens or secret keys in this repository or frontend code.
- Account credential references, project reference, workflow/instance metadata, and the original form webhook identifier have been removed or replaced in this template.
- Publishing a workflow does not mean publishing its editor or database. The form is accessible to people with its URL and currently has no authentication.

## Current scope and next improvements

This is a portfolio learning project, not a finished client deployment. It currently collects only name and service, with no contact email or phone number. Repeated submissions create separate rows.

Next improvements: contact email, server-side validation and trimming, duplicate handling, error notifications, spam protection, and a custom landing page. Notification delivery belongs to a future version.

## What I practiced

n8n form triggers, JSON fields and values, dynamic expressions, field mapping, Supabase integration, database permissions, and test versus production form URLs.
