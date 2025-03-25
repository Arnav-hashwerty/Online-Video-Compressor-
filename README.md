<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Free Video Compressor | Reduce Video Size Online</title>
    <meta name="description" content="Compress MP4, MOV, AVI videos online for free. Reduce video file size without losing quality. No registration needed.">
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            line-height: 1.6;
            color: #333;
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        .hero-section {
            text-align: center;
            padding: 50px 20px;
            background: linear-gradient(135deg, #6e8efb, #a777e3);
            color: white;
            border-radius: 10px;
            margin-bottom: 30px;
        }
        .upload-box {
            border: 2px dashed #fff;
            padding: 40px;
            margin: 30px auto;
            max-width: 600px;
            cursor: pointer;
            transition: all 0.3s;
        }
        .upload-box:hover {
            background-color: rgba(255,255,255,0.1);
        }
        .compression-options {
            margin: 20px auto;
            max-width: 500px;
            display: flex;
            gap: 10px;
            justify-content: center;
            align-items: center;
            flex-wrap: wrap;
        }
        select, button {
            padding: 10px 15px;
            border-radius: 5px;
            border: none;
        }
        button {
            background-color: #4CAF50;
            color: white;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #45a049;
        }
        .progress-container {
            max-width: 600px;
            margin: 30px auto;
        }
        .result-section {
            text-align: center;
            margin-top: 30px;
        }
        #compressedVideo {
            max-width: 100%;
            margin: 20px 0;
            border-radius: 5px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        @media (max-width: 768px) {
            .compression-options {
                flex-direction: column;
            }
        }
        /* Ad Container Styling */
        .ad-container {
            text-align: center;
            margin: 20px 0;
            padding: 10px;
            background: #f5f5f5;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <!-- Header Ad -->
    <div class="ad-container">
        <!-- Propeller Ads Header Code (Replace with yours) -->
        <script type="text/javascript" src="//propellerads.com/your-header-ad-code.js"></script>
    </div>

    <div class="hero-section">
        <h1>Compress Videos Without Losing Quality</h1>
        <p>Free online tool to reduce video file size</p>
        <div class="upload-box" id="dropZone">
            <input type="file" id="videoUpload" accept="video/*">
            <p>Drag & drop your video here or click to browse</p>
        </div>
        <div class="compression-options">
            <label>Quality:</label>
            <select id="quality">
                <option value="0.7">High (70%)</option>
                <option value="0.5" selected>Medium (50%)</option>
                <option value="0.3">Low (30%)</option>
            </select>
            <button id="compressBtn" class="btn btn-primary">Compress Now</button>
        </div>
    </div>
    <div class="progress-container" id="progressContainer" style="display:none;">
        <div class="progress">
            <div id="progressBar" class="progress-bar" style="width: 0%"></div>
        </div>
        <p id="status">Processing...</p>
    </div>
    <div class="result-section" id="resultSection" style="display:none;">
        <h2>Your Compressed Video</h2>
        <video id="compressedVideo" controls></video>
        <a id="downloadBtn" class="btn btn-success">Download</a>
    </div>

    <!-- Sidebar Ad -->
    <div class="ad-container">
        <!-- Propeller Ads Sidebar Code (Replace with yours) -->
        <script type="text/javascript" src="//propellerads.com/your-sidebar-ad-code.js"></script>
    </div>

    <!-- Popunder Ad (Bottom of Page) -->
    <script type="text/javascript" src="//propellerads.com/your-popunder-code.js"></script>

    <!-- FFmpeg & JS -->
    <script src="https://cdn.jsdelivr.net/npm/@ffmpeg/ffmpeg@0.11.6/dist/ffmpeg.min.js"></script>
    <script>
        const { createFFmpeg, fetchFile } = FFmpeg;
        const ffmpeg = createFFmpeg({ log: true });

        document.getElementById('compressBtn').addEventListener('click', async () => {
            const videoFile = document.getElementById('videoUpload').files[0];
            if (!videoFile) {
                alert('Please select a video file first');
                return;
            }

            const quality = parseFloat(document.getElementById('quality').value);
            
            // Show progress
            const progressContainer = document.getElementById('progressContainer');
            const progressBar = document.getElementById('progressBar');
            const status = document.getElementById('status');
            progressContainer.style.display = 'block';
            
            status.textContent = 'Loading FFmpeg...';
            if (!ffmpeg.isLoaded()) {
                await ffmpeg.load();
            }
            
            status.textContent = 'Reading video file...';
            ffmpeg.FS('writeFile', 'input.mp4', await fetchFile(videoFile));
            
            status.textContent = 'Compressing video...';
            ffmpeg.setProgress(({ ratio }) => {
                progressBar.style.width = `${Math.round(ratio * 100)}%`;
            });
            
            await ffmpeg.run('-i', 'input.mp4', '-vcodec', 'libx264', '-crf', (30 * (1 - quality)).toString(), 'output.mp4');
            
            status.textContent = 'Processing complete!';
            const data = ffmpeg.FS('readFile', 'output.mp4');
            
            const videoUrl = URL.createObjectURL(new Blob([data.buffer], { type: 'video/mp4' }));
            const videoElement = document.getElementById('compressedVideo');
            const downloadBtn = document.getElementById('downloadBtn');
            const resultSection = document.getElementById('resultSection');
            
            videoElement.src = videoUrl;
            downloadBtn.href = videoUrl;
            downloadBtn.download = `compressed_${videoFile.name}`;
            resultSection.style.display = 'block';
            
            // Scroll to result
            resultSection.scrollIntoView({ behavior: 'smooth' });
        });

        // Drag and drop functionality
        const dropZone = document.getElementById('dropZone');
        dropZone.addEventListener('dragover', (e) => {
            e.preventDefault();
            dropZone.style.backgroundColor = 'rgba(255,255,255,0.2)';
        });

        dropZone.addEventListener('dragleave', () => {
            dropZone.style.backgroundColor = '';
        });

        dropZone.addEventListener('drop', (e) => {
            e.preventDefault();
            dropZone.style.backgroundColor = '';
            if (e.dataTransfer.files.length) {
                document.getElementById('videoUpload').files = e.dataTransfer.files;
            }
        });
    </script>
</body>
</html>
