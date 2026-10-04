# EasyAccess

Share text and images with nothing more than a file name and a password — no registration, no accounts.

**Live demo:** https://sharepass-two.vercel.app/

**Credit:** group project. Designed and built by Niyaz Khan; repository uploaded by Dhammanand Gaikwad.

## How it works

1. **Create** a file by choosing a name and a password.
2. Anyone who knows the name and password can **open** it, edit the text, add images, and save.
3. Files can be refreshed to pick up edits from someone else, and deleted.

There is no user table and no session: the file name plus its password is the whole access model.

## Stack

| Layer | Technology |
|---|---|
| Front end | React 18, TypeScript, Vite, Tailwind CSS, shadcn/ui, React Router, TanStack Query |
| Back end | Supabase (hosted Postgres) accessed directly from the browser with `@supabase/supabase-js` |

Data lives in two tables (typed in `src/integrations/supabase/types.ts`):

- `files` — `id`, `name`, `password_hash`, `content` (text), `created_at`, `updated_at`
- `file_images` — `id`, `file_id`, `image_data` (images stored as data URLs), `created_at`

The repository contains only the Supabase `config.toml`, not the table definitions, so running against your
own Supabase project means creating those two tables first.

## Run it locally

```bash
npm install
npm run dev
```

`src/integrations/supabase/client.ts` holds the Supabase project URL and publishable key. To use your own
project, replace those two values.

## How it was built

The project was scaffolded with [Lovable](https://lovable.dev) (Vite + React + TypeScript + shadcn/ui +
Supabase), which is visible in the early commit history.

## Limitations

This is a demo-scale project. In particular:

- **The password protection is not strong.** Passwords are reduced to a short non-cryptographic hash in
  the browser (`hashPassword` in `src/utils/supabaseUtils.ts`, marked "for demo purposes only"), and the
  check happens client-side: `authenticateFile` fetches the file by name and then compares hashes in the
  browser. Do not store anything sensitive in it.
- Access control is by file name and password only; the app has no rate limiting or account recovery.
- Images are stored inline as data URLs, which does not scale to large files.
