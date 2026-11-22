# Pi dictation cleanup

A [Pi](https://pi.dev/) extension that cleans up dictated text in the terminal editor before you submit it. I use it with [Voxtype](https://voxtype.io/), configured to append ` /?` (including the leading space) to each transcript without pressing Enter. When the extension sees that suffix, it uses recent conversation context to correct the last line and puts the result back in the editor for you to review or edit. Nothing is submitted automatically.

Tested with Pi 1.6.0. Any dictation tool can trigger cleanup by typing or pasting the same suffix. The extension does not record or transcribe audio.

## Install

From a local checkout:

```sh
pi install /path/to/pi-dictation-cleanup
```

For a checkout hosted on GitHub, install it as a Git package:

```sh
pi install git:git@github.com:OWNER/pi-dictation-cleanup.git
```

Disable any previous standalone `~/.pi/agent/extensions/dictation-cleanup.ts` before loading the package to avoid duplicate hooks. Run `/reload` in existing sessions.

## Use

- Configure your dictation tool to append ` /?` without sending Enter. In [Voxtype](https://voxtype.io/), set `append_text = " /?"` and `auto_submit = false` under `[output]`.
- Pi corrects the last line in place and prefixes it with `🎙️: `. Earlier lines in the draft are used as context without being rewritten. The cursor moves to a new line for the next take.
- Edit or dictate more before pressing Enter. Pi never submits automatically. Escape or a cleanup failure leaves the text unchanged.
- Type `/verbatim` followed by a space before dictating to preserve the original separately in the saved Pi transcript when you submit. That entry is not added to the agent’s model context.

No `AGENTS.md` change is needed. The extension adds guidance about potential mishearings when you first submit a message containing either dictation marker. It keeps the guidance on that conversation branch after compaction.

## Model and privacy

- `/dictation-model` picks a cleanup model without changing the coding model. Use `/dictation-model current` to follow the session model.
- `/dictation-context 20000` sets the recent chat budget in characters. Defaults are 8,000 with the session model and 32,000 with a dedicated model. Use `0` to omit recent chat or `default` to restore the default.
- The selected model provider receives the new dictation, earlier lines of the draft, and bounded recent user and assistant text before you press Enter. The cleanup call does not include audio, images, tool output, or hidden reasoning. An unavailable model override does not fall back to another provider.
- Settings are stored locally in the Pi agent directory as `dictation-cleanup.json`. Configure them separately on each machine.

Inspired by [pi-prompt-enhance](https://github.com/zeroicey/pi-prompt-enhance).
