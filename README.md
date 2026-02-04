<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>For Kimia</title>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Great+Vibes&family=Montserrat:wght@300;400;600&display=swap" rel="stylesheet">
    <!-- Three.js Library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

    <style>
         :root {
            --primary: #ff6b81;
            --glass: rgba(255, 255, 255, 0.1);
            --glass-border: rgba(255, 255, 255, 0.3);
            --text: #ffffff;
        }
        
        body {
            margin: 0;
            overflow: hidden;
            font-family: 'Montserrat', sans-serif;
            background: #050505;
            color: var(--text);
        }
        /* 3D Canvas */
        
        #canvas-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }
        /* Instructions Text (Click the Heart) */
        
        .instruction {
            position: absolute;
            bottom: 10%;
            left: 50%;
            transform: translateX(-50%);
            z-index: 5;
            font-size: 1.2rem;
            color: rgba(255, 255, 255, 0.8);
            pointer-events: none;
            /* Let clicks pass through to the 3D scene */
            animation: pulseText 2s infinite;
        }
        
        @keyframes pulseText {
            0%,
            100% {
                opacity: 0.5;
            }
            50% {
                opacity: 1;
            }
        }
        /* The Question Card (Initially Hidden) */
        
        .main-wrapper {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 90%;
            max-width: 500px;
            z-index: 10;
            perspective: 1000px;
            display: none;
            /* Hidden by default */
            opacity: 0;
            transition: opacity 1s ease;
        }
        
        .card {
            background: var(--glass);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border: 1px solid var(--glass-border);
            border-radius: 20px;
            padding: 40px 30px;
            text-align: center;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.5);
            position: relative;
            min-height: 400px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        
        h1 {
            font-family: 'Great Vibes', cursive;
            font-size: 3.5rem;
            margin-bottom: 10px;
            text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
            color: #ffd700;
        }
        
        p.subtitle {
            font-size: 0.9rem;
            letter-spacing: 2px;
            text-transform: uppercase;
            margin-bottom: 20px;
            opacity: 0.8;
        }
        
        .love-letter {
            font-size: 1rem;
            line-height: 1.6;
            margin-bottom: 20px;
            font-weight: 300;
        }
        
        .question-box {
            margin-top: 10px;
            font-size: 1.2rem;
            font-weight: 600;
            margin-bottom: 30px;
            padding: 15px;
            background: rgba(255, 107, 129, 0.1);
            border-radius: 10px;
            border: 1px solid rgba(255, 107, 129, 0.3);
            width: 100%;
        }
        
        .btn-group {
            position: relative;
            width: 100%;
            height: 60px;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        .btn {
            padding: 12px 40px;
            border: none;
            border-radius: 50px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
            font-family: 'Montserrat', sans-serif;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            position: relative;
            z-index: 5;
        }
        
        .btn-yes {
            background: linear-gradient(45deg, #ff6b81, #ff4757);
            color: white;
        }
        
        .btn-yes:hover {
            transform: scale(1.1);
            box-shadow: 0 0 20px rgba(255, 71, 87, 0.6);
        }
        
        .btn-no {
            background: transparent;
            border: 2px solid white;
            color: white;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
        
        #success-msg {
            display: none;
            font-size: 1.5rem;
            color: #ffd700;
            animation: popIn 0.5s ease;
            margin-top: 20px;
        }
        
        @keyframes popIn {
            0% {
                transform: scale(0);
                opacity: 0;
            }
            100% {
                transform: scale(1);
                opacity: 1;
            }
        }
    </style>
</head>

<body>

    <div id="canvas-container"></div>

    <div class="instruction" id="instruction-text">Click the Heart ❤</div>

    <!-- This card appears AFTER clicking the heart -->
    <div class="main-wrapper" id="card-container">
        <div class="card">
            <h1>Kimia</h1>
            <p class="subtitle">The Moon of my Sky</p>

            <p class="love-letter">
                In the darkness of the night, when everything falls silent, I look to the sky to see you. Your existence is like a full moon to me; bright, beautiful, and soothing. Kimia of my soul, I love you.
            </p>

            <div class="question-box">
                Can you be mine?
            </div>

            <div class="btn-group">
                <button class="btn btn-yes" onclick="sayYes()">Yes</button>
                <button class="btn btn-no" id="noBtn">No</button>
            </div>

            <div id="success-msg">
                I love you too! ❤<br>
                <span style="font-size: 1rem; color: white; font-weight: normal;">Forever & Always</span>
            </div>
        </div>
    </div>

    <script>
        // --- THREE.JS SCENE SETUP ---
        const scene = new THREE.Scene();
        scene.fog = new THREE.FogExp2(0x050505, 0.0008);

        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({
            alpha: true,
            antialias: true
        });

        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(window.devicePixelRatio);
        document.getElementById('canvas-container').appendChild(renderer.domElement);

        // --- 1. BACKGROUND STARS & MOON (Same as before) ---
        const starsGeometry = new THREE.BufferGeometry();
        const starsCount = 3000;
        const posArray = new Float32Array(starsCount * 3);
        for (let i = 0; i < starsCount * 3; i++) posArray[i] = (Math.random() - 0.5) * 1500;
        starsGeometry.setAttribute('position', new THREE.BufferAttribute(posArray, 3));
        const starsMaterial = new THREE.PointsMaterial({
            size: 2,
            color: 0xffffff,
            transparent: true,
            opacity: 0.8
        });
        const starMesh = new THREE.Points(starsGeometry, starsMaterial);
        scene.add(starMesh);

        const moonGeometry = new THREE.SphereGeometry(15, 64, 64);
        const moonMaterial = new THREE.MeshStandardMaterial({
            color: 0xfdfd96,
            emissive: 0xfdfd96,
            emissiveIntensity: 0.5
        });
        const moon = new THREE.Mesh(moonGeometry, moonMaterial);
        moon.position.set(-20, 20, -50);
        scene.add(moon);

        const pointLight = new THREE.PointLight(0xffffff, 1.5, 100);
        pointLight.position.set(-20, 20, -40);
        scene.add(pointLight);
        const ambientLight = new THREE.AmbientLight(0x404040, 0.5);
        scene.add(ambientLight);

        camera.position.z = 100;

        // --- 2. THE 3D HEART ---
        const heartShape = new THREE.Shape();
        const x = 0,
            y = 0;
        heartShape.moveTo(x + 5, y + 5);
        heartShape.bezierCurveTo(x + 5, y + 5, x + 4, y, x, y);
        heartShape.bezierCurveTo(x - 6, y, x - 6, y + 7, x - 6, y + 7);
        heartShape.bezierCurveTo(x - 6, y + 11, x - 3, y + 15.4, x + 5, y + 19);
        heartShape.bezierCurveTo(x + 12, y + 15.4, x + 16, y + 11, x + 16, y + 7);
        heartShape.bezierCurveTo(x + 16, y + 7, x + 16, y, x + 10, y);
        heartShape.bezierCurveTo(x + 7, y, x + 5, y + 5, x + 5, y + 5);

        const extrudeSettings = {
            depth: 4,
            bevelEnabled: true,
            bevelSegments: 2,
            steps: 2,
            bevelSize: 1,
            bevelThickness: 1
        };

        const geometry = new THREE.ExtrudeGeometry(heartShape, extrudeSettings);
        // Center the geometry
        geometry.center();
        // Scale it up
        geometry.scale(1.5, 1.5, 1.5);

        const material = new THREE.MeshPhysicalMaterial({
            color: 0xff0000,
            emissive: 0x550000,
            metalness: 0.1,
            roughness: 0.1,
            clearcoat: 1.0,
            clearcoatRoughness: 0.1
        });

        const heartMesh = new THREE.Mesh(geometry, material);
        scene.add(heartMesh);

        // Light specifically for the heart
        const heartLight = new THREE.PointLight(0xff0000, 2, 50);
        heartLight.position.set(0, 0, 20);
        scene.add(heartLight);


        // --- INTERACTION & ANIMATION ---
        let mouseX = 0;
        let mouseY = 0;
        let isCardVisible = false;
        let heartClicked = false;

        // Raycaster for clicking the heart
        const raycaster = new THREE.Raycaster();
        const pointer = new THREE.Vector2();

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        document.addEventListener('mousemove', (event) => {
            mouseX = (event.clientX - window.innerWidth / 2) / 10;
            mouseY = (event.clientY - window.innerHeight / 2) / 10;

            // For Raycaster
            pointer.x = (event.clientX / window.innerWidth) * 2 - 1;
            pointer.y = -(event.clientY / window.innerHeight) * 2 + 1;

            // Check Runaway Button only if card is visible
            if (isCardVisible) checkProximity(event.clientX, event.clientY);
        });

        document.addEventListener('click', (event) => {
            if (heartClicked) return; // Prevent multiple clicks

            raycaster.setFromCamera(pointer, camera);
            const intersects = raycaster.intersectObjects([heartMesh]);

            if (intersects.length > 0) {
                // HEART CLICKED!
                heartClicked = true;
                showCard();
            }
        });

        function animate() {
            requestAnimationFrame(animate);

            // Background Animation
            starMesh.rotation.y += 0.0002;
            moon.rotation.y += 0.001;

            // Camera Parallax
            camera.position.x += (mouseX - camera.position.x) * 0.02;
            camera.position.y += (-mouseY - camera.position.y) * 0.02;
            camera.lookAt(scene.position);

            // Heart Animation (Heartbeat)
            if (!heartClicked) {
                const time = Date.now() * 0.003;
                const scale = 1 + Math.sin(time) * 0.1; // Pulse
                heartMesh.scale.set(1.5 * scale, 1.5 * scale, 1.5 * scale); // Maintain base scale
                heartMesh.rotation.y += 0.01; // Rotate heart
                document.body.style.cursor = 'default';

                // Change cursor on hover
                raycaster.setFromCamera(pointer, camera);
                const intersects = raycaster.intersectObjects([heartMesh]);
                if (intersects.length > 0) {
                    document.body.style.cursor = 'pointer';
                }
            } else {
                // Shrink heart after click
                heartMesh.scale.multiplyScalar(0.9);
                if (heartMesh.scale.x < 0.01) heartMesh.visible = false;
            }

            renderer.render(scene, camera);
        }

        animate();

        // --- UI LOGIC ---

        function showCard() {
            // Fade out instructions
            document.getElementById('instruction-text').style.opacity = 0;

            // Show Card
            const card = document.getElementById('card-container');
            card.style.display = 'block';
            setTimeout(() => {
                card.style.opacity = 1;
            }, 10);

            isCardVisible = true;
        }

        // Runaway Button Logic
        const noBtn = document.getElementById('noBtn');
        const yesBtn = document.querySelector('.btn-yes');
        const successMsg = document.getElementById('success-msg');
        const questionBox = document.querySelector('.question-box');

        function checkProximity(mX, mY) {
            const btnRect = noBtn.getBoundingClientRect();
            const btnCenterX = btnRect.left + btnRect.width / 2;
            const btnCenterY = btnRect.top + btnRect.height / 2;
            const distance = Math.hypot(mX - btnCenterX, mY - btnCenterY);

            if (distance < 150) {
                runAwayButton();
            }
        }

        function runAwayButton() {
            const newLeft = Math.random() * (window.innerWidth - 100);
            const newTop = Math.random() * (window.innerHeight - 100);
            const rotation = Math.random() * 360;

            noBtn.style.left = `${newLeft}px`;
            noBtn.style.top = `${newTop}px`;
            noBtn.style.transform = `translate(0, 0) rotate(${rotation}deg)`;

            const taunts = ["Nope!", "Try harder", "Really?", "Catch me!", "Can't do it!"];
            noBtn.innerText = taunts[Math.floor(Math.random() * taunts.length)];

            noBtn.style.transition = "all 0.1s linear";
        }

        function sayYes() {
            noBtn.style.display = 'none';
            yesBtn.style.display = 'none';
            questionBox.innerHTML = "You made me the happiest person!";
            questionBox.style.borderColor = "#ffd700";
            questionBox.style.color = "#ffd700";
            successMsg.style.display = 'block';
            createConfetti();
        }

        function createConfetti() {
            for (let i = 0; i < 50; i++) {
                const conf = document.createElement('div');
                conf.style.position = 'fixed';
                conf.style.width = '10px';
                conf.style.height = '10px';
                conf.style.backgroundColor = ['#ff6b81', '#ffd700', '#ffffff'][Math.floor(Math.random() * 3)];
                conf.style.left = Math.random() * 100 + 'vw';
                conf.style.top = '-10px';
                conf.style.zIndex = '100';
                conf.style.borderRadius = '50%';
                document.body.appendChild(conf);
                const duration = Math.random() * 3 + 2;
                conf.style.transition = `top ${duration}s ease-in, opacity ${duration}s`;
                setTimeout(() => {
                    conf.style.top = '110vh';
                    conf.style.opacity = '0';
                }, 100);
                setTimeout(() => conf.remove(), duration * 1000);
            }
        }
    </script>
</body>

</html>
