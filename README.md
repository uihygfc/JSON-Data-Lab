// Fetching live weather data and visualizing it in p5.js

let weatherData;
let url = "https://api.weather.gov/gridpoints/OKX/33,37/forecast"; // Replace with your grid info

function preload() {
  // Load JSON data from the weather API
  loadJSON(url, (data) => {
    weatherData = data.properties.periods;
  });
}

function setup() {
  createCanvas(800, 600);
  noLoop(); // Stop draw() from looping
}

function draw() {
  background(220);

  if (!weatherData) {
    textAlign(CENTER, CENTER);
    textSize(16);
    text("Loading weather data...", width / 2, height / 2);
    return;
  }

  let margin = 60;
  let chartWidth = width - 2 * margin;
  let chartHeight = height - 2 * margin - 100;

  let xSpacing = chartWidth / weatherData.length;
  let maxTemp = Math.max(...weatherData.map((d) => d.temperature));
  let minTemp = Math.min(...weatherData.map((d) => d.temperature));

  // Title
  textAlign(CENTER, CENTER);
  textSize(20);
  text("Weather Forecast Visualization", width / 2, margin / 2);

  // Draw axis
  stroke(0);
  line(margin, height - margin, width - margin, height - margin); // X-axis
  line(margin, height - margin, margin, margin); // Y-axis

  // Y-axis labels
  textSize(12);
  textAlign(RIGHT, CENTER);
  for (let t = minTemp; t <= maxTemp; t += 5) {
    let y = map(t, minTemp, maxTemp, height - margin, margin);
    text(t + "°", margin - 10, y);
    line(margin - 5, y, margin, y);
  }

  // X-axis labels
  textAlign(CENTER, TOP);
  for (let i = 0; i < weatherData.length; i++) {
    let x = margin + i * xSpacing;
    if (i % 2 === 0) { // Skip every other label to prevent overlap
      text(weatherData[i].name, x, height - margin + 5);
    }
  }

  // Draw bars
  fill(100, 150, 255);
  noStroke();
  for (let i = 0; i < weatherData.length; i++) {
    let x = margin + i * xSpacing;
    let y = map(weatherData[i].temperature, minTemp, maxTemp, height - margin, margin);
    let barHeight = height - margin - y;
    rect(x - xSpacing / 4, y, xSpacing / 2, barHeight);
  }

  // Add axis titles
  textAlign(CENTER, CENTER);
  textSize(14);
  text("Days", width / 2, height - margin + 40);
  push();
  translate(margin - 50, height / 2);
  rotate(-PI / 2);
  text("Temperature (°F)", 0, 0);
  pop();
}
