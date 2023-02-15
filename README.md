# Home Assistant
My forray into Home Assistant blueprints (and more?).

## Blueprints

### Certificate Expiry Checker
[![Import Certificate Expiry Check blueprint to your Home Assistant.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fbriped%2Fhomeassistant%2Fmaster%2Fblueprints%2Fcertificate_expiry_checker.yaml)
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
