#include <SPI.h>
#include <LoRa.h>
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define inputCLK 7
#define inputDT 6
#define inputSW 5

#define SCREEN_WIDTH 64 // OLED display width, in pixels
#define SCREEN_HEIGHT 48 // OLED display height, in pixels

#define OLED_RESET     -1 // Reset pin # (or -1 if sharing Arduino reset pin)
#define SCREEN_ADDRESS 0x3C ///< See datasheet for Address; 0x3D for 128x64, 0x3C for 128x32
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

uint32_t frecuencia = 433000000;
uint32_t frecuenciaSW = 0;
int currentStateCLK;
int previousStateCLK;

void setup() {
  pinMode(inputCLK,INPUT);
  pinMode(inputDT,INPUT);
  pinMode(inputSW,INPUT_PULLUP);
  pinMode(16,OUTPUT);
  Serial.begin(9600);
  while (!Serial);

  if (!LoRa.begin(frecuencia)) {
    Serial.println("Starting LoRa failed!");
    while (1);
  }
  
  if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);
  }
  delay(2000);
  display.clearDisplay();
  previousStateCLK = digitalRead(inputCLK);
}

void loop() {
  digitalWrite(16,LOW);
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(5, 5);
  display.setTextSize(1);
  display.println(frecuencia);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(4, 28);
  display.setTextSize(1);
  display.println("Frecuencia");
  display.display();

  currentStateCLK = digitalRead(inputCLK);
  if(currentStateCLK != previousStateCLK){
    if(digitalRead(inputDT) != currentStateCLK){
      frecuencia -= 100000;
    }else{
      frecuencia +=100000;
    }
    Serial.print("Frecuencia: ");
    Serial.println(frecuencia);
  }
  previousStateCLK = currentStateCLK;
  receiver();
  
  /*if(digitalRead(inputSW)==LOW){
    frecuenciaSW = frecuencia;
    Serial.print("FrecuenciaSW: ");
    Serial.println(frecuenciaSW);
  }*/
}

void receiver(){
  Serial.println("LoRa Receiver");
  LoRa.setFrequency(frecuencia);
    // try to parse packetz
  int packetSize = LoRa.parsePacket();
  if (packetSize) {
    // received a packet
    Serial.print("Received packet '");
    // read packet
    while (LoRa.available()) {
      Serial.print((char)LoRa.read());
    }
    // print RSSI of packet
    Serial.print("' with RSSI ");
    Serial.println(LoRa.packetRssi());
    digitalWrite(16,HIGH);
    delay(100);
  }
}








