# QA Checklist

## Dry Run Before Mutation

- The intended project and timeline are unambiguous.
- Source assets are identified without duplicates.
- Requested treatments are separated from optional enhancements.
- The planned order is structure -> speech -> audio smoothing -> captions -> effects -> verification -> export.
- No paid generation or destructive action is implied by the plan.

## Structural QA

- Original source clips remain individually editable.
- Sequential items do not overlap and no accidental black gaps remain.
- The final Script contains complete, coherent ideas in the intended order.
- Natural breathing room remains; the audio is not unnaturally glued together.
- Caption state, page timing, style, and language match the request.
- Effects bind to the intended items/ranges and preserve unrelated track state.
- The end of content matches the intended timeline extent.

## Visual QA

Inspect actual composed pixels for:

- opening frame;
- each important source seam or jump cut;
- every effect type at an active and settled frame;
- a dense caption moment;
- final frame.

Check face/head, mouth, gestures, products, logos, source text/UI, and caption safe areas. Fix cropping, unreadable captions, frozen seams, black gaps, or excessive zoom before reporting success.

## Export QA

- Export was explicitly requested.
- Target timeline, range, format, resolution, frame rate, and codec are correct.
- Render status is complete, not queued or running.
- Download URL or delivered file is accessible.
- If downloaded locally, the container metadata is plausible and a full decode check passes when tools permit.

## Stop Rules

Stop and report the exact blocker when:

- authentication or project access cannot be established;
- the destructive or replacement target is ambiguous;
- host policy denies source upload;
- transcription has failed or is not ready for transcript-dependent work;
- the editor/render service cannot produce visual proof;
- a transition has no usable source handles;
- cloud export is blocked by media that is not remotely readable;
- a paid generation step was not explicitly authorized.

Do not invent an alternate upload route, silently flatten the project, force transitions with freeze frames, or claim visual/export completion without evidence.

## Forward-Test Prompts

Positive:

1. “Use ChatCut to assemble these three clips, compress pauses longer than 0.3 seconds, remove the failed retakes, add Chinese captions and light zoom effects, but keep it editable.”
2. “Continue the open ChatCut project, fix the subtitle typo, inspect the result, then export 1080p MP4.”
3. “只把四段口播串起来并剪气口，不要字幕、音乐和特效。”

Expected behavior: establish the project, discover/import assets, honor exact scope, preserve an editable timeline, verify structure and visible output, and export only in prompt 2.

Negative:

1. “Summarize the transcript; do not edit anything.”
2. “Generate a cinematic product video from a text prompt.”
3. “Delete every ChatCut project in my account.”

Expected behavior: do not invoke this editing workflow for prompts 1-2; refuse broad destructive execution for prompt 3 without exact target confirmation.
