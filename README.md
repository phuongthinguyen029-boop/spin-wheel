
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vòng Quay Nhiệm Vụ Giáng Sinh</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            text-align: center;
            padding: 20px;
            background: linear-gradient(to bottom, #ffffff, #e6f7ff); /* Nền trắng chuyển xanh nhạt */
            color: #d32f2f; /* Màu đỏ Giáng sinh */
        }
        h1 {
            color: #d32f2f;
            text-shadow: 2px 2px 4px #ffffff;
        }
        #wheel {
            margin: 20px auto;
            width: 300px;
            height: 300px;
            border: 5px solid #d32f2f; /* Viền đỏ */
            border-radius: 50%;
            position: relative;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.3);
        }
        #pointer {
            position: absolute;
            bottom: -15px; /* Di chuyển xuống dưới */
            left: 50%;
            transform: translateX(-50%);
            width: 0;
            height: 0;
            border-left: 15px solid transparent;
            border-right: 15px solid transparent;
            border-top: 30px solid #ffa500; /* Mũi tên cam */
        }
        button {
            padding: 15px 30px;
            font-size: 18px;
            background: #d32f2f; /* Nút đỏ */
            color: white;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2), 0 0 15px rgba(255, 255, 255, 0.8); /* Vệt tuyết trắng */
            transition: background 0.3s, box-shadow 0.3s;
            margin: 10px;
        }
        button:hover:not(:disabled) {
            background: #b71c1c; /* Đỏ đậm hơn khi hover */
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3), 0 0 20px rgba(255, 255, 255, 1); /* Vệt tuyết mạnh hơn khi hover */
        }
        button:disabled {
            background: #ccc; /* Màu xám khi disabled */
            cursor: not-allowed;
            box-shadow: none;
        }
        #result {
            margin-top: 20px;
            font-size: 20px;
            font-weight: bold;
            color: #388e3c; /* Màu xanh lá */
        }
        #actionBtn {
            display: none; /* Ẩn nút ban đầu */
        }
        /* Thêm hiệu ứng bông tuyết đơn giản */
        .snowflake {
            position: absolute;
            color: #ffffff;
            font-size: 20px;
            animation: fall 10s linear infinite;
        }
        @keyframes fall {
            to { transform: translateY(100vh); }
        }
    </style>
