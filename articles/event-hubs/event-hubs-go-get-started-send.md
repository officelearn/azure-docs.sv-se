---
title: Skicka händelser till Azure Event Hubs med hjälp av Go | Microsoft Docs
description: Kom igång med att skicka händelser till Event Hubs med hjälp av Go
services: event-hubs
author: joshgav
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: joshgav
ms.openlocfilehash: de454e7c94cb89a44f681fa1301bb5109ced03fd
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39717029"
---
# <a name="send-events-to-event-hubs-using-go"></a>Skicka händelser till Event Hubs med hjälp av Go

Azure Event Hubs är en mycket skalbar Händelsehanteringssystem som kan hantera flera miljoner händelser per sekund programmen kan bearbeta och analysera stora datamängder som produceras av anslutna enheter och andra system. När samlats in i en händelsehubb, du kan ta emot och hantera händelser med hjälp av pågående hanterare eller vidarebefordran till andra system för analys av.

Mer information om Händelsehubbar finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb från ett program som skrivits i Go. Om du vill ta emot händelser, använda den **gå eph** (Event Processor Host)-paketet enligt beskrivningen i [motsvarande Receive-artikel](event-hubs-go-get-started-receive-eph.md).

Koden i den här självstudiekursen kommer från [dessa GitHub-exempel](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), som du kan kontrollera för att se fullständiga fungerande program, inklusive importuttryck och variabeldeklarationer.

Andra exempel är tillgängliga [i Hubs paketera lagringsplatsen](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* Gå installerad lokalt. Följ [instruktionerna](https://golang.org/doc/install) om det behövs.
* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.
* En befintlig Event Hubs-namnområde och event hub. Du kan skapa dessa enheter genom att följa instruktionerna i [i den här artikeln](event-hubs-create.md).

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

## <a name="next-steps"></a>Nästa steg

Finns på följande sidor om du vill veta mer om Event Hubs:

* [Ta emot händelser med hjälp av EventProcessorHost](event-hubs-go-get-started-receive-eph.md)
* [Översikt av händelsehubbar][Event Hubs overview]
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
