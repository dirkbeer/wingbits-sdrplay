***wingbits-sdrplay***

**Initial setup and testing**

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
#RECEIVER_OPTIONS="--device 0 --device-type rtlsdr --gain -10 --ppm 0"
RECEIVER_OPTIONS="--net-connector 127.0.0.1,30002,raw_in"
```
6. Restart readsb
```bash
sudo systemctl restart readsb
```

**Permanent install**

7. Install dump1090
```bash
sudo cp ./dump1090 /usr/bin/
```

7. Create a service script to start dump1090
```bash
sudo nano /lib/systemd/system/dump1090.service
```
```bash
# dump1090 service for systemd

[Unit]
Description=dump1090 ADS-B receiver
Documentation=https://github.com/SDRplay/dump1090
Wants=network.target
After=network.target sdrplay.service

[Service]
# You might want to create an EnvironmentFile if you have many variable options
EnvironmentFile=/etc/default/dump1090
User=readsb
RuntimeDirectory=readsb
RuntimeDirectoryMode=0755
ExecStart=/usr/bin/dump1090 $RECEIVER_OPTIONS --quiet
Type=simple
Restart=always
RestartSec=15
StartLimitInterval=60
StartLimitBurst=5
Nice=-5

[Install]
WantedBy=default.target
```

8. Create a configuration file for the service
```bash
sudo nano /etc/default/dump1090
```
```bash
# dump1090 configuration
# This is sourced by /etc/systemd/system/default.target.wants/dump1090.service as
# daemon startup configuration.

RECEIVER_OPTIONS="--dev-sdrplay --rsp2-antenna-portA --measure-noise --net --net-ro-port 30002"
```

9. Install the service and check that it is running
```bash
sudo systemctl enable dump1090.service
sudo systemctl start dump1090.service
sudo systemctl status dump1090
sudo journalctl -fu dump1090.service
```

10. Reboot
