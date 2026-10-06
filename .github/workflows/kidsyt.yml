name: Kids YouTube Automation

on:
  workflow_dispatch:
  schedule:
    - cron: "30 12 * * *"

jobs:
  create-video:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Install
        run: |
          sudo apt-get update
          sudo apt-get install -y ffmpeg
          pip install edge-tts pillow

      - name: Create video
        run: |
          cat > make_video.py <<'PY'
          import asyncio
          import edge_tts
          from PIL import Image, ImageDraw
          import subprocess

          story = """एक बार एक छोटा खरगोश जंगल में रहता था।
          वह बहुत मेहनती और ईमानदार था।
          एक दिन उसे रास्ते में एक चमकता हुआ सिक्का मिला।
          खरगोश ने सोचा कि यह किसी का होगा।
          उसने जंगल के सभी जानवरों से पूछा।
          आखिरकार एक बूढ़े कछुए ने बताया कि सिक्का उसका है।
          खरगोश ने सिक्का वापस कर दिया।
          कछुआ बहुत खुश हुआ और बोला कि ईमानदारी सबसे बड़ी अच्छाई है।
          सीख: हमेशा ईमानदार रहना चाहिए।"""

          async def voice():
              tts = edge_tts.Communicate(
                  story,
                  "hi-IN-SwaraNeural"
              )
              await tts.save("voice.mp3")

          asyncio.run(voice())

          img = Image.new("RGB", (1280, 720), "white")
          draw = ImageDraw.Draw(img)

          draw.text(
              (350, 300),
              "ईमानदार खरगोश",
              fill="black"
          )

          img.save("cover.png")

          subprocess.run([
              "ffmpeg", "-y",
              "-loop", "1",
              "-i", "cover.png",
              "-i", "voice.mp3",
              "-c:v", "libx264",
              "-tune", "stillimage",
              "-c:a", "aac",
              "-b:a", "128k",
              "-pix_fmt", "yuv420p",
              "-shortest",
              "kids-story.mp4"
          ], check=True)

          print("Video created successfully")
          PY

          python make_video.py

      - name: Save video
        uses: actions/upload-artifact@v4
        with:
          name: kids-story-video
          path: |
            kids-story.mp4
            cover.png
