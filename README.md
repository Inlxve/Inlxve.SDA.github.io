<!-- combined_teachable_machine.html -->
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Combined Teachable Machine Image Model</title>
<!-- TensorFlow.js script tags -->
<script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>
<!-- p5.js and ml5.js script tags -->
<script src="https://cdn.jsdelivr.net/npm/p5@latest/lib/p5.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/p5@latest/lib/addons/p5.dom.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/ml5@latest/dist/ml5.min.js"></script>
</head>
<body>
<div>Teachable Machine Image Model - TensorFlow.js</div>
<button type="button" onclick="initTF()">Start TensorFlow.js</button>
<div id="webcam-container-tf"></div>
<div id="label-container-tf"></div>
<hr>
<div>Teachable Machine Image Model - p5.js and ml5.js</div>
<button type="button" onclick="initML5()">Start p5.js and ml5.js</button>
<div id="webcam-container-ml5"></div>
<div id="label-container-ml5"></div>
<script type="text/javascript">
   // TensorFlow.js code
   const URL_TF = "./my_model_tf/";
   let modelTF, webcamTF, labelContainerTF, maxPredictionsTF;
   async function initTF() {
     const modelURL = URL_TF + "model.json";
     const metadataURL = URL_TF + "metadata.json";
     modelTF = await tmImage.load(modelURL, metadataURL);
     maxPredictionsTF = modelTF.getTotalClasses();
     const flipTF = true;
     webcamTF = new tmImage.Webcam(200, 200, flipTF);
     await webcamTF.setup();
     await webcamTF.play();
     window.requestAnimationFrame(loopTF);
     document.getElementById("webcam-container-tf").appendChild(webcamTF.canvas);
     labelContainerTF = document.getElementById("label-container-tf");
     for (let i = 0; i < maxPredictionsTF; i++) {
       labelContainerTF.appendChild(document.createElement("div"));
     }
   }
   async function loopTF() {
     webcamTF.update();
     await predictTF();
     window.requestAnimationFrame(loopTF);
   }
   async function predictTF() {
     const prediction = await modelTF.predict(webcamTF.canvas);
     for (let i = 0; i < maxPredictionsTF; i++) {
       const classPrediction = prediction[i].className + ": " + prediction[i].probability.toFixed(2);
       labelContainerTF.childNodes[i].innerHTML = classPrediction;
     }
   }
   // p5.js and ml5.js code
   let classifierML5;
   let imageModelURLML5 = 'https://teachablemachine.withgoogle.com/models/QxGqkZk5U/';
   let videoML5;
   let flippedVideoML5;
   let labelML5 = "";
   function preload() {
     classifierML5 = ml5.imageClassifier(imageModelURLML5 + 'model.json');
   }
   function setup() {
     createCanvas(320, 260);
     videoML5 = createCapture(VIDEO);
     videoML5.size(320, 240);
     videoML5.hide();
     flippedVideoML5 = ml5.flipImage(videoML5);
     classifyVideoML5();
   }
   function draw() {
     background(0);
     image(flippedVideoML5, 0, 0);
     fill(255);
     textSize(16);
     textAlign(CENTER);
     text(labelML5, width / 2, height - 4);
   }
   function classifyVideoML5() {
     flippedVideoML5 = ml5.flipImage(videoML5);
     classifierML5.classify(flippedVideoML5, gotResultML5);
     flippedVideoML5.remove();
   }
   function gotResultML5(error, results) {
     if (error) {
       console.error(error);
       return;
     }
     labelML5 = results[0].label;
     classifyVideoML5();
   }
</script>
</body>
</html>
har genvejsmen
