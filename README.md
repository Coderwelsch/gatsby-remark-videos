# ⚠️ INFO

I forked this repo to solve the gatsby error “couldn’t find plugin gatsby-remark-videos” by moving the built index.js to the root folder of the repo. In version 0.3.6 I’ve also added url options for the generated video tags.

# gatsby-remark-videos

The intent of this plugin is to aid in the embedding of looping 'html5 gifv'
like videos from markdown.

## Install

```bash
npm install --save https://github.com/Coderwelsch/gatsby-remark-videos.git gatsby-plugin-ffmpeg
```
or
```bash
yarn install https://github.com/Coderwelsch/gatsby-remark-videos.git
```

This package is dependent on gatsby-plugin-ffmpeg which has the requirement of ffmpeg installed. Please follow the instructions at https://github.com/Mike-Dax/gatsby-plugin-ffmpeg to install the required dependencies.

## Usage

The order of the pipelines will influence the final order in the `<video />`
tag.

Currently it only detects files with the extensions `avi`, `mp4`, `mov`, `mkv`. If you have a different container and would like it added, open an issue or create a PR and I'm happy to include it.

### Configuration

Make sure this plugin comes before `gatsby-remark-images` otherwise it might complain about unknown image file formats.

```javascript
// In your gatsby-config.js
plugins: [
  ...
  {
    resolve: `gatsby-remark-videos`,
    options: {
      pipelines: [
        {
          name: 'vp9',
          transcode: chain =>
            chain
              .videoCodec('libvpx-vp9')
              .noAudio()
              .outputOptions(['-crf 20', '-b:v 0']),
          maxHeight: 480,
          maxWidth: 900,
          fileExtension: 'webm',
        },
        {
          name: 'h264',
          transcode: chain =>
            chain
              .videoCodec('libx264')
              .noAudio()
              .addOption('-profile:v', 'main')
              .addOption('-pix_fmt', 'yuv420p')
              .outputOptions(['-movflags faststart'])
              .videoBitrate('1000k'),
          maxHeight: 480,
          maxWidth: 900,
          fileExtension: 'mp4',
        },
      ],
    }
  },
  ...
]
```

Also make sure you have a plugin that copies the files you are referencing, for example `gatsby-remark-copy-linked-files`.

```
...
{
  resolve: `gatsby-remark-copy-linked-files`,
  options: {},
},
...
```

### Markdown Syntax

Markdown image syntax is used:

```
Video one:
![](video.avi)
```

Creates roughly this:

```html
<video preload>
  <source src="/static/video-hash-optshash.webm" type="video/webm" />
  <source src="/static/video-hash-optshash.mp4" type="video/mp4" />
</video>
```

### Video Url Options

You now can use url parameters to set custom video attributes like loop, muted, autoplay etc:

```
Video one:
![](video.avi?autoplay=true&muted=true&loop=true)
```

Creates this:

```html
<video autoplay="true" muted="true" loop="true">
  <source src="/static/video-hash-optshash.webm" type="video/webm" />
  <source src="/static/video-hash-optshash.mp4" type="video/mp4" />
</video>
```
