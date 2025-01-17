## InfluxDB Docs

### Use Case

We need a database to store data that is sent by IOT-devices. When deciding what database to use we
eventually chose for Influx because of the next reasons.

* Lots of writes, we write data more often than we read from it.
* No relations, the data that gets sent is really simple and doesn't have any relations.


### Queries

The queries InfluxDB expects are not SQL. The queries are really functional and differ a lot from SQL. Here is an example that is being used [here](https://gitlab.apstudent.be/nox/znz-infra/-/blob/dev/src/fastapi/routers/statistics.py?ref_type=heads#L39) by the API.
```
from(bucket: "bucket-name")
 |> range(start: -2d)
  |> filter(fn: (r) => r["_measurement"] == "1")
  |> aggregateWindow(every: 1d, fn: last, createEmpty: true)
  |> yield(name: "last")
```
This takes all the values from the last 2 days, filters out all measurments where the id is not the same as the given id. Next it aggregates the all the values in ranges of 1 day. This gives back two windows because of the range. Next it yields the last values of the two windows.


### Structure

InfluxDB measurements are really flexible and don't have much requirements (1). The only requirement is that the name of the measurement is the `device_id` of the device. This id is used to identify what measurements are from what device. Different devices shoud NOT use the same id in the InfluxDB. Measurement name are stings of text, the id's should be text not numbers.  

Example InfluxDB point:
```
Measurement name: "1"

Data:
"temperature" : 5,
"humidity" : 600
```

In the infrastructure we use [InfluxDB in Docker](https://hub.docker.com/_/influxdb).
