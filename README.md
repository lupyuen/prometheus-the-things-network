# Prometheus Configuration for The Things Network (MQTT)

To ingest MQTT Messages from The Things Network into Prometheus...

```cmd
go get github.com/hikhvar/mqtt2prometheus

## For macOS and Linux:
cd %GOPATH%\src\github.com\hikhvar\mqtt2prometheus

## For Windows:
cd %GOPATH%\src\github.com\hikhvar\mqtt2prometheus

go build ./cmd

go run ./cmd -log-level debug -config ttn-mqtt.yaml
```

We should see...

```text
2021-10-15T18:25:11+08:00       info    mqttclient/mqttClient.go:20  Connected to MQTT Broker   
2021-10-15T18:25:11+08:00       info    mqttclient/mqttClient.go:21  Will subscribe to topic  {"topic": "#"}
2021-10-15T18:25:11+08:00       debug   web/tls_config.go:191        {"level": "info", "msg": "TLS is disabled.", "http2": false}
```
