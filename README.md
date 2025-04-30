<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Player</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      background: #000;
      height: 100%;
      width: 100%;
      overflow: hidden;
    }

    #video {
      position: absolute;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      object-fit: cover;
      background-color: #000;
      cursor: none;
    }

    #loading {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      border: 8px solid rgba(255, 255, 255, 0.2);
      border-top: 8px solid #fff;
      border-radius: 50%;
      width: 60px;
      height: 60px;
      animation: spin 1s linear infinite;
      z-index: 10;
    }

    @keyframes spin {
      0% { transform: translate(-50%, -50%) rotate(0deg); }
      100% { transform: translate(-50%, -50%) rotate(360deg); }
    }
  </style>
</head>
<body>
  <div id="loading"></div>
  <video id="video" autoplay muted playsinline></video>

  <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
  <script>
    const video = document.getElementById('video');
    const loading = document.getElementById('loading');
    const src = "https://shlsakamai4.akamaized.net/hlsorigin/capitalotb_hd_fm_2200/chunklist.m3u8?stream=philadelphia_mbr&cust=TVG&user=&t=1746016125&h=f1aec7a91180c7a28a62278fe860a741&type=live";

    function showLoading() {
      loading.style.display = 'block';
    }

    function hideLoading() {
      loading.style.display = 'none';
    }

    showLoading(); // Mostrar al inicio

    if (Hls.isSupported()) {
      const hls = new Hls();
      hls.loadSource(src);
      hls.attachMedia(video);
      hls.on(Hls.Events.MANIFEST_PARSED, () => {
        video.play();
      });
      hls.on(Hls.Events.BUFFERING, showLoading);
      hls.on(Hls.Events.BUFFER_APPENDED, hideLoading);
      hls.on(Hls.Events.ERROR, function (event, data) {
        console.warn('Error de HLS', data);
        showLoading(); // Si hay error, mostrar carga
      });
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video.src = src;
      video.addEventListener('loadedmetadata', () => {
        video.play();
        hideLoading();
      });
      video.addEventListener('waiting', showLoading);
      video.addEventListener('playing', hideLoading);
      video.addEventListener('error', showLoading); // En caso de error en iOS
    } else {
      showLoading(); // No compatible
      console.warn('Este navegador no soporta HLS');
    }

    // Pantalla completa al tocar
    video.addEventListener('click', () => {
      if (video.requestFullscreen) {
        video.requestFullscreen();
      } else if (video.webkitRequestFullscreen) {
        video.webkitRequestFullscreen();
      } else if (video.msRequestFullscreen) {
        video.msRequestFullscreen();
      }
    });
  </script>
</body>
</html>
