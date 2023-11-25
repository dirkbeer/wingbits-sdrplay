***wingbits-sdrplay***

1. Setup wingbits

2. Setup the SDRplay API
```bash
wget https://www.sdrplay.com/software/SDRplay_RPi_Scripts_v0.3.zip
unzip SDRplay_RPi_Scripts_v0.3.zip -d SDRplay_RPi_Scripts
cd SDRplay_RPi_Scripts
./1installAPI.sh
./2buildSoapy.sh
sudo reboot
SoapySDRUtil --find="driver=sdrplay"
SoapySDRUtil --probe="driver=sdrplay"
```

3. Install SDRplay's dump1090
```bash
git clone https://github.com/SDRplay/dump1090.git
cd dump1090
nano Makefile
#CFLAGS+= -O2 -g -Wall -Werror -W
CFLAGS+= -O2 -g -Wall -W
SDRPLAY=1 make dump1090
```

4. Run dump1090
```bash
./dump1090 --dev-sdrplay --rsp2-antenna-portA --measure-noise --net --net-ro-port 30002 --quiet &
```
5. Modify the readsb configuration
```bash


