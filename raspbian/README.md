# Crackberry Pi software

![render](https://github.com/CrackberryPi/hardware/raw/master/images/cbpi.jpg "terrible render")

##Steps

* Set up latest Rasbian
* Install basic tools
* Enable RTC
* Program EEPROM
* Entropy generator testing
* Enable screen
* Set up IR tx/rx
* Set up keyboard
* Enable FM

###Set up Raspbian

Burn [latest Rasbian image](https://www.raspberrypi.org/downloads/) on uSD card

Expand rootsystem

Advanced options > Enable I2C

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo reboot
```

###Install basic tools

####WiringPi
```
sudo apt-get install git-core
git clone git://git.drogon.net/wiringPi
cd wiringPi
git pull origin
cd wiringPi
./build

gpio -v
gpio readall
```

####I2C

```
sudo apt-get install i2c-tools
sudo apt-get install python smbus
sudo adduser pi i2c
sudo reboot
```

###Enable RTC

Install battery

Check if RTC shows up at 0x6f using
```
i2cdetect -y 1
```

Add to /etc/modules
```
i2c-bcm2708
i2c-dev
```

Add to /etc/rc.local
```
modprobe i2c-dev
modprobe i2c:mcp7941x
echo mcp7941x 0x6f > /sys/class/i2c-dev/i2c-1/device/new-device
```

Reboot

```
sudo date -s "7 MAY 2015 21:27:00"
```

###Program EEPROM

WIP

###Entropy generator testing

Open random.py
```
import RPi.GPIO as GPIO

stepup_enable = 16
random_power = 21
random_output = 26

GPIO.setmode(GPIO.BCM)
GPIO.setup(stepup_enable, GPIO.OUT)
GPIO.setup(random_power, GPIO.OUT)
GPIO.setup(random_output, GPIO.IN)

GPIO.output(random_power, GPIO.HIGH)
GPIO.output(random_output, GPIO.HIGH)

for loop
GPIO.input(random_output)

normalize to make noise white

GPIO.cleanup()
