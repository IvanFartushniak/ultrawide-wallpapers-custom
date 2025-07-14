<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>21:9 Image Converter</title>
  <style>
    body {
      font-family: sans-serif;
      background-color: #111;
      color: #fff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
    }
    #upload-container {
      border: 2px dashed #888;
      padding: 20px;
      text-align: center;
      cursor: pointer;
      margin-bottom: 20px;
    }
    canvas {
      max-width: 100%;
      margin-top: 20px;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      margin-top: 10px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>Convert Image to 21:9 with Blur</h1>
  <div id="upload-container">Click or Drop Image Here
    <input type="file" id="upload" accept="image/*" style="display: none" />
  </div>
  <canvas id="canvas" width="3440" height="1440"></canvas>
  <button id="download">Download</button>

  <script>
    const uploadContainer = document.getElementById('upload-container');
    const uploadInput = document.getElementById('upload');
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    const downloadBtn = document.getElementById('download');

    uploadContainer.addEventListener('click', () => uploadInput.click());
    uploadContainer.addEventListener('dragover', (e) => {
      e.preventDefault();
      uploadContainer.style.borderColor = '#0f0';
    });
    uploadContainer.addEventListener('dragleave', () => {
      uploadContainer.style.borderColor = '#888';
    });
    uploadContainer.addEventListener('drop', (e) => {
      e.preventDefault();
      uploadInput.files = e.dataTransfer.files;
      handleFile(uploadInput.files[0]);
    });
    uploadInput.addEventListener('change', () => {
      if (uploadInput.files.length) handleFile(uploadInput.files[0]);
    });

    function handleFile(file) {
      const reader = new FileReader();
      reader.onload = function (e) {
        const img = new Image();
        img.onload = function () {
          drawBlurred(img);
        };
        img.src = e.target.result;
      };
      reader.readAsDataURL(file);
    }

    function drawBlurred(img) {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw blurred background
      ctx.filter = 'blur(50px)';
      const scaleBG = Math.max(canvas.width / img.width, canvas.height / img.height);
      ctx.drawImage(
        img,
        0, 0, img.width, img.height,
        (canvas.width - img.width * scaleBG) / 2,
        (canvas.height - img.height * scaleBG) / 2,
        img.width * scaleBG,
        img.height * scaleBG
      );

      // Draw main image (centered)
      ctx.filter = 'none';
      const scaleMain = Math.min(canvas.width / img.width, canvas.height / img.height);
      const mainWidth = img.width * scaleMain;
      const mainHeight = img.height * scaleMain;
      ctx.drawImage(
        img,
        (canvas.width - mainWidth) / 2,
        (canvas.height - mainHeight) / 2,
        mainWidth,
        mainHeight
      );
    }

    downloadBtn.addEventListener('click', () => {
      const link = document.createElement('a');
      link.download = 'converted_21-9.jpg';
      link.href = canvas.toDataURL('image/jpeg');
      link.click();
    });
  </script>
</body>
</html>
