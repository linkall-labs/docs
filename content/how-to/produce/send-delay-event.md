---
title: Use Delay Event
---

# Send an event with delay using the CloudEvents SDK

The following document will teach you how to build a simple producer with a timer to delay an Event. 

## Operations

**Prerequisites**

To send events to Vanus, you must meet the following prerequisites:
1. Have a running [Vanus](/vanus-open-source/installation) cluster.
2. Have [vsctl](/how-to/vsctl).
3. Have created an [Eventbus](https://github.com/vanus-labs/docs/blob/main/concepts/eventbus.md) named quick-start.
4. Have exported the environment variable by running this command: `export VANUS_GATEWAY=127.0.0.1:8080`.

> We also provide an interactive [Kubernetes environment](https://play.linkall.com/) to simply deploy and try Vanus in your browser.

**Example code**

```golang
package main

import (
	"context"
	"fmt"
	"os"
	"strings"
	"time"

	ce "github.com/cloudevents/sdk-go/v2"
	"github.com/google/uuid"
)

const (
	httpPrefix           = "http://"
	eventbus             = "quick-start"
	xceVanusDeliveryTime = "xvanusdeliverytime"
)

var (
	endpoint = os.Getenv("VANUS_GATEWAY")
)

func main() {
	// step 1: build a CloudEvents client
	client, err := ce.NewClientHTTP()
	if err != nil {
		panic(fmt.Sprintf("new cloudevents client failed, err: %s\n", err.Error()))
	}

	var target string
	if strings.HasPrefix(endpoint, httpPrefix) {
		target = fmt.Sprintf("%s/gateway/%s", endpoint, eventbus)
	} else {
		target = fmt.Sprintf("%s%s/gateway/%s", httpPrefix, endpoint, eventbus)
	}

	// step 2: specify the target
	ctx := ce.ContextWithTarget(context.Background(), target)
	// step 3: build a CloudEvent
	event := ce.NewEvent()
	event.SetID(uuid.NewString())
	event.SetSource("event-source")
	event.SetType("event-type")
	event.SetData(ce.ApplicationJSON, map[string]string{"hello": "world"})
	// set delivery time of delay event
	timeOfRFC3339Nano := time.Now().Add(10 * time.Second).Format(time.RFC3339Nano)
	event.SetExtension(xceVanusDeliveryTime, timeOfRFC3339Nano)

	// step 4: send the CloudEvent
	if result := client.Send(ctx, event); ce.IsUndelivered(result) {
		panic(fmt.Sprintf("failed to send event, err: %s\n", result.Error()))
	}
}
```

**Expected results**
By running the following command after 10s, we can guarantee that The producer successfully sent the event to the Eventbus.
```
$ vsctl event get quick-start
```
Here is the expected result:
```
+-----+--------------------------------------------+
| NO. |                    EVENT                   |
+-----+--------------------------------------------+
|     | Context Attributes,                        |
|     |   specversion: 1.0                         |
|     |   type: event-type                         |
|     |   source: event-source                     |
|     |   id: 299c6cbd-b3c6-4de8-8486-ef0c13d60d9e |
|     |   time: 2022-09-20T12:42:36.992825162Z     |
|  0  |   datacontenttype: text/plain              |
|     | Extensions,                                |
|     |   xvanusdeliverytime: 2022-09-20T13:00:00Z |
|     |   xvanuseventbus: quick-start              |
|     |   xvanuslogoffset: AAAAAAAAAAA=            |
|     |   xvanusstime: 2022-09-20T12:42:36.994Z    |
|     | Data,                                      |
|     |   {                                        |
|     |     "hello": "world"                       |
|     |   }                                        |
|     |                                            |
+-----+--------------------------------------------+
```
