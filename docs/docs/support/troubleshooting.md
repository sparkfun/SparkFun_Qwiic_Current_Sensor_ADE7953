# Troubleshooting

## Channel A and Channel B Direct Connection

The Non-Invasive Current Sensor board is designed to work with a current transformer with a CT ratio of 2000:1 connected to the 3.5mm TRRS jack. The through-hole connections to Channel A and B do not have any extra current transformation ratio integrated and operate at a 1:1 ratio. As a result, AC loads connected to these pins should not exceed **100mA**.

## ADE7953 SPI Interface

The Non-Invasive Current Sensor board design only supports the ADE7953's I<sup>2</sup>C and UART interfaces as it does not break out the SCLK and CS pins for communicating over SPI. While not supported by SparkFun, if you need to use the ADE7953 over SPI you can adjust the SCLK and CS jumpers to enable SPI and then carefully solder to the SCLK and CS solder jumper pads to make those connections. 

## Support

If you need technical assistance or more information on a product that is not working as you expected, we recommend heading on over to the [SparkFun Technical Assistance](https://www.sparkfun.com/technical_assistance) page for some initial troubleshooting.

If you need technical assistance and more information on a product that is not working as you expected, visit the [SparkFun Community](https://community.sparkfun.com). The community forums are monitored by the SparkFun Technical Support Team and the SparkFun community as a whole. If this is your first visit to our forum, you'll need to create a [Forum Account](https://forum.sparkfun.com/ucp.php?mode=register) to post questions.

        