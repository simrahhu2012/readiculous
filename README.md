# Readiculous — Starter Edition

A vintage-inspired, invite-only reading and writing community starter project.

## Included
- Multi-page responsive frontend with light/dark themes
- Invitation-only registration flow
- Supabase authentication integration
- Member dashboard, discover page, bookshelf, profile
- Chapter-based writing studio with autosave
- Book cover designer (template, colour, title/author, decorative elements, PNG export)
- Member invitation panel in profiles (create, copy, view and revoke invitations)
- PDF upload/reader support for authorised files
- SQL schema, Row Level Security policies, invitation functions
- Supabase Edge Function for creating invitations

## Important setup

This project is a **starter implementation**, not a hosted service. Real accounts, shared storage, invitations and cross-device persistence require your own Supabase project.

### 1. Create a Supabase project
Create a project at https://supabase.com and copy:
- Project URL
- Publishable/anon key

Open `js/config.js` and replace the placeholders. Never put a service-role key in frontend code.

### 2. Run the database setup
In Supabase SQL Editor, run:
1. `database/schema.sql`
2. `database/policies.sql`

The SQL includes:
- Profiles and membership records
- Invite tokens stored as hashes
- Invite validation during account creation
- Books, chapters, shelves, reading progress and bookmarks
- Storage bucket definitions and policies

For production, review all policies and test with separate accounts before inviting anyone.

### 3. Configure invitation creation
The Edge Function in `supabase/functions/create-invitation/index.ts` allows an active member to create an invitation, subject to the five-active-invitation limit. The Profile page includes an invitation panel to create and copy links and review/revoke outstanding invitations.

Deploy it with the Supabase CLI:
```bash
supabase functions deploy create-invitation
```
The function uses the caller's access token to verify membership and a server-side service role key to call the invitation RPC. Supabase manages the function secret; do not expose it in the browser.

### 4. Configure authentication
Enable email/password authentication and email confirmation in Supabase.
The signup page requires:
- A valid invitation code
- The same email address the invitation was issued to
- Email confirmation

The database trigger validates and consumes the invitation at account creation. Uninvited accounts do not receive an active membership and cannot access member data. For a strict production deployment, disable unrestricted signups and route registration through a server-side invitation acceptance function that uses Supabase Admin invite APIs. The supplied client flow plus database guard is a starter configuration; verify the current Supabase Auth settings available in your project.

### 5. Storage
The SQL creates private buckets for:
- `avatars`
- `covers`
- `manuscripts`
- `published-books`

Review bucket limits and MIME types in Supabase. PDF files must be owned by the uploader or licensed/authorised for distribution. Do not upload copyrighted books without permission.

### 6. Run locally
Because this uses ES modules, run a local web server from this folder:
```bash
python -m http.server 8000
```
Then visit http://localhost:8000. On Windows, you can also double-click `START_READICULOUS_WINDOWS.bat` if Python is installed. Do not open the other pages by double-clicking them: JavaScript modules and Supabase need a local web server.

Do not open HTML files directly with `file://`.

### 7. Deploy frontend
You can publish the frontend through GitHub Pages after adding your Supabase URL and publishable key. Configure the correct site URL and redirect URLs in Supabase Auth.

## Current scope
This is the Starter edition. Friends, chat, coin wallet, rewards shop, book clubs and advanced author analytics are reserved for a later release.

## Security checklist before real launch
- Test signup with valid, expired, revoked and wrong-email invitation codes.
- Test data isolation using two separate accounts.
- Test private draft and manuscript storage access.
- Configure rate limits, email templates, backups and abuse reporting.
- Add a real admin workflow for reviewing books and handling copyright complaints.
- Consider an external security review before inviting real members.
