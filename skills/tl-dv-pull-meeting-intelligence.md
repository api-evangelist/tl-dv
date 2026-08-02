---
name: Pull meeting intelligence from tl;dv
description: List recent meetings and retrieve the transcript and AI notes for one, using the tl;dv API.
api: openapi/tl-dv-openapi.yml
operations: [getMeetings, getMeeting, getTranscript, getNotes]
---

# Pull meeting intelligence from tl;dv

Use the tl;dv API to find a meeting and retrieve its transcript and AI-generated notes.

## Auth
- Send your API key on every request as the `x-api-key` header.
- Get a key at https://tldv.io/app/settings/personal-settings/api-keys
- Base URL: `https://pasta.tldv.io`

## Steps
1. **List meetings** — call `getMeetings` (`GET /v1alpha1/meetings`). Page through results with `page`/`limit`; the response envelope carries `page`, `pages`, `total`, `pageSize`, `results[]`. Optionally filter with `meetingType=internal|external`.
2. **Pick a meeting** — take the `id` of the target meeting from `results[]`. Optionally call `getMeeting` (`GET /v1alpha1/meetings/{meetingId}`) for full metadata (organizer, invitees, duration).
3. **Get the transcript** — call `getTranscript` (`GET /v1alpha1/meetings/{meetingId}/transcript`). Iterate `data[]` where each segment is `{speaker, text, startTime, endTime}`.
4. **Get the notes** — call `getNotes` (`GET /v1alpha1/meetings/{meetingId}/notes`). Use `markdownContent` for a ready summary, or walk `topics[]` and `structuredNotes[]` for structured output.

## Error handling
- `401`/`403`: missing or unauthorized API key — check the `x-api-key` header.
- `404`: the `meetingId` does not exist.
- Error bodies are `{name, message}` (or `{message, errors[]}` for `400`).
