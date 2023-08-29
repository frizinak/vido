# VIDO

Simple video / timeline editor.

- Each cut is stored in .vido-parts/part-<hash>.mov (lossless by default, but configurable)
  so editing can be done incrementally.
- Capture audio / video or both from the given cut.
- Apply ffmpeg filters.
- Multiple outputs (SECTION=<name>)

## Documentation / Usage

See the [bash script](https://github.com/frizinak/vido/blob/dev/vido) and / or `vido -h`

## Example


file: Timeline

```
# Apply a hald clut to all cuts.
CLUT='/my-luts/Kodak/Kodak Portra 800 3 +.png'

# Apply ffmpeg vidstabtransform to each cut.
STAB=smoothing=5:zoom=5

# Remove dead pixel from each clip
FILTER_BEFORE='delogo=x=2283:y=679:w=5:h=5'

# Define a section (optional).
SECTION=one

# This is a cut
va /hdd/RAW/Originals/0000313516032-DSCF7322.MOV __:05:00.000-__:05:05.000

# This is a cut with ffmpeg filters, we discard the audio and use the audio of the cut below.
# We also disable stabilization.
STAB=
v  /hdd/RAW/Originals/0000313516032-DSCF7322.MOV __:__:01.500-__:__:04.500 eq=brightness=-0.10:contrast=1.4:saturation=1.6:gamma=0.9,scale=w=-2:h=2160,pad=w=3840:h=2160:x=(ow-iw)/2
a  ./music.aac                                   __:04:00.000-__:04:03.000
```

```
$ vido
```
