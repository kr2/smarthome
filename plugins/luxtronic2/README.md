# Luxtronic2

# Requirements
This plugin has no requirements or dependencies.

# How it works
At init it tries to connect to the device. If successful and if only one parameter, attribute or calculated is configured it reads the entire block. E.g. if one parameter is configured for reading, it reads the entire parameter block every 'cycle', but not the other ones. That’s by design of the device.
If the connection is lost or if the max number of timeouts ('max_timeouts') is reached it tries to reconnect every 'reconnect_cycle'.

# Configuration

## plugin.conf
<pre>
[luxtronic2]
    class_name = Luxtronic2
    class_path = plugins.luxtronic2
    host = 192.168.0.123
    # port = 8888
    # cycle = 300
    # reconnect_cycle = 60
    # max_timeouts = 10
</pre>

### Attributes
  * `host`: specifies the hostname of your heating server.
  * `port`: if you want to use a nonstandard port.
  * `cycle`: read cycle time in seconds.
  * `reconnect_cycle`: cycle time for reconnect attempts in seconds.
  * `max_timeouts`: max number of timeouts before connection is considered lost.

## items.conf

Each heating controlled with a Luxtronic 2.0 controller has different things which can controlled or different information which can be received. 
The reason for that is that every heating system can have special modules installed or mounted to the system itself.
To have the most generic way to read state informations or change values this plugin is based on the index values of the ouput from your heating.
There are three main sections:
  * 'parameter': all parameters which are needed to control the heating (parameters can be changed with this plugin, so be careful with them). Attention: because the protocol is not well documented, not every parameter and its function is knwon.
  * 'attribute': I'm not sure what they are for. In the Java Applet on the webserver of the heating itself it is used as visibility and it seems that these are only boolean flags, maybe to control which parameter is realy needed.
  * 'calculated': returns calculated information. For example the current state or the time the heating was running until now.

For all of the following items.conf attributes you have to define the right index for the output from your heating.

### lux2
Special post processed values from the calculated section for the most important information (read-only).

Processed indexes are:
119: current state of the heating as string
10, 11, 12, 15, 19, 20, 151, 152: original float values encoded as integer so they're just divided by ten.

### lux2_p
Defines a mapping to a parameter (remember, parameters are read- and writeable). All parameter are integer (numbers).

### lux2_a
Defines a mapping to a attribute (read-only). All attribute values are bytes (numbers).

### lux2_c
Defines a mapping to a calculated value (read-only). All calculated values are integer (numbers).

### lux2_unpack
Python lambda function. Called before the value is sent towards the device.

### lux2_pack
Called before the value is written to the smarthome item.

<pre>
[heating]
    [[temp_outside]]
        type = num
        lux2 = 10
    [[state_numeric]]
        type = num
        lux2_c = 119
        lux2_unpack = lambda x: x/10.0
    [[state]]
        type = str
        lux2 = 119
</pre>


#### Pack Unpack

lux_unpack and lux_pack require a standard Python lambda function. Pack is called before the value is sent towards the device, unpack for the other way around (device to Smarthome).



