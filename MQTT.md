# MQTT data synchroniser

### Content
1. [Usecase](#usecase)
2. [Env variabels](#env-variabels)
3. [Debug mode](#debug-mode)

### Usecase

This docker container connects an MQTT broker with an influxdb. It subscribes to the given `MQTT_DATA_TOPIC` or to the default topic `data`.
It expects any JSON structure that contains a `device_id` key where the value is an int and the other keys have to be one of the allowed keys (see below).

`device_id` : this is an unique identifier for an MQTT client, multiple clients should NOT be using the same identifier (this is given by the frontend).  
The point name of the influx measurement is the `device_id` as a string

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
    "PM10",
    "PM2_5",
    "voltage",
    "current",
    "power",
    "percentage",
    "TVOC",
    "nox"
]
```

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
//      device_id:      5    (int field)
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
//      device_id:      5    (int field)
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
//      device_id:      5   (int field)
//      temperature:    30  (int field)
//      rain:           420 (int field)
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

When debugging you can use an [mqtt cli tool](https://mqttx.app/cli) to subscribe to the debug topics.
