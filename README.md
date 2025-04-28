<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Reproductor en Vivo</title>
  <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
  <style>
    body {
      background: #000;
      margin: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100vh;
    }
    video {
      width: 80%;
      max-width: 900px;
      border: 4px solid #fff;
      border-radius: 12px;
    }
  </style>
</head>
<body>
  <video id="video" controls autoplay></video>

  <script>
    const video = document.getElementById('video');
const src = "https://shlsakamai4.akamaized.net/hlsorigin/philadelphia_hd_fm_2200/chunklist.m3u8?stream=gulfstream_mbr&cust=TVG&user=&t=1745856172&h=1a7ae109f62686506f5bc0aa4221f986&type=live";

    if (Hls.isSupported()) {
      const hls = new Hls();
      hls.loadSource(videoSrc);
      hls.attachMedia(video);
      hls.on(Hls.Events.MANIFEST_PARSED, function () {
        video.play();
      });
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video.src = videoSrc;
      video.addEventListener('loadedmetadata', function () {
        video.play();
      });
    } else {
      alert('Tu navegador no soporta reproducción HLS.');
    }
  </script>
</body>
</html>
