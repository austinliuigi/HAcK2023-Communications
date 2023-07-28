# 🖥️ HAcK Communication
In this year's HAcK, you will be communicating your sensor data collected from
your ESP to your website in real time. To handle this, we have blackboxed the
communication part so all you will need to do is call the proper functions that
we will be providing you.

## Arduino

### Installation
1. Install the following two libraries into your IDE:
    - [ArduinoJSON](https://github.com/bblanchon/ArduinoJson) by *Benoit Blanchon*
    - [PubSubClient](https://github.com/knolleary/pubsubclient) by *Nick O'Leary*
2. Download into your sketch folder:
    - [HackPublisher.h](https://github.com/austinliuigi/HAcK2023-Communications/blob/main/HackPublisher.h)

### Usage

#### `HackPublisher(team_name: const char*) -> HackPublisher`
Creates a `HackPublisher` instance which will handle sending data to the
website.

#### `HackPublisher.begin()`
Initializes the publisher. Call this in `setup()` ***after connecting to wifi***.

#### `HackPublisher.store(sensor: const char*, value: any)`
Store a sensor's value that will be communicated to the website on the next call
to `HackPublisher.send()`. The `sensor` argument will be used in your website to
target the tag with the same id (this will be explained further in the
javascript section of this document).

#### `HackPublisher.send()`
Send the stored sensor data to the website

### Example
``` cpp
#include "HackPublisher.h"

const char *ssid = <SECRET_SSID>;
const char *password = <SECRET_PASSWORD>;

HackPublisher publisher("hackers");  // publisher instance for team "hackers"
int temp = 0;  // variable that holds the temperature

void setup() {
  // Initialize serial communication
  Serial.begin(115200);
  while (!Serial) continue;

  // Connect to wifi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  // Initialize publisher
  publisher.begin();
}

void loop() {
  publisher.store("ultrasonic", 12.56); // store value for ultrasonic sensor
  publisher.store("temp", temp);        // store value for temp
  publisher.store("meow", "woof");      // store value for meow

  publisher.send();                     // send stored data to website

  delay(2000);
  temp++;
}
```

## Javascript

### Installation
- Download into your website folder:
    - [communication.js](https://github.com/austinliuigi/HAcK2023-Communications/blob/main/communication.js)

### Usage
Add the following script tags to your html. ***Be sure to replace `"hackers"`
with your actual team name.*** The string that you use for the team name *must
match* the string you use when you initialize your arduino publisher.
``` html
<script>team_name = "hackers"</script>
<script type="module" src="communication.js"></script>
```

To add tags that contain the value of the sent sensor data, they *must contain
an `id` attribute with the same name as the sensor*. This sensor name is the one
you used in arduino when you called `HackPublisher.store()`.

### Example
``` cpp
/* arduino_file.ino */

// Initialize Wifi and Publisher
// ...
void loop() {
  publisher.store("temperature", temp);  // store value for temperature
  publisher.send();  // send the stored value for temperature
}
```
``` html
<!-- html_file.js -->

<!-- ... -->
<p id="temperature"></p>  <!-- this will be populated with the sensor data -->
<!-- ... -->
```


### Additional Notes
If you want to work locally on your website, these are two options that you
could use to locally host your site:
- [http-server](https://www.npmjs.com/package/http-server)
- [live-server](https://www.npmjs.com/package/live-server)
