# Setup FFmpeg

리뷰를 렌더링 하기 위해 ffmpeg를 설치해야 합니다. 멋진 johnvansickle 님이 ffmpeg를 빌드해서 인터넷에 공개해두었습니다.

```
sudo su
mkdir -p /usr/local/ffmpeg
cd /usr/local/ffmpeg
wget http://johnvansickle.com/ffmpeg/builds/ffmpeg-git-amd64-static.tar.xz
tar xpvf ffmpeg-git-amd64-static.tar.xz --strip 1
```

admin 페이지에서 ffmpeg, ffprobe 명령어의 위치를 설정합니다.

* /usr/local/ffmpeg/ffmpeg
* /usr/loca/ffmpeg/ffprobe
