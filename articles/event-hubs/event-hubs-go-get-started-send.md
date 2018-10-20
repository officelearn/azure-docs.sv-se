---
title: Skicka händelser till Azure Event Hubs med hjälp av Go | Microsoft Docs
description: Kom igång med att skicka händelser till Event Hubs med hjälp av Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: f5e30a103b09613caee8e9912a89a5bc2d390f65
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458096"
---
# <a name="send-events-to-event-hubs-using-go"></a>Skicka händelser till Event Hubs med hjälp av Go

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Detaljerad översikt över Event Hubs finns i [översikt av Händelsehubbar](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb från ett program som skrivits i Go. 

> [!NOTE]
> Du kan hämta den här snabbstarten som ett exempel från den [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), Ersätt `EventHubConnectionString` och `EventHubName` strängarna med värdena för din händelsehubb, och kör den. Du kan också följa stegen i den här kursen och skapa dina egna.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* Gå installerad lokalt. Följ [instruktionerna](https://golang.org/doc/install) om det behövs.
* En befintlig Event Hubs-namnområde och event hub. Du kan skapa dessa enheter genom att följa instruktionerna i [i den här artikeln](event-hubs-create.md).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Skapa ett namnområde för Event Hubs och en händelsehubb
Det första steget är att använda [Azure Portal](https://portal.azure.com) till att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du vill skapa ett namnområde och en händelsehubb följer du anvisningarna i [i den här artikeln](event-hubs-create.md), fortsätter sedan enligt följande steg i den här självstudien.

## <a name="install-go-package"></a>Installera Go-paketet

Hämta Go-paketet för Event Hubs med `go get` eller `dep`. Exempel:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>Importera paket i kodfilen

Använd följande kodexempel för att importera Go-paket:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett nytt huvudnamn för tjänsten genom att följa instruktionerna i [skapa Azure-tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Spara de angivna autentiseringsuppgifterna i din miljö med följande namn. Både Azure SDK för Go och Event Hubs-paket är förkonfigurerade för att leta efter dessa variabelnamn:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Nu skapa en auktoriseringsprovider för ditt Event Hubs-klient som använder dessa autentiseringsuppgifter:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Skapa en händelsehubbklient

Följande kod skapar en händelsehubbklient:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Skicka meddelanden

Använd (1) för att skicka meddelanden interaktivt från en terminal eller (2) för att skicka meddelanden från ditt program i följande kodavsnitt:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Tillägg

Hämta ID: N för partitionerna i din event hub:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

Kör programmet för att skicka händelser till event hub. 

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skickat meddelanden till en händelsehubb med hjälp av Go. Läs hur du tar emot händelser från en händelsehubb med hjälp av Go i [ta emot händelser från event hub - Go](event-hubs-go-get-started-receive-eph.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
