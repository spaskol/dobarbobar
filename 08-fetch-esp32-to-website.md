# Fetch ESP32 data to website

Assumptions:
1. I used ESP32 DEVKITV1 with DHT11 sensor
2. My website is hosted on Ubuntu server using Apache HTTP
3. I used Arduino IDE on Windows 10 to upload the sketch file (C++ file) 

## Install Arduino IDE and upload sketch file to ESP 32

### Connect ESP 32 via USB and install drivers

First I conected ESP 32 and installed the driver from [here](https://www.silabs.com/documents/public/software/CP210x_VCP_Windows.zip).

### Install Arduino IDE

Then I installed Arduino IDE from official web page.

### Install needed dependencies and connect ESP 32

I added the URL 'https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json' in "Additional boards manager URLs" from File->Preferences.

Then I installed the dependencies from Arduino IDE GUI. Here are the links in gitHub:

1. [esp32 by Espressif](https://github.com/espressif/arduino-esp32)
2. [DHT sensor library from   Adafruit](https://github.com/adafruit/DHT-sensor-library)

I waited a lot.

Finally I connected ESP 32 from Tools->Port

### Upload sketch file to ESP 32

I pasted the code below, changed 'your_ssid', 'your_password', <port>, <DHTPIN> to 27 (I connected data cable to pin 27).
Then uploaded it with the button like arrow pointing to the right (->).

```cpp
#include <WiFi.h>
#include <WebServer.h>
#include <DHT.h>

// WiFi credentials
const char* ssid = "your_ssid";
const char* password = "your_password";

// DHT11 Configuration
#define DHTPIN <DHTPIN>
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

// Create ESP32 Web Server on port <port>
WebServer server(<80>);

// Function to handle root page
void handleRoot() {
  String html = "<html><head><meta http-equiv='refresh' content='5'></head><body>";
  html += "<h1>ESP32 DHT11 Sensor</h1>";
  html += "<p>Temperature: " + String(dht.readTemperature()) + "°C</p>";
  html += "<p>Humidity: " + String(dht.readHumidity()) + "%</p>";
  html += "</body></html>";
  server.send(200, "text/html", html);
}

// Function to serve JSON data (REST API)
void handleData() {
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  String json = "{\"temperature\": " + String(temperature) + ", \"humidity\": " + String(humidity) + "}";
  server.send(200, "application/json", json);
}

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  
  Serial.print("Connecting to WiFi...");
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("\nConnected!");
  Serial.print("ESP32 IP Address: ");
  Serial.println(WiFi.localIP());

  dht.begin();

  // Define server routes
  server.on("/", handleRoot);
  server.on("/data", handleData);
  
  // Start server
  server.begin();
}

void loop() {
  server.handleClient();  // Handle client requests
}
```


### Check if the connection is successful

After the sketch was uploaded I went to Tools->Serial Monitor, swithed to Both NL & CR and select 115200 baud.

I received:
```
Connected!
ESP32 IP Address: <ESP32-IP-Address>
```
If you don't see the message you can try to press "EN" button.

## Set webpage

### Enable Apache proxy module

```bash
$ sudo a2enmod proxy
Enabling module proxy.
To activate the new configuration, you need to run:
  systemctl restart apache2
$ sudo a2enmod proxy_http
Considering dependency proxy for proxy_http:
Module proxy already enabled
Enabling module proxy_http.
To activate the new configuration, you need to run:
  systemctl restart apache2
```

### Change site configuration in '/etc/apache2/sites-available'

I added the following lines under the tag <VirtualHost>

```bash
# Reverse proxy for ESP32 sensor data
ProxyPass "/sensordata" "http://<ESP32-IP-Address>:<port>/data"
ProxyPassReverse "/sensordata" "http://<ESP32-IP-Address>:<port>/data"
```
And after that restarted Apache

```bash
sudo systemctl restart apache2
```

Then I tested by adding /sesordata. For example www.examle.com/sesordata

### Add script to fetch data in index.html

I was almost done. I had to add only the part were the data is fetched by the website.
So I added below lines in my /var/www/example/index.html file.

```html
<script>
    function fetchData() {
    fetch("/sensordata")
            .then(response => response.json())
            .then(data => {
                document.getElementById("temperature").innerText = data.temperature + "°C";
                document.getElementById("humidity").innerText = data.humidity + "%";
            })
            .catch(error => console.error('Error:', error));
    }
    setInterval(fetchData, 5000); // Refresh every 5s
</script>

...

<h2>Temperature from ESP 32 sensor:</h2>
<p>Temperature: <span id="temperature">Loading...</span></p>
<p>Humidity: <span id="humidity">Loading...</span></p>
```

## Source:

Prompting chatGPT and testing.





