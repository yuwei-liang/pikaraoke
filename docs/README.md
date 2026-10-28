# PiKaraoke, KTV edition

<img width="588" height="339" alt="Image" src="https://i.ibb.co/Z6MTM4wt/pikaraoke-readme.png" />

Sing to any music video, KTV style. This is a fork of [PiKaraoke](https://github.com/vicwomg/pikaraoke) by vicwomg that adds:

- **AI vocal removal.** Queue a song, and its lead vocal is taken out in the background before its turn. Backing vocals are kept by default; if the singer can still be heard, the song is redone with every voice removed. An Original/Instrumental switch is in the player controls.
- **Synced lyrics.** Lyrics are fetched from [LRCLIB](https://lrclib.net), lined up with the singing, and shown KTV style on videos that don't have their own. Lyrics nobody has timed are timed by recognizing the sung words. Buttons in the player controls move lyrics that run early or late.
- **Queue status.** The queue shows what each song has ready, what is still being prepared and how long it will take, and lets the next song go first.

Everything else is PiKaraoke: guests scan a QR code to search YouTube and your own songs, queue them from their phones, and change the key to fit their voice.

The official PiKaraoke chose not to include these features because they need a fast computer. If you have a Raspberry Pi or an older machine, use the [official PiKaraoke](https://github.com/vicwomg/pikaraoke) instead, and if it makes your parties better, [buy its author a coffee](https://www.buymeacoffee.com/vicwomg).

## 中文简介

PiKaraoke 的 KTV 版：任何 YouTube 音乐视频都能当伴奏唱。点歌后自动用 AI 去掉原唱（默认保留和声），自动下载并对齐同步歌词，队列里显示每首歌的准备进度。需要 Apple Silicon 的 Mac 或较新的电脑，安装方法见下面的 [Install](#install)。

## What you need

- An Apple Silicon Mac, or a modern Windows or Linux PC. Intel Macs and Raspberry Pis can't run vocal removal.
- About 5GB of disk for the AI models and PyTorch, downloaded on first use.
- A modern web browser (Chrome, Edge or Safari) for the player screen.

On an Apple Silicon Mac, a 4-minute song takes about 4 minutes to have its vocals removed, and about 9 more when every voice has to come out. Songs are prepared while earlier ones are sung, so after the first few there is usually no wait.

## Install

1. Install [uv](https://docs.astral.sh/uv/getting-started/installation/), FFmpeg and Deno. On a Mac with [Homebrew](https://brew.sh):

   ```sh
   brew install uv ffmpeg deno
   ```

   On Windows and Linux, get [FFmpeg](https://ffmpeg.org/download.html) and [Deno](https://deno.com/) from their sites.

2. Install this fork:

   ```sh
   uv tool install "pikaraoke[vocal-removal] @ git+https://github.com/yuwei-liang/pikaraoke"
   ```

   Leave out `[vocal-removal]` for synced lyrics only, without the 2GB PyTorch download. If you already have the official PiKaraoke, this replaces it, and your songs and settings are kept.

3. Run `pikaraoke`, open Settings, and turn on "Remove vocals from queued songs" and "Add synced lyrics to queued songs".

To update to the latest version, run the install command again with `--reinstall`:

```sh
uv tool install --reinstall "pikaraoke[vocal-removal] @ git+https://github.com/yuwei-liang/pikaraoke"
```

To go back to the official PiKaraoke, run `uv tool uninstall pikaraoke`, then `uv tool install pikaraoke`.

## Usage

```sh
pikaraoke
```

This opens the player in your default browser. Put it on the TV, and guests scan the QR code to queue songs. Use `pikaraoke --headless` to run it as a background server and open the player in any browser yourself. `pikaraoke --help` lists the other options.

In Settings:

- "Keep backing vocals" is on by default. Turn it off to remove every voice from every song; it takes about twice as long.
- If you can still hear the singer on a song, "Remove all vocals" under the vocals switch redoes it with every voice out, from its next play.

Lyrics timed by this fork are marked as possibly off in the player controls. Lyrics are only added to videos that don't show their own.

## How it works

- Vocal removal runs [audio-separator](https://github.com/nomadkaraoke/python-audio-separator) with the Mel-Band RoFormer karaoke model (900MB), which keeps backing vocals. Each result is checked by speech recognition for lyrics still being sung; a song that fails is separated again with BS-RoFormer (600MB), which takes out every voice.
- Lyrics come from LRCLIB. Lyrics timed to a recording of the video's length are used as they are; others are lined up against the separated vocal. Untimed lyrics are timed with [faster-whisper](https://github.com/SYSTRAN/faster-whisper) (1.6GB model).
- Prepared tracks and lyrics are saved next to each song, so each song is only prepared once.

## Screenshots

<div style="display: flex; flex-wrap: wrap;">
<img width="250" alt="pikaraoke-nowplaying" src="images/screenshot-now-playing.png">
<img width="250" alt="pikaraoke-queue" src="images/screenshot-queue.png">
<img width="250" alt="pikaraoke-browse" src="images/screenshot-songs-all.png">
<img width="250" alt="pikaraoke-search" src="images/screenshot-songs-search.png">
<img width="250" alt="pikaraoke-add-new" src="images/screenshot-add-new.png">
<img width="250" alt="pikaraoke-play-history" src="images/screenshot-play-history.png">
</div>

## Help and contributing

For problems with the PiKaraoke basics, see the official [troubleshooting wiki](https://github.com/vicwomg/pikaraoke/wiki/Troubleshooting). Vocal removal and lyrics are only in this fork, so don't report their problems to the official project.

To work on the code, see the [development guide](development.md).

## Credits

PiKaraoke is created and maintained by [vicwomg](https://github.com/vicwomg) and its contributors. This fork follows its updates.
