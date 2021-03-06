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
./build

gpio -v
gpio readall
```

####I2C

```
sudo apt-get install i2c-tools
sudo apt-get install python-smbus
sudo adduser pi i2c
sudo reboot
```

Add to /etc/modules
```
i2c-bcm2708
i2c-dev
```

Add to /etc/rc.local
```
modprobe i2c-dev
```

###Enable RTC

Install battery

Check if RTC shows up at 0x6f using
```
i2cdetect -y 1
```

Add to /etc/rc.local
```
modprobe i2c:mcp7941x
echo mcp7941x 0x6f > /sys/class/i2c-dev/i2c-1/device/new-device
```

Reboot

```
sudo date -s "7 MAY 2015 21:27:00"
```

###Program EEPROM

WIP, see [RPi HAT programming](https://github.com/raspberrypi/hats/tree/master/eepromutils)

Add to /boot/config.txt
```
dtparam=i2c_arm=on
dtparam=i2c_vc=on
```

```
mkdir eeprom
cd eeprom
wget http://github.com/raspberrypi/hats/tree/master/eepromutils/eepdump.c
wget http://github.com/raspberrypi/hats/tree/master/eepromutils/eepflash.sh
wget http://github.com/raspberrypi/hats/tree/master/eepromutils/eepmake.c
wget http://github.com/raspberrypi/hats/tree/master/eepromutils/eeprom_settings.txt
wget http://github.com/raspberrypi/hats/tree/master/eepromutils/eeptypes.h
```

###Entropy generator testing

WIP

Sudo nano random.py
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

quasi programming
for loop
	a = GPIO.input(random_output)
	b = GPIO.input(random_output)
	if a!=b append a to output.txt

GPIO.cleanup()

###IR tx/rx

```
sudo apt-get install apache2 php5 mysql-client mysql-server tomcat6 vsftpd
sudo apt-get install lirc

```

Add to /etc/modules
```
lirc_dev
lirc_rpi gpio_in_pin=23 gpio_out_pin=22
```

Change /etc/lirc/hardware.conf to match
```
########################################################
# /etc/lirc/hardware.conf
#
# Arguments which will be used when launching lircd
LIRCD_ARGS="--uinput"

# Don't start lircmd even if there seems to be a good config file
# START_LIRCMD=false

# Don't start irexec, even if a good config file seems to exist.
# START_IREXEC=false

# Try to load appropriate kernel modules
LOAD_MODULES=true

# Run "lircd --driver=help" for a list of supported drivers.
DRIVER="default"

# usually /dev/lirc0 is the correct setting for systems using udev
DEVICE="/dev/lirc0"
MODULES="lirc_rpi"

# Default configuration files for your hardware if any
LIRCD_CONF=""
LIRCMD_CONF=""
########################################################
```


###TCA8424

import RPi.GPIO as GPIO
import smbus
import time

bus = smbus.SMBus(1)
addr = 0x3B

bus.write_byte_data(addr, 0x01, 0x00)


KB_INT = 20
GPIO.setmode(GPIO.BCM)
GPIO.setup(KB_INT, GPIO.IN, pull_up_down=GPIO.PUD_UP)

x = bus.read_byte_data(addr,0x30)
print x


GPIO.wait_for_edge(KB_INT, GPIO.FALLING)
print "ping"

GPIO.cleanup()

