# Baro content pipeline roadmap

## Goal

Turn a source video and an approved script into a review-ready MP4 using original footage, generated shots where needed, and a reusable channel theme. The script drives the edit. The first release ends with a local export for human review.

## Responsibilities

| Component | Role |
| --- | --- |
| OpenAI or Claude | Draft a structured shot list from the script and timestamped transcript. Choose one provider for the first integration. |
| Higgsfield | Generate approved shots that fill gaps in the source footage. |
| Suno | Create the theme once. Export approved intro, bed, and outro audio for reuse. |
| FFmpeg | Trim and assemble video, mix narration and music, and render the review MP4. |
| Google Drive | Hold episode media, shared theme assets, and exports. The existing media workspace has `video/` and `theme_song/`. |

## Episode contract

Each episode has a stable ID, a source video, a script, and an optional voiceover. The script defines the sequence and spoken content. Use source dialogue where it matches the script and record a voiceover for new lines. Measure speech timing before locking picture.

Use a versioned `timeline.json` as the handoff between planning and rendering. Each shot records its timeline start and duration, narration or source dialogue, the source asset and trim range or a generation prompt and reference frame, and any associated music cue. Generated shots also record the approved local clip and provider request ID once available. A music cue identifies an approved theme version, its start and end times, fades, and whether to duck under speech.

Keep the timeline, generated clips, and review render with the episode so an editor can revise one shot or cue and render again. Validate source files, time ranges, and required assets before rendering.

## Implementation milestones

| Milestone | Build | Done when |
| --- | --- | --- |
| 1. Local pilot | Create one 30–60 second episode with a hand-authored timeline, original footage, narration, and the approved theme. Render with FFmpeg. | The same timeline renders twice with matching cuts and cue timing. Speech is clear, and the theme enters and exits where intended. |
| 2. Script-to-timeline planning | Add transcription with timestamps and one planning provider. Validate its timeline against the input files and let an editor correct and approve it. | A real script produces an editable shot list with plausible timing and traceable source footage. Invalid timelines fail before rendering or billable video generation. |
| 3. Generated shots | Add Higgsfield after shot approval. Set a per-episode shot cap. Save request IDs, download results, and keep approved clips by shot and revision. | A restart can resume a known request. Rendering again reuses approved clips, and changing one shot regenerates only that shot. |
| 4. Upload intake | Add a folder watcher or scheduled scan. Start when the source and script are complete. Persist job status and deduplicate repeated upload events. | A partial sync waits, duplicate events create one job, and a failed run can resume from its last completed step. |
| 5. Unattended operation | Deploy a worker with separate production credentials, usage limits, and logs. Surface actionable failures and keep final review manual. | One upload reaches a review export end to end. A provider failure is visible, and retrying a job does not submit duplicates for known requests. |

For ambiguous Higgsfield submissions, such as a timeout before a request ID is saved, reconcile the existing request or flag it for review before submitting again. Start with a small shot cap, such as three per episode, and review actual usage before raising it.

## Configuration and credentials

- Keep ordinary settings in Git, including model IDs, aspect ratio, output resolution, shot limits, and theme asset names. Keep episode media outside Git.
- Commit an `.env.example` with empty credential placeholders when adding provider integrations. Keep live secrets in a local secret store outside Google Drive or in the deployed worker's secret manager. Inject them at runtime and keep them out of logs.
- Start with a development OpenAI project or Anthropic workspace. Add a separate production project or workspace when deploying the worker. Use scoped service credentials and configure spend and rate limits for the active providers.
- Add Higgsfield credentials when milestone 3 begins. Suno stays a manual step for the reusable theme. Archive the theme WAVs and commercial-use documentation alongside the approved assets.

## Pilot decisions

Before implementing provider integrations, use the local pilot to decide whether source dialogue or newly recorded narration should lead the edit, which upload folder and file naming convention to watch, and which aspect ratio to render first. Confirm the chosen planning provider and Higgsfield model when their milestones begin.

## References

- [OpenAI production best practices](https://developers.openai.com/api/docs/guides/production-best-practices)
- [Anthropic workspace setup](https://platform.claude.com/docs/en/manage-claude/workspaces)
- [Higgsfield API quick start](https://open.higgsfield.ai/quick-start)
- [Suno commercial-use rights](https://help.suno.com/en/articles/9601665)
