# Home Assistant
My forray into Home Assistant blueprints (and more?).

## Blueprints

### Certificate Expiry Checker
[![Import Certificate Expiry Checker blueprint to your Home Assistant.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fbriped%2Fhomeassistant%2Fmaster%2Fblueprints%2Fcertificate_expiry_checker.yaml)
![certificate_expiry_checker](/blueprints/certificate_expiry_checker.jpg)

This blueprint depends on the [Certificate Expiry integration](https://www.home-assistant.io/integrations/cert_expiry/).

The blueprint will return an array of dictionaries, which can be referenced using `{{items}}`. This way you can gain greater control over how the output should be handled.

An example of the returned data:
```json
[
  {
    "host": "portainer.example.com",
    "port": "9443",
    "relative_time": "9 days",
    "entity_id": "sensor.cert_expiry_timestamp_portainer_example_com_9443",
    "state": "2023-02-24T20:24:44+00:00"
  },
  {
    "host": "proxmox.example.com",
    "port": "8006",
    "relative_time": "1 day",
    "entity_id": "sensor.cert_expiry_timestamp_proxmox_example_com_8006",
    "state": "2023-02-16T23:06:53+00:00"
  },
  {
    "host": "www.example.com",
    "port": 443,
    "relative_time": "9 days",
    "entity_id": "sensor.cert_expiry_timestamp_www_example_com",
    "state": "2023-02-24T20:19:17+00:00"
  }
]
```

An example on how to configure HTML e-mail notifications that will contain a list of certificates about to expire:
```yaml
service: notify.smtp_gmail
data:
  title: Certificates expiring soon
  message: |
    The following monitored certificates 
      will expire soon:
  data:
    html: |
      <!DOCTYPE html>
      <html>
        <head>
          <meta charset="UTF-8">
          <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>Certificates expiring soon</title>
        </head>
        <body>
          <p>The following monitored certificates will expire soon:</p>
          <ul>
          {% for item in items %}
            <li><a href="https://{{item.host}}:{{item.port}}">{{item.host}}:{{item.port}}</a> expires in {{item.relative_time}}</li>
          {% endfor %}
          </ul>
        </body>
      </html>
```

### Device Availability Checker
[![Import Device Availability Checker blueprint to your Home Assistant.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fbriped%2Fhomeassistant%2Fmaster%2Fblueprints%2Fdevice_availability_checker.yaml)
![certificate_expiry_checker](/blueprints/device_availability_checker.jpg)

The blueprint will return an array of dictionaries, which can be referenced using `{{items}}`. This way you can gain greater control over how the output should be handled.

An example of the returned data:
```json
[
  {
    "device_id": "bb0074342dc93760d18ae40bb454f9ec",
    "device_name": "LUMI lumi.sensor_magnet",
    "area_id": "entrance",
    "state": "unavailable"
  },
  {
    "device_id": "0a300fdf5cdcc8e7d2e2473d003f9585",
    "device_name": "LUMI lumi.weather",
    "area_id": "bedroom",
    "state": "unavailable"
  },
  {
    "device_id": "6586d15e7134da404836203e165b7b9c",
    "device_name": "LUMI lumi.weather",
    "area_id": "living_room",
    "state": "unavailable"
  },
  {
    "device_id": "5e20568a620d5b500811c86568c5cee8",
    "device_name": "RGB Bulb",
    "area_id": "closet",
    "state": "unavailable"
  },
  {
    "device_id": "2563429dfb3eb402007748d833f2dfd4",
    "device_name": "LUMI lumi.weather",
    "area_id": "kitchen",
    "state": "56"
  }
]
```

An example on how to configure HTML e-mail notifications that will contain a list of unavailable or low battery devices:
```yaml
service: notify.smtp_gmail
data:
  title: Unavailable or low battery devices
  message: |
    The following devices are low on battery or unavailable:
  data:
    html: |
      <!DOCTYPE html> <html>
        <head>
          <meta charset="UTF-8">
          <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>Unavailable or low battery devices</title>
        </head>
        <body>
          <p>The following devices are low on battery or unavailable:</p>
          <ul>
          {% for item in items %}
            <li><a href="https://homeassistant.example.com/config/devices/device/{{item.device_id}}">{{item.device_name}}</a><br />
              <ul>
                <li>Area: <a href="https://homeassistant.example.com/config/areas/area/{{item.area_id}}">{{area_name(item.area_id)}}</a></li>
                <li>State: {{item.state}}</li>
              </ul>
            </li>
          {% endfor %}
          </ul>
        </body>
      </html>
```
