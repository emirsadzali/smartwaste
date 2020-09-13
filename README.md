# smartwaste
/*
 * Salman Faris
 * HCSR04 - Test with ESP8266 Board
 */

#define trigPin D1   //Pin-14 GPIO5
#define echoPin D2   //pin-13 GPIO4

void setup() {


  Serial.begin(115200); 
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

}


void loop() {

  long duration, distance;

  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = (duration / 2) / 29.1;

  Serial.println(distance);



}

/**
   Poject :- Planet Bin
   Author :- Salman Faris
   Sensor-node id :- #001
**/


#include <ESP8266WiFi.h>
#include <PubSubClient.h>


const char* ssid = "******************"; //Wifi SSID
const char* password = "*************";             //WiFi Password


const char* mqtt_server = "104.215.155.129"; //Sever IP Address

WiFiClient espClient;
PubSubClient client(espClient);

#define trigPin D1   //Pin-14 | GPIO5 
#define echoPin D2   //Pin-13 | GPIO4


long now = millis();
long lastMeasure = 0;


void setup_wifi() {
  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.print("WiFi connected - ESP IP address: ");
  Serial.println(WiFi.localIP());
}


void callback(String topic, byte* message, unsigned int length) {
  Serial.print("Message arrived on topic: ");
  Serial.print(topic);
  Serial.print(". Message: ");
  String messageTemp;

  for (int i = 0; i < length; i++) {
    Serial.print((char)message[i]);
    messageTemp += (char)message[i];
  }
  Serial.println();
}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");

    if (client.connect("ESP8266Client")) {
      Serial.println("connected");
      // Subscribe or resubscribe to a topic
      // You can subscribe to more topics (to control more LEDs in this example)
      client.subscribe("wastebin1");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}


void setup() {
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

}
void loop() {

  if (!client.connected()) {
    reconnect();
  }
  if (!client.loop())
    client.connect("ESP8266Client");


  long duration, distance;

  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = (duration / 2) / 29.1;

  Serial.println(distance);

  int data2 = 80 - distance ;

  char distance1[7];
  char data3[7];


  dtostrf(distance, 6, 2, distance1);

  dtostrf(data2, 6, 2, data3);

  client.publish("wastebin1", distance1);

  delay(500);
  client.publish("wastebin2", data3);

  delay(2000);

}

curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
sudo apt-get install -y nodejs build-essential
