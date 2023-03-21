---
title: Use CloudEvents SDK
---

# Receive events from Vanus with [CloudEvents](https://github.com/cloudevents) SDK

The following document will teach you how to build a simple consumer to consume events from Vanus.

## Operations

**Prerequisites**

To consume events from Vanus, you must meet the following prerequisites:
1. Have a running [Vanus](/vanus-open-source/installation) cluster.
2. Have [vsctl](/how-to/vsctl.md).
3. Have created a subscription in Vanus. Please refer to this section of [Manage Subscriptions](/how-to/manage-subscription.md) to create a subscription.

> We also provide an interactive [Kubernetes environment](https://play.linkall.com/) to simply deploy and try Vanus in your browser.

**Example code**
gitgit 
```golang
package main

import (
	"context"
	"fmt"
	"net"

	ce "github.com/cloudevents/sdk-go/v2"
)

func main() {
	ctx := context.Background()
	// step 1: Create a tcp listener
	ls, err := net.Listen("tcp", ":6789")
	if err != nil {
		panic(fmt.Sprintf("failed to listen, err: %s\n", err.Error()))
	}

	// step 2: Create a cloudevents client
	ceClient, err := ce.NewClientHTTP(ce.WithListener(ls))
	if err != nil {
		panic(fmt.Sprintf("failed to init cloudevents client, err: %s", err.Error()))
	}

	fmt.Println("start event receiver")
	// step 3: Start the receiver and do business logic
	err = ceClient.StartReceiver(ctx, func(event ce.Event) {
		fmt.Printf("received an event: %s\n", event.String())
	})
	if err != nil {
		panic(fmt.Sprintf("failed to start cloudevents receiver, err: %s\n", err.Error()))
	}
}

```

> Note: Vanus must communicate with the consumer.

**Send an event to Vanus**

Send an event to the Eventbus

```shell
~ > vsctl event put quick-start \
  --data-format plain \
  --data "Hello Vanus" \
  --id "1" \
  --source "quick-start" \
  --type "examples"
```

**Expected results**

```
listen 0.0.0.0:6789...
received an event: Context Attributes,
  specversion: 1.0
  type: examples
  source: quick-start
  id: 34d3e285-0329-4c6c-9e80-5b3bbcf7cd6f
  time: 2022-09-21T02:08:42.893778582Z
  datacontenttype: text/plain
Extensions,
  xvanuseventbus: quick-start
  xvanusstime: 2022-09-21T02:08:42.894Z
Data,
  Hello Vanus
```
