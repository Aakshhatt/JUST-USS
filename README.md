# Demo link:-https://cheerful-entremet-a58c05.netlify.app
# Our Story

A little web app for making a personalized, interactive surprise page for your partner — a photo, a timeline of your relationship, a memory-match game, and a quiz about your story together. Anyone can sign up, build their own version, and share a code with their partner to unlock it.

## How it works

There are two sides to this:

**The creator** signs up with an email and password, then goes through a short builder: add a photo, write out a few moments from your relationship, pick a theme, choose icons for the memory game, and write a quiz only your partner would be able to pass. Everything saves to your account so you can come back and edit it later.

**The partner** doesn't need an account at all. They just go to the site, type in the code you give them, and answer a security question you set (something like "where was our first date?"). Get it right, and the page unlocks.

## Stack

- Single HTML file, no build step, no framework
- [Supabase](https://supabase.com) for auth and storage (free tier is plenty for this)
- Hosted wherever — Netlify, GitHub Pages, Vercel, doesn't matter, it's static

## Setting it up yourself

1. Create a Supabase project.
2. In the SQL editor, run:

    ```sql
    create table portals (
      user_id uuid primary key references auth.users(id) on delete cascade,
      code text unique not null,
      data jsonb not null,
      updated_at timestamptz default now()
    );

    alter table portals enable row level security;

    create policy "owner manages own portal"
      on portals for all
      using (auth.uid() = user_id)
      with check (auth.uid() = user_id);

    create policy "anyone can view by code"
      on portals for select
      using (true);
    ```

3. Grab your project URL and anon/public key from Project Settings → API.
4. Open `index.html` and paste both into the two constants near the top of the script tag.
5. In Authentication → Settings, turn off "Confirm email" if you want people to be able to sign up and start using it right away.
6. Deploy the file anywhere that serves static HTML.

## A note on security

The security-question answer is stored as plain text and checked in the browser — it's meant to keep the surprise a surprise, not to hold up against someone determined to guess it. Don't put anything actually sensitive behind it.

## Why it's built this way

No backend code, no servers to maintain — just one HTML file and a database. Anyone forking this can have their own copy running in about ten minutes.
