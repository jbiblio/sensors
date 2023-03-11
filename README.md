# House Setup
This is my setup for some sensors I'm running on our house.

The installation is not connected to the internet.

It consists of a Docker Compose configuration along with some configuration files required by some of the tools. The whole installation runs on a Raspberry Pi 4 from an NVMe.

The sensors are:
- some Zigbee sensors for temperature/humidity/pressure from Aquara
- a Vaillant heating system, based on a heat pump with a hot water reservoir
- some smart smart plugs from Gosund running Tasmota with power measurment.

All sensors dump their measurements via MQTT or Prometheus endpoint into an Influx2DB, which is than queried by Grafana.

## Zigbee sensors
To collect information from the Zigbee/Aqara sensors to InfluxDB2, we need a ConBee II USB adapter from Phoscon, as well as the ConBee software.

The installation is inspired by https://github.com/deconz-community/deconz-docker.

Connecting the Aquara sensors might be a bit tedious, but should not be a big problem. So_metimes they looses the connection, but can be reconnected via the ConBee UI.

ConBee is some kind of Home Automation Software on its own.

To export the values from ConBee, the `deconz-exporter` from https://github.com/KartoffelToby/deconz-exporter is needed. It publishes the values via Prometheus metrics endpoint.
I forked it into https://github.com/jbiblio/deconz-exporter to be able to update the Go SDK.

You must build the Docker image.

## Vaillant heating system
To be able to acquire values from a Vaillant system, you need to connect to its eBUS. I've got a USB connector from https://esera.de/Produkte/eBus/135/1-Wire-Hub-Platine.

The values from the eBUS are translated into JSON and pushed via MQTT by https://github.com/john30/ebusd.

## Visualization
As said before, all values are transmitted via MQTT.

They are pushed to [Mosquitto](https://mosquitto.org/), then collected by [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/), which pushes the values to [InfluxDB2](https://www.influxdata.com/products/influxdb/).

Then I use [Grafana](https://grafana.com/) for visualization.

## Installation
1. Clone https://github.com/jbiblio/deconz-exporter
2. Build Docker Image `docker build -t deconz-exporter`
3. Setup secrets: copy [.env.template](.env.template) to `.env` and fill values accordingly
4. Launch installation: `docker compose up`.

If anything is fine, you may want to setup startup on boot time. Consult the docs of Docker Compose to do so.

## Endpoints
- Grafana: http://localhost:3000
- ConBee: http://localhost
- ebusd output: http://localhost:8080/data
- InfluxDB: http://localhost:8086
- Mosquitto: http://localhost:1883

Adjust the host name or IP address to your needs. For example, the Tasmota devices pushes to mosquitto directly and must be configured for its host and port.

# TODOs
- Import more values from Vaillant
- Integrate weather sensors (Ecowitt)