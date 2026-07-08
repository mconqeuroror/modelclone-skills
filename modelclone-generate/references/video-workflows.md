# Video workflows

## Motion control (SFW)

Best when you have a model still and a reference dance/action clip.

```bash
modelclone generate motion \
  --body '{
    "modelId":"<uuid>",
    "imageUrl":"https://cdn…/still.png",
    "videoUrl":"https://cdn…/reference.mp4",
    "duration":8,
    "prompt":"natural motion, cinematic"
  }' \
  --wait
```

Credits: `motionXPerSec` × duration (default **9.5**/s).

## Complete recreation

Returns **two** ids — image then video. Always `--wait` polls both.

```bash
modelclone generate complete-recreation \
  --body '{
    "modelId":"<uuid>",
    "modelIdentityImages":["https://…/p1.jpg","https://…/p2.jpg","https://…/p3.jpg"],
    "videoScreenshot":"https://…/frame.jpg",
    "originalVideoUrl":"https://…/source.mp4",
    "videoDuration":5
  }' \
  --wait
```

## Creator Studio video

For general text/image-to-video without model identity lock:

```bash
modelclone studio video \
  --body '{
    "prompt":"slow push-in on subject, golden hour",
    "videoModel":"seedance2",
    "aspectRatio":"9:16",
    "duration":8,
    "inputImageUrl":"https://…/first-frame.jpg"
  }' \
  --wait
```

Veo follow-ups: poll `engine` for `task:<id>`, then `studio 4k` / `studio extend` as needed.

## Pacing

- 6+ seconds between POST submits on one account
- Poll every 10–15s (`modelclone gen wait` default interval 5s is fine for CLI)
