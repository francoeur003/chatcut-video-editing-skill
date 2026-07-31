# Workflow Reference

Use this reference for a standard multi-clip speech-led edit. Tool fields change over time, so read the active schema before each call.

## Project and Asset Flow

1. Establish or target the project.
2. Read timeline orientation and call `browse_assets`.
3. Match existing assets by filename, type, duration, and transcript state.
4. For missing local files, load the ChatCut `asset-import` skill:
   - call `import_media` with `action: "create_session"`;
   - run that skill's bundled `scripts/upload-media.mjs` with the returned token and endpoint;
   - pass no more than four local files per session;
   - read the helper's final JSON and retain each returned asset ID.
5. Track transcription readiness for speech media. Track upload readiness only when a later byte-dependent step requires it.

Never replace the bundled helper with handwritten upload, metadata, or transcode commands. If host policy denies file transfer, stop and ask the user to upload through an allowed surface or grant permission.

## Editable Assembly

- Place original source assets as individual timeline items.
- Sequential clips go on one video track in increasing time order; overlays go on higher video tracks.
- Prefer the editor's `track-end` alignment capability when exposed, or calculate exact non-overlapping starts from the latest timeline read.
- Keep the source order unless the user specifies another order or content makes a different order unambiguous.
- After assembly, read back the video track and confirm there are no unintended gaps or overlaps.

## Speech Cleanup

Use this order:

1. `read_script`
2. `clean_script` for fixed fillers and batch pauses
3. `read_script` again
4. edit `timeline.md` semantically
5. `apply_script` preview when available
6. `apply_script` commit
7. `read_script` final review
8. `smooth_audio` after the final structure change when available

For common “tighten breaths” requests, `silence: "compress:300"` is a useful starting point. Treat it as a maximum for long pauses, not an order to erase all silence. Keep longer pauses around topic changes when they serve meaning.

Script is the spoken-content surface. Use it for retakes, false starts, repeated ideas, highlights, excerpts, and reordering. Use `manage_transcript` only for ASR corrections. Use `find_transcript` for timing lookup, not for destructive speech assembly.

## Caption Pass

1. Confirm transcription is ready.
2. List language-aware templates when the user asks for a general style.
3. Enable the closest built-in preset.
4. Adjust only requested properties.
5. Read captions back and inspect a caption-heavy composed frame.

For precise page boundaries, use the current word-level caption display controls. Do not edit transcript content merely to change a caption line break.

## Light Effect Pass

Map effects to moments, not clip count:

- opening emphasis: subtle slow push;
- demonstration or screen reveal: gentle push or hold;
- reaction beat: very brief shake;
- key cost/result/CTA line: one punch zoom.

Search `browse_library`, inspect an exact library item, and use the returned `edit_item` guidance. Keep the set sparse. Validate first where supported, then inspect multiple frames including the settled state. Reduce zoom strength when face, UI, product, or subtitles leave their safe area.

Transitions require unused source handles on both sides of a seam. A zero-frame feasible transition is a hard constraint, not an invitation to extend with frozen frames.

## Verification Matrix

| Area | Structural proof | Visual proof |
| --- | --- | --- |
| Assembly | ordered items, exact starts/durations, no overlap | first frame and each source seam |
| Speech | regenerated Script reads naturally | inspect jump-cut moments when visible |
| Captions | enabled pages and word timing | caption-heavy frame is readable and safe |
| Effects | effect binding and intended range | mid-effect plus settled frame |
| Ending | last content item and project duration agree | final visible frame is intentional |

## Export Flow

1. Confirm the exact timeline/range.
2. Submit the requested export and save the render ID.
3. Check with `track_export` according to returned guidance; do not busy-poll.
4. Deliver only after status is complete and a download URL exists.
5. When a local delivery is required, use a collision-safe filename and verify media metadata and decodeability with the host's allowed tools.
