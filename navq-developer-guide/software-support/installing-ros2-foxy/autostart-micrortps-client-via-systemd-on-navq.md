# Auto-start microRTPS client/agent on FMU/NavQ

## Creating a systemd service to auto-start the microRTPS agent on NavQ

Generate a start up script for the micrortps client under _/usr/local/bin_

```text
sudo nano /usr/local/bin/start_micrortps_agent.sh
```

with content

```text
#!/bin/bash
## startup script for micro_rtps_agent
## agent will communicate to FMUK66 via UDP
## FMUK66 IPv4 addr = 10.0.0.2 
##
## Author: Gerald Peklar <gerald.peklar@nxp.com>  

source /opt/ros/foxy/setup.bash
source ~/px4_ros_com_ros2/install/setup.bash

# Comment out the line that you are not using:

# If you're using T1 Ethernet communication:
micrortps_agent -t UDP -i 10.0.0.2

# If you're using UART communication over the UART3 port:
micrortps_agent -d /dev/ttymxc2 -b 921600
```

Save the file and exit nano.  
Make the file executable

```text
sudo chmod +x /usr/local/bin/start_micrortps_agent.sh
```

Generate a systemd service file to start the startup script at boot

```text
sudo nano /etc/systemd/system/micrortps_agent.service
```

with content

```text
[Unit]
Description=PX4 micrortps service
After=network.target

[Service]
Restart=always
TimeoutStartSec=10
User=navq
Group=navq
WorkingDirectory=~
ExecStart=/usr/local/bin/start_micrortps_agent.sh

[Install]
WantedBy=multi-user.target
```

Save the file and exit nano.  
Check if the process starts 

```text
sudo systemctl start micrortps_agent.service
sudo systemctl status micrortps_agent.service
```

You should see an state _active \(running\),_ quit with &lt;q&gt;  
Enable the systemd service file finally to be active at boot

```text
sudo systemctl enable micrortps_agent.service
```

##  Auto-start the microRTPS client on the FMU

{% hint style="info" %}
To run fastRTPS over Ethernet with NavQ board the [RDDRONE-T1ADAPT](../../../navq-add-on-modules/100baset1-2-wire-automotive-ethernet-media-converter.md) is needed.
{% endhint %}

### Building PX4 with microRTPS

{% hint style="info" %}
You will need a Linux VM or computer to complete this step.
{% endhint %}

In order to use the microRTPS client on NavQ, you'll need to build PX4 with the `_rtps` tag for the fmuk66-v3 build target. To do this, you will need to have both the FastRTPS and Fast-RTPS-Gen packages installed. You can just follow the previous guide on your Linux development VM or computer.

Once you have successfully installed those two packages, you can navigate to your cloned PX4 repository and run the following:

```text
$ make nxp_fmuk66-v3_rtps
```

### Flashing your FMU with the updated binary

You will need to flash your FMU with the updated RTPS binary. If you don't know how to do this yet, follow the guide here:

{% embed url="https://nxp.gitbook.io/hovergames/developerguide/program-software-using-debugger" %}

### Creating a startup file on the SD card

To make the microRTPS client start at boot on the FMU, you will need to have an SD card inserted. On your SD card, make a file at /etc/extras.txt and insert one of the following options:

```text
set +e
# For T1 Ethernet communication:
micrortps_client start -t UDP -i <NavQ_IP_Address>

# For UART communication over the TELEM port:
micrortps_client start -d /dev/ttyS4 -b 921600

# For UART communication over the IR/TELM2 port:
micrortps_client start -d /dev/ttyS1 -b 921600
set -e
```

{% hint style="info" %}
Calling _**set +e**_  at beginning / **set -e** at end is needed to prevent from boot errors.   
Further details can be found on [https://dev.px4.io/master/en/concept/system\_startup.html\#replacing-the-system-startup](https://dev.px4.io/master/en/concept/system_startup.html#replacing-the-system-startup)
{% endhint %}

