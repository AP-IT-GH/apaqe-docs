### PostgreSQL Docs

PostgreSQL is a relational database, it makes it easy to use relations between tables/entities. In the previous architecture sensors were stored in influx. We found this to be unhandy.

#### Sensors

Sensors are stored in the PostgreSQL.
```SQL
create table if not exists sensor(

    -- The sensor id is an unique identifier to connect data from the InfluxDB to the sensor that sent it.
    sensor_id serial not null unique,

    -- Sensors have a name to more easily recognise them.
    sensor_name text not null,

    -- Every sensor has an image.
    img_src text not null,

    -- This indicates when the sensor was created, this is the unix timestamp.
    creation_timestamp bigint not null,

    -- Groups are used to organize sensors, every sensor has one. The default is the group with id 1.
    group_id int not null default(1), -- default is de 'unassigned' groep
    
    -- Location of the sensor.
	lattitude bigint not null,
    longitude bigint not null,

    foreign key(group_id) references sensor_group(group_id)
);
```

#### Groups

Sensors can be put into a group to manage them more easily.

```SQL
create  table if not exists sensor_group (
    group_id serial not null unique,
    name text not null
);
```


#### Notifications

Different source can send notifications to users, for example a sensor sends data for the first time.

```SQL

create table if not exists notification(
	notification_id serial not null unique,

    -- Timestamp when the notification was created, this is in the unix timestamp.
	timestamp bigint not null,
	message text not null,

    -- severity: number from 1-3 (1 = check it out, 2 = semi critical, 3 = WAKE THE FUCK UP)
	severity int not null
);
```

#### AI forecast

No docs yet.
```SQL
create table if not exists AI_forecast(
    predicion int not null,
    date int not null
);
```

#### Default values
Every sensor is by default in the group with id 1, this is why we have to create this group at startup.

```SQL
-- This group gets the id 1.
insert into sensor_group(name) values ('unassigned');
```
