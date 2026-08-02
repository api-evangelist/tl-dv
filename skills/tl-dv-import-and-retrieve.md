---
name: Import a recording into tl;dv and retrieve results
description: Import an external meeting recording, then retrieve its transcript once processing completes, using the tl;dv API and webhooks.
api: openapi/tl-dv-openapi.yml
operations: [importMeeting, getMeetings, getMeeting, getTranscript, downloadRecording]
---

# Import a recording into tl;dv and retrieve results

Import a recording from a URL, wait for tl;dv to process it, then pull the transcript and recording.

## Auth
- Send your API key on the `x-api-key` header. Base URL: `https://pasta.tldv.io`.

## Steps
1. **Import** — call `importMeeting` (`POST /v1alpha1/meetings/import`) with the recording `url`. The response is `{success, jobId, message}`; processing is asynchronous.
2. **Wait for readiness** — prefer webhooks over polling:
   - Configure a `MeetingReady` webhook (user/team/org level). Its payload is `{id, event, data, executedAt}`; `data` carries the meeting.
   - Add `TranscriptReady` if you specifically need transcript completion.
   - If not using webhooks, poll `getMeetings` (`GET /v1alpha1/meetings`) or `getMeeting` until the meeting appears/completes.
3. **Retrieve the transcript** — call `getTranscript` (`GET /v1alpha1/meetings/{meetingId}/transcript`).
4. **Download the recording** — call `downloadRecording` (`GET /v1alpha1/meetings/{meetingId}/download`); it returns a `302` redirect to a signed URL — follow the `Location` header.

## Notes
- No idempotency key is documented; avoid duplicate imports by tracking `jobId`.
- Error bodies: `{name, message}` (or `{message, errors[]}` on `400`).
