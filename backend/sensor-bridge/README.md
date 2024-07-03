# Sensor Bridge
## Goal
## How is this achieved
> Add design diagram 
## How to use/replicate
## Detail explanation 

### Content
> Dit is niet nodig, er is een instelling die dit automatisch genereerd

> Gebruik ook de juiste niveaus
1. [Usecase](#usecase)
2. [Allowed Keys](#allowed-keys)
3. [Env variabels](#env-variabels)
4. [Debug mode](#debug-mode)

### Usecase


> Dit stukje tekst is ineens te gedetaileerd. Het is zeer moeilijk te volgen. Betere intro nodig. Indien nodig gooi uw tekst door chatgpt.

This docker container connects an MQTT broker with an lnfluxDB. It subscribes to the given `MQTT_DATA_TOPIC` or to the default topic `data`.
It expects any JSON structure that contains a `device_id` key where the value is an int and the other keys have to be one of the allowed keys (see below).

`device_id` : this is an unique identifier for an MQTT client, multiple clients should NOT be using the same identifier (this is given by the frontend).  
The point name of the InfluxDB measurement is the `device_id` as a string

    f_pm1p0, f_pm2p5, f_pm4p0, f_pm10p0, f_hum, f_temp, f_voc, f_nox



For example:
```json
{
    "device_id" : 5,
    "temperature": 1,
    "rain": 3.14,
}
// This generates the following point
// Measurement name: "5"
// Fields:
//      temperature:    1    (int field)
//      rain:           3.14 (float field)
```

```json
{
    "device_id" : 5,
    "temperature": 1,
    "rain": 3.14,
}
// This generates the following point
// Measurement name: "5"
// Fields:
//      temperature:    1    (int field)
//      rain:           3.14 (float field)
```


Any nested structure gets unnested and the key is discarded
For example:
```json
{
    "device_id" : 5,
    "foo" : {
        "temperature" : 30,
        "bar" : {
            "rain" : 420
        }
    }
}
// This generates the following point
// Measurement name: "5"
// Fields:
//      temperature:    30  (int field)
//      rain:           420 (int field)
```

### Allowed Keys

All the allowed keys.
```json
[
        "temperature",
        "humidity",
    "wind_avg",
    "wind_max",
    "wind_direction",
    "rain",
    "pressure",
    "CO2",
        "pm1p0",
        "pm2p5",
        "pm4p0",
        "pm10p0",
    "voltage",
    "current",
    "power",
    "percentage",
        "tvoc",
        "nox"
]
```


### Env variabels
The following env variabels are expected by this program
```env
INFLUXDB_ORG=       // the org of the influxdb
INFLUXDB_BUCKET=    // bucket to put the data in
INFLUXDB_URL=
INFLUXDB_TOKEN=     // API token from influx webpage

MQTT_BROKER=
MQTT_USERNAME=
MQTT_PASSWORD=

MQTT_DATA_TOPIC=    // topic to subscribe to (this field is optional and defaults to `data`)
OVERRIDE_FILTER=    // to override the allowed keys filter
```

### Debug mode
This service sends debug info to the topic `debug`, on startup and on error. When publishing data the publisher can also add a `debug_mode` variabel and set this to `true`. When this is `true` this service sends specific debug info to the topic `debug/{device_id}` where the `{device_id}` is the id of your device that should be in the JSON structure.  
Note that this field is optional and defaults to `false`

example:
```json
{
    "device_id" : 5,
    "debug_mode": true,
}
```

> Waarom niet de standaard tools van mosquitto? Leg ook uit hoe je dit doet? 

When debugging you can use an [mqtt cli tool](https://mqttx.app/cli) to subscribe to the debug topics.
