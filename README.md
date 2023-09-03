# VIDO

Simple video / timeline editor.

- Each proxy is stored in .vido-parts/part-&lt;hash&gt;.mov (lossless by default, but configurable)
  so editing can be done incrementally.
- Capture audio / video or both from the given cut.
- Apply ffmpeg filters.
- Multiple outputs (SECTION=&lt;name&gt;)
- ...

## Documentation / Usage

See the [bash script](https://github.com/frizinak/vido/blob/dev/vido) and / or `vido -h`

`vido` parses a `Timeline` file that contains bash assignments and cut defintions.

###  Assignments

`FILTER_V_BEFORE='eq=contrast=1.05'`

or

`PROXY_CODEC_PARAMS=("${codec_v_h265[@]}" "${codec_a_pcm[@]}")`


### Cuts:

(Both lines are equivalent)

```
   /my-videos/a-video.mov 00:00:00.000-00:00:00.000    scale=640:480
VA /my-videos/a-video.mov __:__:__.___-__:__:__.___ V: scale=640:480 A: anull
~~ ~~~~~~~~~~~~~~~~~~~~~  ~~~~~~~~~~~~~~~~~~~~~~~~~ ~~~~~~~~~~~~~~~~~~~~~~~~~
│        │                   │                       │
│        │                   │                       │
│        │                   │                       └ ffmpeg filters to apply
│        │                   │
│        │                   └ the start and end timestamp (HH:MM:SS.mmm-HH:MM:SS.mmm)
│        │
│        │
│        └ the file path
│          if this starts with $CLIP_ a variable with the same name
│          will be resolved in its stead.
│
└ the streams to extract (video and/or audio)

```

## Config:

Interpreted as a bash script.

`XDG_CONFIG_HOME/vido/defaults`

## Example

file: Timeline

```
# Apply a hald clut to all cuts.
CLUT='/my-luts/Kodak/Kodak Portra 800 3 +.png'

# Apply ffmpeg vidstabtransform to each cut.
STAB=smoothing=5:zoom=5

# Remove dead pixel from each cut
FILTER_BEFORE='delogo=x=2283:y=679:w=5:h=5'

# Define a section (optional).
SECTION=one

# This is a cut
VA /hdd/RAW/Originals/0000313516032-DSCF7322.MOV __:05:00.000-__:05:05.000

# This is a cut using a variable
CLIP_ONE='/hdd/RAW/Originals/0000313516032-DSCF7322.MOV'
VA $CLIP_ONE __:05:00.000-__:05:05.000

# This is a cut with ffmpeg filters, we discard the audio and use the audio of the cut below.
# We also disable stabilization.
STAB=
V  /hdd/RAW/Originals/0000313516032-DSCF7322.MOV __:__:01.500-__:__:04.500 eq=brightness=-0.10:contrast=1.4:saturation=1.6:gamma=0.9,scale=w=-2:h=2160,pad=w=3840:h=2160:x=(ow-iw)/2
A  ./music.aac                                   __:04:00.000-__:04:03.000
```

```
$ vido > final_encode.sh && \
  chmod +x final_encode.sh && \
  ./final_encode.sh
```
