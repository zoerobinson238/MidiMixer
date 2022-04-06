# MidiMixer

// Event Horizon Starter
// Zane Cochran
// 27 March 2022

boolean isBroadcast = false;       // Toggles Visualization On/Off
String ipAddress = "10.40.4.115";  // Event Horizon IP Address

int x = 240;
int y = 300;

void setup() {
  size(480, 480);
  initBroadcast();
  initAudio();
}

float whichColor = 0;
void draw() {
  background(0);
  getLoud();
  
  if(soundSize>100){whichColor = random(255);}
  
  colorMode(HSB, 255);
  fill(whichColor, 255,255);
  beginShape();
  vertex(x, y-soundSize);
  vertex(x+soundSize/4, y+soundSize/4);
  vertex(x-soundSize/2.5, y-soundSize/4);
  vertex(x+soundSize/2.5, y-soundSize/4);
  vertex(x-soundSize/4, y+soundSize/4);
  endShape();
  if (isBroadcast) {
    broadcast();
  }
}

import processing.sound.*;
AudioIn input;
Amplitude loudness;
Waveform waveform;

float sizeMin = 0;    // Set Min for Sound Size
float sizeMax = 350;  // Set Max for Sound Size
float soundSize = 0;  // Current Sound Size
int samples = 100;    // Number of Audio Wave Samples

// Initialize Audio Input
void initAudio(){
  input = new AudioIn(this, 0);
  input.start();
  loudness = new Amplitude(this);
  loudness.input(input);
}

// Get Audio Loudness
void getLoud(){
  float inputLevel = map(mouseY, 0, height, 1.0, 0.0);
  input.amp(inputLevel);
  float volume = loudness.analyze();
  soundSize = int(map(volume, 0, 0.5, sizeMin, sizeMax));
  // Do Something
}

// Get Audio Waves
void getWave(){
  waveform.analyze();
  for(int i = 0; i < samples; i++){
    // Do something
  }
}

import processing.video.*;
import javax.imageio.*;
import java.awt.image.*; 
import java.net.*;
import java.io.*;

int clientPort = 9100; 
DatagramSocket ds; 

void initBroadcast(){
  try {
    ds = new DatagramSocket();
  } catch (SocketException e) {
    e.printStackTrace();
  }
  frameRate(30);
}

// Broadcast Image
void broadcast() {

  BufferedImage bimg = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB );
  loadPixels();
  bimg.setRGB( 0, 0, width, height, pixels, 0, width);

  ByteArrayOutputStream baStream  = new ByteArrayOutputStream();
  BufferedOutputStream bos    = new BufferedOutputStream(baStream);

  try {
    ImageIO.write(bimg, "jpg", bos);
  } 
  catch (IOException e) {
    e.printStackTrace();
  }

  byte[] packet = baStream.toByteArray();

  try {
    ds.send(new DatagramPacket(packet,packet.length, InetAddress.getByName(ipAddress),clientPort));
  } 
  catch (Exception e) {
    e.printStackTrace();
  }
}
