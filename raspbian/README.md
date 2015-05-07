# Crackberry Pi software

![render](https://github.com/CrackberryPi/hardware/raw/master/images/cbpi.jpg "terrible render")

##Steps

Set up latest Rasbian

Install basic tools

Enable RTC

Program EEPROM

Enable Entropy generator

Enable screen

Set up IR tx/rx

Set up keyboard

Enable FM

###Set up Raspbian

Burn [latest Rasbian image](https://www.raspberrypi.org/downloads/) on uSD card

Boot

sudo apt-get update

sudo apt-get upgrade

sudo reboot

###Install basic tools

####WiringPi
sudo apt-get install git-core
git clone git://git.drogon.net/wiringPi
cd wiringPi
git pull origin
cd wiringPi
./build

Test:
gpio -v
gpio readall

###Enable RTC

sudo nano /etc/modules
add:
i2c-bcm2708

sudo nano /etc/rc.local
add:
modprobe i2c-dev
modprobe i2c:mcp7941x
echo mcp7941x 0x6f > /sys/class/i2c-dev/i2c-1/device/new-device

sudo reboot
install battery

sudo date -s "7 MAY 2015 21:27:00"