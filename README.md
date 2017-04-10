# govcloudair [![Build Status](https://ci.vmware.run/api/badges/augabet/govcloudair/status.svg)](https://ci.vmware.run/augabet/govcloudair) [![Build Status](https://travis-ci.org/augabet/govcloudair.svg?branch=master)](https://travis-ci.org/augabet/govcloudair) [![Coverage Status](https://coveralls.io/repos/augabet/govcloudair/badge.svg?branch=master&service=github)](https://coveralls.io/github/augabet/govcloudair?branch=master) [![GoDoc](https://godoc.org/github.com/augabet/govcloudair?status.svg)](http://godoc.org/github.com/augabet/govcloudair) [![Join the chat at https://gitter.im/augabet/govcloudair](https://badges.gitter.im/augabet/govcloudair.svg)](https://gitter.im/augabet/govcloudair?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
This repo provides the `govcloudair` package which offers an interface to the vCloud Air 5.6 and 5.7 API and vCloud Director 5.5 API.

It serves as a foundation for a project currently in development, there are plans to make it a general purpose API in the future. The `govcloudair` package is used by the Terraform provider for vCloud Director.

The API is currently under heavy development, its coverage is extremely limited at the moment.

The bindings now support both Subscription and On-demand accounts and vCloud Director 5.5

### Example ###

```go
package main

import (
        "fmt"
        "net/url"
        "os"

        govcd "github.com/augabet/govcloudair"
)

type Config struct {
        User     string
        Password string
        Org      string
        Href     string
        VDC      string
        Insecure bool
}

func (c *Config) Client() (*govcd.VCDClient, error) {
        u, err := url.ParseRequestURI(c.Href)
        if err != nil {
                return nil, fmt.Errorf("Unable to pass url: %s", err)
        }

        vcdclient := govcd.NewVCDClient(*u, c.Insecure)
        org, vcd, err := vcdclient.Authenticate(c.User, c.Password, c.Org, c.VDC)
        if err != nil {
                return nil, fmt.Errorf("Unable to authenticate: %s", err)
        }
        vcdclient.Org = org
        vcdclient.OrgVdc = vcd
        return vcdclient, nil
}

func main() {
  config := Config{
                User:     "Username",
                Password: "password",
                Org:      "vcd org",
                Href:     "vcd api url",
                VDC:      "vcd virtual datacenter name",
        }

  client, err := config.Client() // We now have a client
  if err != nil {
      fmt.Println(err)
      os.Exit(1)
  }
  fmt.Printf("Org URL: %s\n", client.OrgHREF.String())
}
```

