# Digital Postcards

A React + TypeScript app for creating and sharing animated digital postcards with friends via shared groups.

## Features

- **Postcard Editor** — 8 templates, live preview, stickers, font picker, 5 animations
- **AI Suggestions** — Claude-powered message generator (requires API key)
- **Groups** — Create groups with shareable invite codes, send postcards to a shared feed
- **Preview Page** — Shareable link per postcard with envelope reveal animation
- **Persistence** — State saved to localStorage (swap for Supabase for multi-user)

## Quick Start

```bash
npm install
npm run dev
```

Open [http://localhost:5173](http://localhost:5173).

## AI Message Suggestions

The "✦ AI suggest" button in the editor calls the Anthropic API. To enable it:

1. Get an API key from [console.anthropic.com](https://console.anthropic.com)
2. Create a `.env` file in the project root:

```
VITE_ANTHROPIC_API_KEY=sk-ant-...
```

3. Update `src/utils/api.ts` to pass the key in the Authorization header:

```ts
headers: {
  'Content-Type': 'application/json',
  'x-api-key': import.meta.env.VITE_ANTHROPIC_API_KEY,
  'anthropic-version': '2023-06-01',
  'anthropic-dangerous-direct-browser-access': 'true',
},
```

> ⚠️ For production, proxy API calls through your own backend to keep the key secret.

## Project Structure

```
src/
├── components/
│   ├── PostcardCanvas.tsx      # Renders the postcard visual
│   ├── Toolbar.tsx             # Editor controls (template/text/style/anim)
│   ├── HomePage.tsx            # Groups overview
│   ├── PostcardEditor.tsx      # Create/edit page
│   ├── GroupPage.tsx           # Shared group feed
│   ├── PreviewPage.tsx         # Shareable postcard view
│   ├── SendModal.tsx           # Group picker modal
│   └── Toast.tsx               # Notification toasts
├── data/
│   └── constants.ts            # Templates, stickers, fonts, sample data
├── hooks/
│   └── useGroups.ts            # Group + postcard state (localStorage)
├── utils/
│   └── api.ts                  # Anthropic API helpers
├── types.ts                    # Shared TypeScript types
├── App.tsx                     # Root + routing
└── main.tsx                    # Entry point
```

## Connecting a Backend (Supabase)

Replace `useGroups.ts` with Supabase queries. Suggested schema:

```sql
-- Users (handled by Supabase Auth)

create table groups (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  code text unique not null,
  created_at timestamptz default now()
);

create table group_members (
  group_id uuid references groups(id),
  user_id uuid references auth.users(id),
  primary key (group_id, user_id)
);

create table postcards (
  id uuid primary key default gen_random_uuid(),
  group_id uuid references groups(id),
  from_name text,
  location text,
  message text,
  template text,
  stickers text[],
  font text,
  anim text,
  created_at timestamptz default now()
);
```

## Tech Stack

- React 18 + TypeScript
- Vite
- React Router v6
- CSS Modules
- Anthropic Claude API (optional, for AI suggestions)
# 1962final
