---
name: schedule-posts
description: Schedule social posts in Postcue from files in this repo (a POST.md with its frames), check which accounts have gone quiet, or move and change planned posts. Use when the user asks to schedule, plan, queue or reschedule a social post, or asks what is coming up or which account needs a post.
---

# Schedule posts with Postcue

Postcue is a posting reminder. It never publishes: at the due time the user's phone gets a cue with the frames and
caption ready, and the user posts by hand in the network's app. Never tell the user a post "will go out" on its own.

## Before scheduling

1. Call `list_workspaces` to get the workspaces, their time zones and each account's ref
   (`workspace/platform/handle`). Use that ref or the channel id; don't guess handles.
2. If the account doesn't exist yet, ask before calling `add_channel` or `create_workspace`.

## Scheduling a post from this repo

1. Read the post's text file (often `POST.md`): title, caption, hashtags, the account and the time.
2. Find the frames (images or videos) in order. Frames are files on disk, so upload each one:
   - call `get_upload_link` once per file;
   - send the file with `curl --fail -T <file> '<upload_url>'`;
   - pass the `upload_id`s to `create_post` in the order the frames should appear.
3. Call `create_post` with the channel, `kind` (feed, story, reel, photo_post, thread or text), `due_at` as a local
   wall time plus zone (`2026-11-20T18:00 Europe/Berlin`), a short `title` for the push, the caption and the frames.
   Put music ideas, people to tag and the first comment in `notes`; they are shown to the user, not posted.
4. If the response has a cadence `warning`, tell the user. The post is saved anyway.

## Other requests

- "What's coming up?": `list_posts` with `compact: true`.
- "Which account is quiet?": `get_rhythm`, quietest first.
- Moving several posts: `move_posts` with `dry_run: true` first, then for real. It returns a `move_id` for undo.
- Changing one post: `update_post`. `assets` replaces every frame, so send the full list.
- `cancel_post` and `skip_post` need `confirm: true`; call them without it first and show the user the preview.
- The user posted something by hand without a reminder: `log_post`. The reminder went out and they posted it:
  `mark_posted`.
