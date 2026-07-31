---
name: chatcut-video-editing
description: "Use ChatCut to turn one or more local or project video clips into an editable speech-led cut: import and sequence original media, tighten pauses and breaths, remove fillers or retakes with transcript-aware editing, add readable captions and restrained effects, verify the composed timeline, and export only when requested. Trigger for ChatCut editing, talking-head or 口播 cleanup, multi-clip assembly, subtitles, light effects, or final MP4 delivery."
---

# ChatCut Video Editing

Build the result as an editable ChatCut timeline. Preserve original source clips as timeline items; a locally flattened video is not the primary deliverable.

## Operating Rules

1. Treat the active ChatCut MCP manifest and returned tool schemas as the runtime contract. Never invent IDs or legacy payloads.
2. Establish the intended project before non-trivial work. Use the project from an active editor URL when it is unambiguous; otherwise list, create, or target a project.
3. Inspect current state before mutating it. Users may edit the project manually between calls.
4. Work in dependency order: source structure, speech timing, audio smoothing, captions, effects, verification, export.
5. Do only the requested treatments. Captions, effects, music, B-roll, and export are not automatic extras.
6. Keep user-facing language about clips, sentences, and visible results. Never expose segment IDs, item IDs, tokens, or internal transcript anchors.

## End-to-End Workflow

### 1. Establish the Project

- Use `list_projects`, `create_project`, `target_project`, or `get_editor_url` as appropriate.
- Surface the live editor when the host returns a browser handoff.
- Read project orientation and the intended timeline before editing.
- If access fails, verify the exact project ID and that the editor and connector use the same ChatCut account. Do not debug infrastructure or guess another project.

### 2. Discover or Import Sources

- Call `browse_assets` first to avoid duplicate imports.
- For readable local files that are absent, load the ChatCut `asset-import` skill and follow its `import_media` session plus bundled upload-helper workflow.
- Import at most four files per session; create additional sessions for larger batches.
- Use the returned asset IDs. Wait for transcription before transcript or caption work. Wait for upload readiness only before byte-dependent operations such as cloud frame rendering or export.
- Sequence original clips on the same video track in the requested or natural order. Use exact frame-native placement and avoid overlaps.

Read [references/workflow.md](references/workflow.md) for the detailed import and sequencing playbook.

### 3. Finish the A-Roll First

Use transcript-aware Script editing for spoken-content decisions:

1. Call `read_script` and understand the complete thought across adjacent transcript rows.
2. For mechanical cleanup, run `clean_script` first. A useful natural default for “剪气口” is pause compression around 300 ms, not deletion of every breath.
3. Re-read Script after cleanup; the previous materialization is stale.
4. Remove failed attempts, context-dependent fillers, semantic repetition, or unwanted words through `timeline.md` and `apply_script`. Preview when available, then commit.
5. Re-read the regenerated Script and check meaning, order, and pacing.
6. Run `smooth_audio` after the final structural speech edit when the active manifest exposes it.

Prefer the smallest cut that removes the defect. Preserve subjects, connectors, useful setup, and natural breathing room. Do not assemble speech by converting transcript timestamps into manual `split_item` operations.

### 4. Add Captions After Timing Is Stable

- Ensure transcription is ready.
- Use `edit_captions` to enable captions and prefer a real built-in preset for a general style request.
- Use custom styling only when requested. For a clean Chinese talking-head fallback, use high-contrast white text, a dark outline or shadow, bottom-center placement, no more than two lines, and conservative CJK density.
- Use `read_captions` to verify text, page breaks, timing, and safe placement.
- Correct ASR source words with `manage_transcript` rather than disguising errors in caption display text.

### 5. Add Restrained Effects

- Add effects only when requested or clearly included in an end-to-end brief.
- Search the current ChatCut Library with `browse_library`; inspect the selected item and follow its returned usage guidance.
- Apply a small, editorially motivated set: a gentle push on an opening or demonstration, a short shake on a reaction, or a punch zoom on one key line.
- Validate an effect mutation before committing when the tool supports validation.
- Keep magnification restrained and inspect settled frames. If an effect crops the face, text, product, or screen content, reduce or remove it.
- Use transitions only when both source clips have enough handles. If the feasible transition length is zero, keep a hard cut or use a brief effect; never create freeze frames to force a transition.

### 6. Verify Before Reporting Completion

Use both kinds of evidence:

- Structural: re-read the affected timeline/items and confirm order, durations, gaps, overlaps, track placement, captions, and effect bindings.
- Visual: render composed timeline frames with the current manifest's screenshot/frame tool and inspect the returned pixels. Include the opening, every important seam/effect, a caption-heavy moment, and the ending.

A successful mutation or signed image URL is not visual proof. If verification is blocked, state the blocker and direct the user to the live editor; do not claim the result looks correct.

### 7. Export Only on Explicit Request

- Confirm the active timeline and intended range, format, resolution, frame rate, and codec from project state and user intent.
- For a standard final video, use `submit_export` for MP4/H.264 with project-native framing unless the user specifies otherwise.
- Record every render ID and use `track_export`, not generation progress, until the job is terminal.
- Deliver only a completed download URL or file. Never call a queued render “exported.”
- If cloud rendering cannot read local-only media, complete the authorized import/upload path or report the blocker. Do not silently replace the editable project with a local flatten.

## Scope Gates

- Do not add music, B-roll, generated media, Motion Graphics, translations, or extra versions unless the user asks.
- Paid generation tools require explicit intent for that exact generation.
- Destructive project deletion, ambiguous target replacement, or broad timeline resets require explicit confirmation.
- Preserve unrelated tracks and user edits.

## Completion Standard

Finish only when every requested treatment is present, the timeline remains editable, structural read-back passes, visible frames have been inspected, and any requested export is complete and accessible.

Before handoff, run the checklist in [references/qa-checklist.md](references/qa-checklist.md).

## Examples

Positive triggers:

- “把这几段口播按顺序串起来，剪掉气口，上中文字幕，再加一点轻特效。”
- “Clean this talking-head video in ChatCut and export a 1080p MP4.”
- “Keep the edit live in ChatCut; remove the failed takes but preserve natural pauses.”

Negative triggers:

- “只分析这段视频的文案，不要剪辑。” Use a transcript/content-analysis workflow instead.
- “用 Premiere 手工剪辑。” Do not redirect the task to ChatCut.
- “生成一条完全没有源素材的 AI 视频。” Use the appropriate video-generation workflow.
