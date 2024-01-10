# Smart Dehumidifier: Design and Energy Efficiency in Indoor Air Quality Management
This project aims at creating a smart dehumidifier that automatically regulates humidity, activating when humidity exceeds a preset threshold, thereby providing a healthier living space while reducing the risk of building damage and health problems caused by excessive humidity.
![d5bcbbdea3ca29e158ff35d15369780](https://github.com/2333-hr/CASA0016-coursework/assets/146243657/4b53368f-8c99-450c-b964-e901a0f60a17)
#overview
With rising living standards and growing health awareness, indoor air quality is increasingly crucial. Maintaining appropriate indoor humidity levels is crucial for comfort and health, as excess moisture can cause discomfort, encourage mold, and foster harmful microorganisms.
Applicable in various settings such as homes in damp regions, storage spaces for books and artwork, and laundry areas, this device serves those in humid climates, individuals with specific storage needs, and anyone seeking to enhance their living quality through automated humidity control. The device operates autonomously, adjusting to environmental conditions to ensure a consistently pleasant indoor atmosphere while promoting energy efficiency and a sustainable lifestyle.
![image](https://github.com/2333-hr/CASA0016-coursework/assets/146243657/871b9115-cb39-45cb-838b-48c2d5ee6db2)
#Hardware
| COMPONENTS        | TYPE                        | FUNCTION                                                                                      |
|-------------------|-----------------------------|-----------------------------------------------------------------------------------------------|
| ARDUINO UNO R3    | Microcontroller Development Board | Provides power/controls the behavior of all other components/it reads sensor data, makes decisions based on logical judgment, and directs actuators such as motors and LED strips to work as required |
| PIR Monitor       | Sensor                      | Used to detect human or animal movement.When motion is detected, it can trigger specific actions, such as activating an LED light strip or turning on a water pump, in response to a person's approach. |
| DHT22 Monitor     | Sensor                      | Detect ambient humidity to decide whether to turn on the water pump and determine the light strip. |
| L9110 Motor Driver| Motor Driver                | Receive commands from Arduino to control the water pump on and off.                           |
| NeoPixel LED Stick| Actuator                    | Visualize ambient humidity by changing color based on DHT22 sensor readings.                  |
| Small Water Pump  | Actuator                    | Simulated dehumidifier as a low voltage circuit project model component.                      |
| Switch Board      | Switch Perfboard            | Control circuit connection                                                                   |

#Schematics
##Component assembly drawing
![image](https://github.com/2333-hr/CASA0016-coursework/assets/146243657/847b1367-ad37-4a73-b9d5-678f543d6957)
##Breadboard
![Untitled Sketch 3_bb](https://github.com/2333-hr/CASA0016-coursework/assets/146243657/a8bcf783-90f4-4bcb-bd8b-847d8870253b)
- The switch is connected in series with the power line.
- DHT22, PIR monitor, motor drive module, and RGB LED are all connected in parallel in the circuit.
- The water pump is connected to the motor drive module through terminals.
- The DHT22 signal pin is at No. 2; the light bar signal pin is at No. 7; the PIR monitor signal pin is at No. 3; the L9110 signal ground wire is connected to the negative pole of the power supply, and the signal input wire is connected to No. 4.
#code
1.Library Inclusion and Sensor/Actuator Setup:
This block includes necessary libraries and defines the pins and settings for the DHT22 sensor, NeoPixel LED strip, and motor driver module.

```#include <Adafruit_NeoPixel.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT22
DHT dht(DHTPIN, DHTTYPE);

#define LED_PIN 7
#define NUMPIXELS 8
Adafruit_NeoPixel pixels(NUMPIXELS, LED_PIN, NEO_GRB + NEO_KHZ800);

const int motorPin = 4;
const int pirPin = 3;
```

2.Configuration and Initialization:
In setup(), pins are configured as input or output, and the sensors and actuators are initialized.

```void setup() {
  pinMode(motorPin, OUTPUT);
  pinMode(pirPin, INPUT);
  dht.begin();
  pixels.begin();
  pixels.setBrightness(50);
  Serial.begin(9600);}
```

3.Main Logic in Loop Function:
The loop() function contains the logic to read the humidity, control the motor, and adjust the LED colors based on the humidity level and PIR sensor input.

```void loop() {
  // Read humidity
  float humidity = dht.readHumidity();
  // Read PIR sensor
  int pirValue = digitalRead(pirPin);

  // Check if humidity reading was successful
  if (isnan(humidity)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  // Control motor and LED strip
  if (humidity > humidityThreshold) {
    digitalWrite(motorPin, HIGH); // Start the water pump
    if(pirValue == HIGH) { // When motion is detected close by
      setAllPixels(255, 0, 0); // Red
    }
  } else {
    digitalWrite(motorPin, LOW); // Stop the water pump
    if(pirValue == HIGH) { // When motion is detected close by
      setAllPixels(0, 255, 0); // Green
    }
  }

  // If no motion is detected, turn off LED strip
  if(pirValue == LOW) {
    setAllPixels(0, 0, 0); // Turn off LED
  }

  // Output current humidity value to serial
  Serial.print("Humidity: ");
  Serial.println(humidity);
  delay(2000); // Wait some time before reading again
}
```

4.LED Control Function:
 The setAllPixels() function changes the color of all the pixels in the LED strip.

```void setAllPixels(uint8_t red, uint8_t green, uint8_t blue) {
  for(int i = 0; i < NUMPIXELS; i++) {
    pixels.setPixelColor(i, pixels.Color(red, green, blue));
  }
  pixels.show();}
```

The function of this code is to control the water pump based on the reading of the temperature and humidity sensor, and to change the color of the LED bar based on whether the PIR sensor detects the movement. When the humidity exceeds 60% and the motion is detected, the LED displays red and the pump is opened; when the humidity is below 60%, the LED displays green. If no motion is detected, the LED turns off regardless of humidity.
#Enclosure
For the outer package of the project, I used a 3mm birch template with laser cut into a square box with mortise and tenon joint structure, and bonded the outer package with wood glue. Window-shaped hollow cuts on one of the sides for heat dissipation. The initial cutting drawings are as follows:

<img width="213" alt="image" src="https://github.com/2333-hr/CASA0016-coursework/assets/146243657/4d21d15b-ebc6-4898-a0d2-c585c9da50f4">

After the display, the teacher's suggestion was that the packaging size was too large, which was not convenient for indoor electrical appliances, so I designed to reduce the size of the outer package, and the cutting drawings are as follows:

<img width="238" alt="image" src="https://github.com/2333-hr/CASA0016-coursework/assets/146243657/e06a5603-3e6f-4561-a68b-f715a88495c8">

#main function
-When the humidity is higher than the set humidity threshold, the dehumidifier (simulated  as a water pump in this project) starts until the humidity is lower than the set threshold.
-The LED strip can display the current room humidity status to the user through the light color.When the room humidity is higher than the set threshold, the LED strip turns red, otherwise it is green.
-When a person is approaching and is detected by the PIR monitor, the LED strip lights up to show the current humidity state of the room.When no one is observing, the LED strip goes out to save energy, and other components operate normally.
Using MakerCase as a template, I designed the final casing in CAD.
#Future Improvements
- Function Expansion**: Plans to add humidification capabilities to adapt to different indoor humidity conditions.
- Sensor Integration**: Integration of air quality sensors like PM2.5, VOCs, and CO2 to create a comprehensive indoor air quality management system.
- Performance Enhancement**: Use of more accurate humidity sensors and efficient pumps and fans to improve performance and reduce energy consumption.
- Smart Algorithm Application**: Exploration of machine learning algorithms for autonomous learning and automatic humidity adjustment.
- Market Potential**: Development of a multifunctional product line to meet the personalized needs of different users.
- Smart Home Integration**: The project will become an essential part of smart home systems, offering comprehensive indoor environment control in collaboration with other devices.
- Health Protection**: Automatic adjustment of indoor humidity to prevent health issues related to humidity, such as asthma and allergies.
- Special Applications**: Plays a vital role in special environments like storage of collectibles, protecting sensitive items.
#Contact information
ucfnhy0@ucl.ac.uk
