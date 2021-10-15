# Prometheus Configuration for The Things Network (MQTT)

To ingest The Things Network MQTT Messages into Prometheus...

```cmd
go get github.com/hikhvar/mqtt2prometheus

## For macOS and Linux:
cd %GOPATH%\src\github.com\hikhvar\mqtt2prometheus

## For Windows:
cd %GOPATH%\src\github.com\hikhvar\mqtt2prometheus

go build ./cmd

go run ./cmd -log-level debug -config ttn-mqtt.yaml
```