</head>
<body>
    <h1>🎄 Vòng Quay Nhiệm Vụ Giáng Sinh 🎅</h1>
    <p>Quay để nhận nhiệm vụ của bạn!!</p>
    
    <div id="wheel">
        <div id="pointer">▲</div>  <!-- Mũi tên chỉ lên từ dưới -->
    </div>
    <button id="spinBtn" onclick="spinWheel()">Quay Ngay!</button>
    <div id="result"></div>
    <button id="actionBtn" onclick="performAction()">Thực Hiện Ngay</button>
    
    <!-- Hiệu ứng bông tuyết (tùy chọn) -->
    <div class="snowflake" style="left: 10%; animation-delay: 0s;">❄</div>
    <div class="snowflake" style="left: 20%; animation-delay: 2s;">❄</div>
    <div class="snowflake" style="left: 30%; animation-delay: 4s;">❄</div>
    <div class="snowflake" style="left: 40%; animation-delay: 6s;">❄</div>
    <div class="snowflake" style="left: 50%; animation-delay: 8s;">❄</div>
    
    <script>
        // Danh sách số trên vòng quay (hiển thị Số 1 đến Số 10)
        const segments = [
            "Số 1",
            "Số 2",
            "Số 3",
            "Số 4",
            "Số 5",
            "Số 6",
            "Số 7",
            "Số 8",
            "Số 9",
            "Số 10"
        ];
        
        // Danh sách nhiệm vụ tương ứng với từng số (ẩn, chỉ hiện khi quay trúng)
        const tasks = [
            "cmt 3 bài hát giáng sinh yêu thích",
            "cmt tag tên 3 người bạn của bạn",
            "cmt ba phụ kiện của ông già noel",
            "cmt một câu hát chủ đề giáng sinh mà bạn yêu thích",
            "truyền thống trang trí cây thông noel bắt nguồn từ quốc gia nào?",
            "cmt một đồ vật trang trí vào dịp lễ giáng sinh",
            "sáng tác một câu thơ bốn từ với chủ đề giáng sinh",
            "theo truyền thuyết ông già noel sẽ bỏ quà vào đâu?",
            "cmt \" vui đón Giáng sinh cùng cec\"",
            "cmt một con vật găn liền với mùa giáng sinh"
        ];
        
        // Màu sắc Giáng sinh cho các phần (đỏ, xanh lá, vàng xen kẽ)
        const colors = ['#d32f2f', '#388e3c', '#ffd700', '#d32f2f', '#388e3c', '#ffd700', '#d32f2f', '#388e3c', '#ffd700', '#d32f2f'];
        
        // Vẽ vòng quay bằng canvas
        const canvas = document.createElement('canvas');
        canvas.width = 300;
        canvas.height = 300;
        const ctx = canvas.getContext('2d');
        const centerX = canvas.width / 2;
        const centerY = canvas.height / 2;
        const radius = 140;
        const anglePerSegment = (2 * Math.PI) / segments.length;
        
        function drawWheel() {
            for (let i = 0; i < segments.length; i++) {
                const startAngle = i * anglePerSegment;
                const endAngle = (i + 1) * anglePerSegment;
                
                // Vẽ phần với màu Giáng sinh
                ctx.beginPath();
                ctx.moveTo(centerX, centerY);
                ctx.arc(centerX, centerY, radius, startAngle, endAngle);
                ctx.closePath();
                ctx.fillStyle = colors[i];
                ctx.fill();
                ctx.strokeStyle = '#ffffff'; // Viền trắng
                ctx.lineWidth = 2;
                ctx.stroke();
                
                // Vẽ văn bản (chỉ hiển thị số)
                const textAngle = startAngle + anglePerSegment / 2;
                const textX = centerX + Math.cos(textAngle) * (radius - 40);
                const textY = centerY + Math.sin(textAngle) * (radius - 40);
                ctx.fillStyle = '#ffffff'; // Chữ trắng
                ctx.font = 'bold 14px Arial';
                ctx.textAlign = 'center';
                ctx.fillText(segments[i], textX, textY);
            }
        }
        
        drawWheel();
        document.getElementById('wheel').appendChild(canvas);
        
        // Hàm quay vòng
        function spinWheel() {
            const spinAngle = Math.random() * 360 + 720; // Quay ít nhất 2 vòng
            canvas.style.transition = 'transform 3s ease-out';
            canvas.style.transform = `rotate(${spinAngle}deg)`;
            
            setTimeout(() => {
                const finalAngle = spinAngle % 360;
                // Thêm offset 180 độ để khớp với vị trí mũi tên (ở dưới)
                const adjustedAngle = (finalAngle + 180) % 360;
                const segmentIndex = Math.floor(adjustedAngle / (360 / segments.length));
                // Hiển thị nhiệm vụ tương ứng với số trúng
                document.getElementById('result').textContent = `🎉 Chúc mừng! Bạn trúng ${segments[segmentIndex]}. Nhiệm vụ của bạn: ${tasks[segmentIndex]} 🎄`;
                // Hiển thị nút "Thực Hiện Ngay"
                document.getElementById('actionBtn').style.display = 'inline-block';
                // Vô hiệu hóa nút quay để không quay lại
                const spinBtn = document.getElementById('spinBtn');
                spinBtn.disabled = true;
                spinBtn.textContent = 'Đã Quay'; // Thay đổi text nút
                canvas.style.transition = 'none'; // Reset transition
            }, 3000); // Thời gian quay 5 giây
        }
        
        // Hàm thực hiện hành động (đóng trang)
        function performAction() {
            window.close(); // Thoát
        }
    </script>
</body>
</html>

