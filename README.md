# Development environment for the SGP30 eCO2 sensor and M5Atom-lite (ESP32)
1. Install VSCode (Visual Studio Code) on your chosen operating system
2. Select the Extensions tab on the left side (Ctrl Shift X)
3. Search for PlatformIO, select and install it. VSCode will prompt you to reload it.
4. A new tab will appear on the side, with the PlatformIO logo (alien head symbol), select it.
5. When the PlatformIO menu appears, select the Home tab within, and press [+ New Project]
-- Give the project a name (e.g. atomlite_sgp30_test)
-- Select the 'Board:' M5Stack-Atom
-- Select the 'Framework:' Arduino
-- Press 'Finish'
6. It will load for a while, its downloading everything you need, once it finishes the VSCode will reload.
7. A prompt will appear asking if you trust the authors of this project, select the trust option.
8. The project files have been generated as seen on the folder view on the left side.
9. Select the PlatformIO tab once more and open the Libraries tab within.
10.  Search for 'SGP30'
-- In the results select 'Adafruit SGP30 Sensor by Adafruit', press 'Add to Project'.
-- Press 'Select a project' and click your project.
-- Press Add
-- Press OK and hit the back button in the top left.
11. Search for 'M5Stack'
-- In the results, select 'M5Atom by Hades, M5Stack', press 'Add to Project'.
-- Press 'Select a project' and click your project.
-- Press Add
-- Press OK and hit the back button in the top left.
12. Search for 'FastLED'
-- In the results, select 'FastLED by Daniel Garcia', press 'Add to Project'.
-- Press 'Select a project' and click your project.
-- Press Add
-- Press OK
13. Select the Explorer tab on the left side (Ctrl Shift E)
14. In the folder panel on the left, expand the 'src' folder and select the file 'main.cpp'.
-- Replace the contents with this code:
```cpp
#include  <Arduino.h>
#include  <M5Atom.h>
#include  "Adafruit_SGP30.h"
  
Adafruit_SGP30  sgp;
int  i = 15;
long  last_millis = 0;

void setup() {
	M5.begin();
	Wire.begin(26,32);
	Serial.begin(115200);
	Serial.println("\nSGP30 test program");
	if (! sgp.begin()) {
		Serial.println("SGP30 not found...");
		while (1);
	}
	Serial.print("Found SGP30 serial #");
	Serial.print(sgp.serialnumber[0], HEX);
	Serial.print(sgp.serialnumber[1], HEX);
	Serial.println(sgp.serialnumber[2], HEX);
}

void loop() {
	while(i > 0) {
		if(millis()- last_millis > 1000) {
			last_millis = millis();
			i--;
			Serial.print("Readings will start in: ");
			Serial.println(i);
		}
	}
	if (! sgp.IAQmeasure()) {
		Serial.println("Measurement failed");
		return;
	}
	Serial.print("TVOC ");
	Serial.print(sgp.TVOC);
	Serial.print(" ppb\t");
	Serial.print("eCO2 ");
	Serial.print(sgp.eCO2);
	Serial.println(" ppm");
	delay(1000);
}
```

16. At the bottom of the project folder select the platform.ini file, add in the line ```monitor_speed = 115200``` so it looks like this:
```ini
[env:m5stack-atom]
platform = espressif32
board = m5stack-atom
framework = arduino
monitor_speed = 115200
lib_deps =
	m5stack/M5Atom@^0.0.5
	adafruit/Adafruit SGP30 Sensor@^2.0.0
	fastled/FastLED@^3.4.0
```
17. Plug in your M5Atom-Lite via a USB cable, with the TVOC/SGP30 sensor connected between their grove ports.
18. If you are on a typical linux distribution, make sure your user is added to the 'dialout' group else you cant write data to the M5Atom-Lite. After you will need to logout/login or restart, graphically logging in and out sometimes wont apply the change.
19. Click on the PlatformIO tab on the left side once more, in the side panel under PROJECT TASKS > Default > General find and select 'Upload and Monitor'
20. The terminal at the bottom will appear. It will build the application, flash it to the M5Atom-Lite and then show you the the serial output. Readings from the eCO2 sensor will appear shortly after.
