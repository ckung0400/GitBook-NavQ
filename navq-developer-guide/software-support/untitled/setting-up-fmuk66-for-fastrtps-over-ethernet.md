# Setting up FMUK66 for fastRTPS over Ethernet

{% hint style="info" %}
To run fastRTPS over Ethernet with NavQ board the [RDDRONE-T1ADAPT](../../../navq-add-on-modules/100baset1-2-wire-automotive-ethernet-media-converter.md) is needed.
{% endhint %}

1. Update PX4 build environment to latest status and build the code with _`make nxp_fmuk66-v3_rtps`_
2. upload the generated _nxp\_fmuk66-v3\_rtps_ FW into FMUK66.
3. Prepare the FMU's SD card by adjusting /etc/extras.txt file. This will autostart the fastRTPS communication at boot time. Add the following line in to _/etc/extras.txt_  _`micrortps_client start -t UDP -i 10.0.0.3`_

Assuming that NavQ is running with IP 10.0.0.3 

