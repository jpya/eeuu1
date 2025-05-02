<!DOCTYPE html> 
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
  <title>Player</title>
  <style>
    * {
      box-sizing: border-box;
    }

    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      width: 100%;
      background-color: #000;
      overflow: hidden;
    }

    #video {
      display: block;
      width: 100%;
      height: 100%;
      object-fit: cover;
      background-color: #000;
    }

    /* En fullscreen horizontal, mostrar video completo sin recorte */
    body.fullscreen-landscape #video {
      object-fit: contain !important;
      background-color: black;
    }

    #loading {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      border: 6px solid rgba(255, 255, 255, 0.2);
      border-top: 6px solid #fff;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      animation: spin 1s linear infinite;
      z-index: 10;
    }

    @keyframes spin {
      0% { transform: translate(-50%, -50%) rotate(0deg); }
      100% { transform: translate(-50%, -50%) rotate(360deg); }
    }

    #controls {
      position: absolute;
      bottom: 8px;
      right: 10px;
      display: flex;
      flex-direction: row;
      gap: 8px;
      z-index: 20;
      opacity: 1;
      transition: opacity 0.3s ease;
    }

    .control-btn {
      background: rgba(0, 0, 0, 0.85);
      border: none;
      color: white;
      padding: 6px 10px;
      font-size: 14px;
      border-radius: 4px;
      cursor: pointer;
      transition: background 0.2s ease;
    }

    .control-btn:hover {
      background: rgba(255, 255, 255, 0.2);
    }

    body.inactive #controls {
      opacity: 0;
      pointer-events: none;
    }

    body.inactive {
      cursor: none;
    }
  </style>
</head>
<body>
  <div id="loading"></div>
  <video id="video" autoplay muted playsinline></video>

  <div id="controls">
    <button id="audioBtn" class="control-btn">🔇</button>
    <button id="fullscreenBtn" class="control-btn">⛶</button>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
  <script>
    const video = document.getElementById('video');
    const loading = document.getElementById('loading');
    const audioBtn = document.getElementById('audioBtn');
    const fullscreenBtn = document.getElementById('fullscreenBtn');
 
const src = "https://shlsakamai4.akamaized.net/hlsorigin/capitalotb_hd_fm_2200/chunklist.m3u8?stream=gulfstream_mbr&cust=TVG&user=&t=1746187337&h=7988b66b96ca4884f3ab237e2840e610&type=live";

    function showLoading() {
      loading.style.display = 'block';
    }

    function hideLoading() {
      loading.style.display = 'none';
    }

    showLoading();

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
        showLoading();
      });
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video.src = src;
      video.addEventListener('loadedmetadata', () => {
        video.play();
        hideLoading();
      });
      video.addEventListener('waiting', showLoading);
      video.addEventListener('playing', hideLoading);
      video.addEventListener('error', showLoading);
    } else {
      showLoading();
      console.warn('Este navegador no soporta HLS');
    }

    // Pantalla completa
    fullscreenBtn.addEventListener('click', () => {
      if (!document.fullscreenElement) {
        if (video.requestFullscreen) video.requestFullscreen();
        else if (video.webkitRequestFullscreen) video.webkitRequestFullscreen();
        else if (video.msRequestFullscreen) video.msRequestFullscreen();
      } else {
        document.exitFullscreen();
      }
    });

    // Audio On/Off
    audioBtn.addEventListener('click', () => {
      video.muted = !video.muted;
      audioBtn.textContent = video.muted ? '🔇' : '🔊';
    });

    // Ocultar controles y cursor tras 3 segundos
    let hideTimeout;
    function resetInactivityTimer() {
      document.body.classList.remove('inactive');
      clearTimeout(hideTimeout);
      hideTimeout = setTimeout(() => {
        document.body.classList.add('inactive');
      }, 3000);
    }
    document.addEventListener('mousemove', resetInactivityTimer);
    resetInactivityTimer();

    // Detectar orientación + fullscreen
    function updateVideoFit() {
      const isFullscreen = !!document.fullscreenElement;
      const isLandscape = window.innerWidth > window.innerHeight;

      if (isFullscreen && isLandscape) {
        document.body.classList.add('fullscreen-landscape');
      } else {
        document.body.classList.remove('fullscreen-landscape');
      }
    }
    document.addEventListener('fullscreenchange', updateVideoFit);
    window.addEventListener('resize', updateVideoFit);
  </script>
</body>
</html>
