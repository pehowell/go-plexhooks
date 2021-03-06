# Plex Hooks

[![Go Report Card](https://goreportcard.com/badge/github.com/acamilleri/go-plexhooks)](https://goreportcard.com/report/github.com/acamilleri/go-plexhooks)

Library to listen [Plex Webhook Events](https://support.plex.tv/articles/115002267687-webhooks/) and execute actions.

**Plex Webhooks require a Plex Pass subscription**

## Installation

Use the library in your own app to register your actions.
```bash
go get github.com/acamilleri/go-plexhooks
```

## Usage

You can check a real world example with [myplexhooks](https://github.com/acamilleri/myplexhooks) repository.

Example:
```golang
package main

import (
    "net"

    "github.com/sirupsen/logrus"
    "github.com/acamilleri/go-plexhooks"
    "github.com/acamilleri/go-plexhooks/plex"
)

type MyActionOnMediaPlay struct{}

func (a *MyActionOnMediaPlay) Name() string {
	return "MyActionOnMediaPlay"
}

func (a *MyActionOnMediaPlay) Execute(event plex.Event) error {
	fmt.Printf("I'm print a message when i received a MediaPlay event")
	return nil
}

func main() {
	listenAddr, err := net.ResolveTCPAddr("tcp4", "127.0.0.1:8080")
	if err != nil {
		panic(err)
	}

	actions := plexhooks.NewActions()
	actions.Add(plex.MediaPlay, &MyActionOnMediaPlay{})

	app := plexhooks.New(plexhooks.Definition{
		ListenAddr: listenAddr,
		Actions:    actions,
		Logger: plexhooks.LoggerDefinition{
			Level:     logrus.InfoLevel,
			Formatter: &logrus.JSONFormatter{},
		},
	})
    
    err := app.Run()
    if err != nil {
        panic(err)
    }
}
```

### Metrics

The Run function running a metrics handler to have some metrics about the
events handler  and actions executed.

Metrics are expose at `/metrics` path in [Prometheus](https://prometheus.io/) format.

You can find all metrics [available here](metrics.go)

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.
