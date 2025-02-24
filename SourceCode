#include <WiFi.h>

const char* ssid = "Joseph";
const char* password = "12345678";

WiFiServer server(80);

const int leds[] = {15, 16, 17, 19, 21}; 
bool OnAll = false;

void setup() {
    Serial.begin(115200);
    for (int led : leds) {
        pinMode(led, OUTPUT);
        digitalWrite(led, LOW);
    }

    WiFi.begin(ssid, password);
    Serial.print("Connecting to WiFi...");
    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.print(".");
    }

    Serial.println("\nConnected to WiFi!");
    Serial.print("IP Address: ");
    Serial.println(WiFi.localIP());

    server.begin();
}

void loop() {
    WiFiClient client = server.available();
    if (!client) return; 
    String request = client.readStringUntil('\r');
   
    while (client.available()) client.read();

    if (request.indexOf("/LED=ALL") != -1) {
        AllLEDs();
    } else if (request.indexOf("/LED=32415") != -1) {
        const int sequence1[] = {17, 16, 19, 15, 21};  
        Sequence32415(sequence1, 5);
    } else if (request.indexOf("/LED=12345") != -1) {
        CenterToSide();
    }

    sendResponse(client);
}


void sendResponse(WiFiClient &client) {
    String html = "<!DOCTYPE html><html><head><title>ESP32 LED Control</title>";
    html += "<meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\"></head><body>";
    html += "<div class=\"container\">";
    html += "<h1>ESP32 LED Control</h1>";
    html += "<a href=\"/LED=ALL\"><button class=\"btn\">All LEDs ON</button></a><br>";
    html += "<a href=\"/LED=32415\"><button class=\"btn\">32415</button></a><br>";
    html += "<a href=\"/LED=12345\"><button class=\"btn\">Center to Side</button></a>";
    html += "</div></body></html>";

    client.println("HTTP/1.1 200 OK");
    client.println("Content-type:text/html");
    client.println();
    client.println(html);
    client.println();
    delay(10);
    client.stop();
}

void AllLEDs() {
    Serial.println("All Leds ON");
    OnAll = !OnAll;
    for (int led : leds) {
        digitalWrite(led, OnAll ? HIGH : LOW);
    }
}

void Sequence32415 (const int sequence[], int size) {
    Serial.println("32415");
    for (int i = 0; i < size; i++) {
        digitalWrite(sequence[i], HIGH);
        delay(300);
        digitalWrite(sequence[i], LOW);
    }
}

void CenterToSide() {
    Serial.println("Center to Side");
    ledPattern({17}, 300);
    ledPattern({19, 16}, 300);
    ledPattern({21, 15}, 300);
    ledPattern({19, 16}, 300);
    ledPattern({17}, 300);
    clearLEDs();
}

void ledPattern(std::initializer_list<int> pattern, int delayTime) {
    clearLEDs();
    for (int led : pattern) {
        digitalWrite(led, HIGH);
    }
    delay(delayTime);
}

void clearLEDs() {
    for (int led : leds) {
        digitalWrite(led, LOW);
    }
}

