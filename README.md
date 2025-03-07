<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fishing Game</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three/examples/js/controls/PointerLockControls.js"></script>
    <style>
        body { margin: 0; overflow: hidden; font-family: Arial, sans-serif; }
        #info { position: absolute; top: 10px; left: 10px; color: white; background: rgba(0, 0, 0, 0.5); padding: 10px; }
    </style>
</head>
<body>
    <div id="info">Click to enter fullscreen. Use mouse to look around. Press 'F' to fish.</div>
    <script>
        let scene, camera, renderer, controls;
        let lake, rod, npc;
        let fish = [];
        let caughtFish = [];

        function init() {
            // Scene setup
            scene = new THREE.Scene();
            camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            renderer = new THREE.WebGLRenderer();
            renderer.setSize(window.innerWidth, window.innerHeight);
            document.body.appendChild(renderer.domElement);

            // Controls (First-person)
            controls = new THREE.PointerLockControls(camera, document.body);
            document.body.onclick = () => controls.lock();

            // Ground
            let groundGeometry = new THREE.PlaneGeometry(50, 50);
            let groundMaterial = new THREE.MeshBasicMaterial({ color: 0x228B22 });
            let ground = new THREE.Mesh(groundGeometry, groundMaterial);
            ground.rotation.x = -Math.PI / 2;
            scene.add(ground);

            // Lake
            let lakeGeometry = new THREE.CircleGeometry(10, 32);
            let lakeMaterial = new THREE.MeshBasicMaterial({ color: 0x1E90FF });
            lake = new THREE.Mesh(lakeGeometry, lakeMaterial);
            lake.rotation.x = -Math.PI / 2;
            lake.position.set(0, 0.01, 0);
            scene.add(lake);

            // Fishing Rod
            let rodGeometry = new THREE.CylinderGeometry(0.1, 0.1, 2);
            let rodMaterial = new THREE.MeshBasicMaterial({ color: 0x8B4513 });
            rod = new THREE.Mesh(rodGeometry, rodMaterial);
            rod.position.set(0.5, -1, -1);
            camera.add(rod);
            scene.add(camera);

            // NPC (Fish Seller)
            let npcGeometry = new THREE.BoxGeometry(1, 2, 1);
            let npcMaterial = new THREE.MeshBasicMaterial({ color: 0xFFD700 });
            npc = new THREE.Mesh(npcGeometry, npcMaterial);
            npc.position.set(5, 1, 5);
            scene.add(npc);

            // Spawn Fish
            spawnFish();

            // Animation Loop
            animate();
        }

        function spawnFish() {
            for (let i = 0; i < 10; i++) {
                let fishGeometry = new THREE.SphereGeometry(0.3, 16, 16);
                let fishMaterial = new THREE.MeshBasicMaterial({ color: Math.random() > 0.8 ? 0xff0000 : 0xffffff });
                let newFish = new THREE.Mesh(fishGeometry, fishMaterial);

                let x = (Math.random() * 8) - 4;
                let z = (Math.random() * 8) - 4;
                newFish.position.set(x, 0.2, z);
                fish.push(newFish);
                lake.add(newFish);
            }
        }

        function catchFish() {
            let caught = fish.pop();
            if (caught) {
                caughtFish.push(caught);
                lake.remove(caught);
                alert(`You caught a ${caught.material.color.getHex() === 0xff0000 ? 'CHEAT FISH' : 'Normal Fish'}!`);
            } else {
                alert('No fish left!');
            }
        }

        function sellFish() {
            let earnings = 0;
            caughtFish.forEach(f => {
                earnings += f.material.color.getHex() === 0xff0000 ? 500 : 100;
            });
            alert(`You sold your fish for $${earnings}!`);
            caughtFish = [];
        }

        function animate() {
            requestAnimationFrame(animate);
            renderer.render(scene, camera);
        }

        // Fishing event
        document.addEventListener('keydown', (event) => {
            if (event.code === 'KeyF') catchFish();
        });

        // Sell fish when near NPC
        document.addEventListener('keydown', (event) => {
            if (event.code === 'KeyE') sellFish();
        });

        // Resize handler
        window.addEventListener('resize', () => {
            renderer.setSize(window.innerWidth, window.innerHeight);
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
        });

        init();
    </script>
</body>
</html>
