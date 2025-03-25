## Utilities

### Concatenate multiple files with ffmpeg (same format)
- install basics (python, pip, etc)
- Install FFmpeg (apt install ffmpeg)
- Generate the list of multimedia files you want to concatenate (ls *.mp3 | sed "s/^/file '/;s/$/'/" > file_list.txt )
- Merge all media files in the file_list.txt (ffmpeg -f concat -safe 0 -i file_list.txt -c copy output.mp3)
- 
### Generate srt files for the multimedia file using open-ai whisper
- Install FFmpeg (apt install ffmpeg)
- Install Whisper (pip install openai-whisper)
- Generate srt file (whisper audio.mp3 --language English --output_format srt)

### Next
