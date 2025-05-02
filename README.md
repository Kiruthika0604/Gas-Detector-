#include <ESP8266WiFi.h>

const char* ssid = "YOUR_SSID";
const char* password = "YOUR_PASSWORD";

WiFiServer server(80);
int gasSensorPin = A0;

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  Serial.println("Connecting to WiFi...");
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("Connected!");
  Serial.println(WiFi.localIP());
  server.begin();
}

void loop() {
  WiFiClient client = server.available();
  if (client) {
    Serial.println("Client Connected");
    String request = client.readStringUntil('\r');
    client.flush();

    int gasValue = analogRead(gasSensorPin);

    String html = "<!DOCTYPE html><html><head><meta http-equiv='refresh' content='2'>";
    html += "<style>body{font-family:sans-serif;text-align:center;padding-top:30px;}</style>";
    html += "<h1>Gas Detection System</h1>";
    html += "<h2>Gas Level: " + String(gasValue) + "</h2>";
    html += "</body></html>";

    client.println("HTTP/1.1 200 OK");
    client.println("Content-Type: text/html");
    client.println("Connection: close");
    client.println();
    client.println(html);
    delay(100); // optional
  }
}
