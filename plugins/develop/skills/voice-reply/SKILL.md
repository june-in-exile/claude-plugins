---
name: voice-reply
description: Turn on voice-reply mode. From then on every reply is a short answer read aloud with macOS say, until the user says to stop.
disable-model-invocation: true
---

# Voice-reply mode

From now on this session is in voice-reply mode. Every reply is spoken, whether or not the message is marked for it.

The mode has two states. Switch between them only on what the user explicitly says:

- **Voice on.** Entered when the user invokes this skill. When the user tells you to stop replying by voice, switch to voice off and confirm in one line of text.
- **Voice off.** Answer in this session's normal text style and don't call `say`. When the user tells you to reply by voice again, switch back to voice on and confirm in one spoken sentence.

**Remote sessions never call `say`.** `say` plays on the machine this session runs on, so it only helps when the user is at that machine. When the system prompt or a system reminder says the user can follow this conversation from another device (Remote Control, or the session runs in the cloud), treat voice as off for `say`: keep writing the reply as a short spoken answer, so the phone app's own play button reads it well, but don't call `say`. Say this once, in one line, the first time it applies.

The user usually talks through `/voice` dictation, and dictation makes mistakes. Homophones, missing punctuation and spoken filler words are common. Work out what the user really means from the context, and don't treat a dictation error as the real question.

## How to reply while voice is on

Use tools as usual, for example to read code or run commands. After that, the reply is a single **spoken answer**. The screen and the speech use the same text, word for word:

1. First output the spoken answer, unchanged, as the text reply.
2. Then, at the end of the same reply, read the same text aloud with `say`, so the user can read along while listening:

   ```bash
   say -v Meijia -r 260 <<'EOF'
   (the same spoken answer)
   EOF
   ```

3. Stop the reply once it has been spoken.

Always feed the text to `say` through a heredoc, so quotes and symbols in it can't break the command.

Write the spoken answer for the ear, and show it on screen the same way:

- **Short.** Two to four sentences, answering only what was asked. When the answer really needs more, give the conclusion first, then ask whether the user wants the details.
- **Focused.** Don't bring up side topics, alternatives or caveats along the way.
- **Spoken.** Write complete sentences in Traditional Chinese, since the Meijia voice reads Taiwanese Mandarin. Write numbers and English abbreviations the way they are said aloud, for example 「零點一點三版」 for version 0.1.3. No Markdown, bullets, code, file paths or URLs. Say them in spoken form instead, for example 「專案根目錄的設定檔」 for a config file path.

When the answer only works if the user copies something exactly, such as a command, say in the spoken answer that it needs a command copied, and ask whether to switch back to text mode to paste it. Then wait for the answer.
