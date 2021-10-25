# Torque API to MQTT
Listens to events from Torque App and publishes to MQTT Message or InfluxDB 2.0

## How to use

### Prepare configuration files

#### devices.json
Describes the relation between an email configured in Torque App to a device name (as you would like it to be called)
```json
{
    "email@email.com": "Name of car"
}
```

#### mqtt.json

MQTT Broker configuration
```json
{
    "host": "127.0.0.1",
    "port": 1883,
    "username": "user",
    "password": "password",
    "clientId": "TorqueAPI",
    "topic": "torque/device/status"
}
```

#### influxdb.json

InfluxDB configuration
```json
{
    "host": "127.0.0.1",
    "port": 8086,
    "protocol": "http",
    "bucket": "torque",
    "organization": "org",
    "token": "token",
    "measurement": "device"
}
```

#### memory.json

Memory configuration
```json
{
    "maximumInMemory": 10000,
    "flushInterval": 60,
    "outputDirectory": "/data"
}
```

#### Run docker container using the following Docker Compose
```yaml
  torque-api:
    image: eladbar/torque-api:latest
    restart: unless-stopped
    hostname: torque-api
    container_name: torque-api
    ports:
      - 8128:8128
    volumes:
      - ./config:/config
      - /etc/localtime:/etc/localtime:ro
      - /etc/timezone:/etc/timezone:ro

```

#### Setup Tourqe App

In *Settings -> Data Logging & Upload*

Under the *Logging Preferences* header:

Touch *Select what to log*, activate the menu in the upper right, and select *Add PID to log*.
Select items of interest.

Under the *Realtime Web Upload* header:

Check *Upload to web-server*.
- Enter http://HOST:PORT/api/torque as the *Web-server URL*, where HOST and PORT are your externally accessible Torque API HTTP host and port. 
- Enter an email address in User Email Address (Same email address as in the `devices.yaml`)
- Note that it is recommanded to use HTTP with Torque App

## Endpoints

Endpint | Method | Description
---|---|---|
/ | GET | Readme | 
/api/torque | GET | Report statistics (For Torque App) | 
/api/torque/raw | GET | Raw event's data, Available when `memory.json` is configured |
/api/torque/data | GET | Processed events, Available when `memory.json` is configured |
/api/torque/sensors | GET | List all available sensors | 
/api/debug | GET | Get debug mode | 
/api/debug | POST | Set debug mode | 
/api/debug | DELETE | Stop debug mode | 

## MQTT Messages
According to the topic configured in `mqtt.json` 

## InfluxDB Event
According to the topic configured in `measurement.json` 