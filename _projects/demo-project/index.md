---
layout: post
title: Midnight Sun Battery box
description:
    Developing the battery box for midsun came with multiple setbacks and problems, since I was tring to build teh battery box with a more servicable outlook. The battery box included modules which function like hardrive trays. Additionally to reduce the scrap rate of our battery box I designed and tested hundreds of cell level fuses to get the right thickness. Lastly, to allow our driver to drive with piece of mind I created a mounting solution for the battery box which was validated with FEA to be able to withstand 5G of force.
skills: 
  - Structural analysis
  - 3D printing 
  - Spotwelding 
  - Carbon fiber manufacturing
  - Mold design 
  - Electrical simulation
  - Equipment testing 
  - Firmware development
  - Thermal simulation

main-image: /Car.jpg
---

---
# Header 1 
Used for the title (already generated automatically at the top)
## Header 2  
Use this for the header of each section
### Header 3 
Use this to have subsection if needed


## Embedding images 
### External images
main-image: /Cellfusetesing.jpg

You can put in multiple entries. All images will be at a fixed height in the same row. With smaller window, they will switch to columns.  

### Embeed images
{% include image-gallery.html images="project2.jpg" height="400" %} 
place the images in project folder/images then update the file path.   


## Embedding youtube video
The second video has the autoplay on. copy and paste the 11-digit id found in the url link. <br>
*Example* : https://www.youtube.com/watch?v={**MhVw-MHGv4s**}&ab_channel=engineerguy
{% include youtube-video.html id="MhVw-MHGv4s" autoplay= "false"%}
{% include youtube-video.html id="XGC31lmdS6s" autoplay = "true" %}

you can also set up custom size by specifying the width (the aspect ratio has been set to 16/9). The default size is 560 pixels x 315 pixels.  

The width of the video below. Regardless of initial width, all the videos is responsive and will fit within the smaller screen.
{% include youtube-video.html id="tGCdLEQzde0" autoplay = "false" width= "900px" %}  

<br>

## Adding a hozontal line
---

## Starting a new line
leave two spaces "  " at the end or enter <br>

## Adding bold text
this is how you input **bold text**

## Adding italic text
Italicized text is the *cat's meow*.

## Adding ordered list
1. First item
2. Second item
3. Third item
4. Fourth item

## Adding unordered list
- First item
- Second item
- Third item
- Fourth item

## Adding code block
```ruby
def hello_world
  puts "Hello, World!"
end
```

```python
import sys
import time
from PyQt6.QtWidgets import (
    QApplication, QMainWindow, QPushButton, QLabel,
    QVBoxLayout, QWidget
)
from PyQt6.QtCore import QTimer
import pyvisa
import openpyxl
from datetime import datetime


class ShuntLogger(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Shunt Voltage Logger")

        self.dmm = None
        self.timer = QTimer()
        self.timer.setInterval(100)  # 100 ms
        self.timer.timeout.connect(self.read_voltage)

        self.voltage_label = QLabel("Voltage: --- V")
        self.current_label = QLabel("Current: --- A")
        self.status_label = QLabel("Status: Idle")

        self.start_button = QPushButton("Start Logging")
        self.stop_button = QPushButton("Stop Logging")
        self.stop_button.setEnabled(False)

        layout = QVBoxLayout()
        layout.addWidget(self.voltage_label)
        layout.addWidget(self.current_label)
        layout.addWidget(self.status_label)
        layout.addWidget(self.start_button)
        layout.addWidget(self.stop_button)

        container = QWidget()
        container.setLayout(layout)
        self.setCentralWidget(container)

        self.start_button.clicked.connect(self.start_logging)
        self.stop_button.clicked.connect(self.stop_logging)

        self.rm = pyvisa.ResourceManager()
        self.wb = None
        self.ws = None
        self.start_time = None

    def start_logging(self):
        try:
            self.status_label.setText("Status: Connecting...")
            resources = self.rm.list_resources()
            agilent = [r for r in resources if "0x0957" in r]
            if not agilent:
                self.status_label.setText("Status: No Agilent multimeter found")
                return
            self.dmm = self.rm.open_resource(agilent[0])
            self.dmm.write("*RST")
            self.dmm.write("CONF:VOLT:DC 10")
            self.dmm.write("VOLT:DC:NPLC 0.02")

            self.wb = openpyxl.Workbook()
            self.ws = self.wb.active
            self.ws.title = "Shunt Voltage Log"
            self.ws.append(["Time (s)", "Voltage (V)", "Current (A)"])

            self.start_time = time.time()
            self.timer.start()
            self.start_button.setEnabled(False)
            self.stop_button.setEnabled(True)
            self.status_label.setText("Status: Logging")

        except Exception as e:
            self.status_label.setText(f"Status: Error - {e}")

    def stop_logging(self):
        self.timer.stop()
        self.start_button.setEnabled(True)
        self.stop_button.setEnabled(False)
        self.status_label.setText("Status: Saving")

        try:
            if self.wb:
                filename = f"shunt_log_{datetime.now().strftime('%Y%m%d_%H%M%S')}.xlsx"
                self.wb.save(filename)
                self.status_label.setText(f"Saved: {filename}")
        except Exception as e:
            self.status_label.setText(f"Error saving file: {e}")

        if self.dmm:
            try:
                self.dmm.close()
            except:
                pass

    def read_voltage(self):
        try:
            voltage = float(self.dmm.query("READ?"))
            if abs(voltage) < 0.001:
                voltage = 0.0

            current = voltage / 0.001667  # based on 50mV = 30A shunt
            elapsed_time = round(time.time() - self.start_time, 3)

            self.ws.append([elapsed_time, voltage, current])

            self.voltage_label.setText(f"Voltage: {voltage:.6f} V")
            self.current_label.setText(f"Current: {current:.2f} A")
        except Exception as e:
            self.status_label.setText(f"Error: {e}")
            self.timer.stop()


def main():
    app = QApplication(sys.argv)
    window = ShuntLogger()
    window.show()
    sys.exit(app.exec())


if __name__ == "__main__":
    main()

```

```javascript
let x = 1;
if (x === 1) {
  let x = 2;
  console.log(x);
}
console.log(x);

```

## Adding external links
[Wikipedia](https://en.wikipedia.org)


## Adding block quote
> A blockquote would look great if you need to highlight something


## Adding table 

| Header 1 | Header 2 |
|----------|----------|
| Row 1, Col 1 | Row 1, Col 2 |
| Row 2, Col 1 | Row 2, Col 2 |

make sure to leave aline betwen the table and the header


