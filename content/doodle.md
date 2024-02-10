---
title: doodle
---


<style>
    canvas {
        border: 2px solid #FEAA70;
    }
</style>
</head>

<canvas id="drawingCanvas" width="400" height="400"></canvas><br>
<button onclick="resetCanvas()">Reset</button>
<button onclick="sendData()">Send</button>

<script>
function postToURL(url, imageData) {
    // Convert image data to Blob
    let blob = new Blob([imageData], { type: 'image/jpeg' });

    // Create a FormData object
    let formData = new FormData();
    // Append the Blob to the FormData object
    formData.append('image', blob, 'image.jpg');

    // Send a POST request with the FormData
    fetch(url, {
        method: 'POST',
        body: formData
    })
    .then(response => {
        if (!response.ok) {
            throw new Error('Network response was not ok');
        }
        return response.json();
    })
    .then(data => {
        console.log('Data sent successfully:', data);
    })
    .catch(error => {
        console.error('Error sending data:', error);
    });
}



    let canvas = document.getElementById("drawingCanvas");
    let ctx = canvas.getContext("2d");
    let bgColor= "#222129"; // default draw color
    let drawColor = "#FEAA70"; // default background color
    let drawing = false;
    
    function draw(e) {
        if (!drawing) return;
        ctx.fillStyle = drawColor;
        ctx.fillRect(e.clientX - canvas.offsetLeft, e.clientY - canvas.offsetTop, 4, 4);
    }
    
    canvas.addEventListener("mousedown", () => {
        drawing = true;
    });
    
    canvas.addEventListener("mousemove", draw);
    
    window.addEventListener("mouseup", () => {
        drawing = false;
    });
    
    function resetCanvas() {
        ctx.fillStyle = bgColor;
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    }
    
    function sendData() {
        // Post data to a specified URL
        let imageData = canvas.toDataURL(); // Convert canvas to image data
        let currentURL = window.location.href; // Get current URL
        let postData = {
            image: imageData,
            url: currentURL
        };
        // Assuming you have a function to post data to a URL
        // Replace 'postToURL' with your actual function to post data
        postToURL('http://localhost:8080/upload', postData);
    }
</script>
