---
title: 'Snabbstart: Skicka och ta emot händelser med Go - Azure Event Hubs'
description: 'Snabbstart: Den här artikeln innehåller en genomgång för att skapa ett Go-program som skickar händelser från Azure Event Hubs.'
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720582"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Snabbstart: Skicka händelser till eller ta emot händelser från eventhubbar med Go
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här självstudien beskrivs hur du skriver Go-program för att skicka händelser till eller ta emot händelser från en händelsehubb. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

- Gå installerat lokalt. Följ [dessa instruktioner](https://golang.org/doc/install) om det behövs.
- Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][] konto innan du börjar.
- **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Använd [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md).

## <a name="send-events"></a>Skicka händelser
I det här avsnittet visas hur du skapar ett Go-program för att skicka händelser till en händelsehubb. 

### <a name="install-go-package"></a>Install Go-paketet

Hämta Go-paketet för eventhubbar med `go get` eller `dep`. Ett exempel:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importera paket i kodfilen

Om du vill importera Go-paketen använder du följande kodexempel:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett nytt tjänsthuvudnamn genom att följa instruktionerna i [Skapa ett Azure-tjänsthuvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Spara de angivna autentiseringsuppgifterna i din miljö med följande namn. Både Azure SDK för go och eventhubbar-paketen är förkonfigurerade för att leta efter dessa variabelnamn:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Nu skapar du en auktoriseringsprovider för din Event Hubs-klient som använder dessa autentiseringsuppgifter:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Skapa klient för eventhubbar

Följande kod skapar en Event Hubs-klient:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Skriv kod för att skicka meddelanden

I följande kodavsnitt använder du (1) för att skicka meddelanden interaktivt från en terminal, eller (2) för att skicka meddelanden inom programmet:

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Tillägg

Hämta ID:erna för partitionerna i händelsehubben:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Kör programmet för att skicka händelser till händelsehubben. 

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="receive-events"></a>Ta emot händelser

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare

Tillstånd som lån på partitioner och kontrollpunkter i händelseströmmen delas mellan mottagare med hjälp av en Azure Storage-behållare. Du kan skapa ett lagringskonto och en behållare med Go SDK, men du kan också skapa ett genom att följa instruktionerna i [Om Azure-lagringskonton](../storage/common/storage-create-storage-account.md).

Exempel för att skapa lagringsartefakter med Go SDK finns i [repo-erpoerna Gå-exempel](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) och i exemplet som motsvarar den här självstudien.

### <a name="go-packages"></a>Gå paket

Om du vill ta emot meddelandena hämtar du `go get` `dep`Go-paketen för eventhubbar med eller:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importera paket i kodfilen

Om du vill importera Go-paketen använder du följande kodexempel:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett nytt tjänsthuvudnamn genom att följa instruktionerna i [Skapa ett Azure-tjänsthuvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Spara de angivna autentiseringsuppgifterna i din miljö med följande namn: Paketet Både Azure SDK för go och eventhubbar är förkonfigurerade för att leta efter dessa variabelnamn.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Skapa sedan en auktoriseringsprovider för din Event Hubs-klient som använder dessa autentiseringsuppgifter:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Hämta metadatastruktur

Skaffa en struktur med metadata om din Azure-miljö med Azure Go SDK. Senare åtgärder använder den här strukturen för att hitta korrekta slutpunkter.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Skapa autentiseringsuppgifter 

Skapa en autentiseringsuppgifter som använder de tidigare Azure Active Directory-autentiseringsuppgifterna (AAD) för att skapa en SAS-autentiseringsuppgifter (Shared Access Signature) för lagring. Den sista parametern talar om för den här konstruktorn att använda samma miljövariabler som tidigare:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Skapa en kontrollpekare och en leaser 

Skapa en **leaser**, ansvarig för att leasa en partition till en viss mottagare, och en **kontrollpekare**, som ansvarar för att skriva kontrollpunkter för meddelandeströmmen så att andra mottagare kan börja läsa från rätt förskjutning.

För närvarande är en enda **StorageLeaserCheckpointer** tillgänglig som använder samma lagringsbehållare för att hantera både lån och kontrollpunkter. Förutom lagringskontot och behållarnamnen behöver **StorageLeaserCheckpointer** den autentiseringsuppgifter som skapats i föregående steg och Azure-miljöstrukturen för att komma åt behållaren på rätt sätt.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Konstruera händelseprocessorvärd

Du har nu de bitar som behövs för att konstruera en EventProcessorHost, enligt följande. Samma **StorageLeaserCheckpointer** används både som leaseer och checkpekare, som beskrivits tidigare:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Skapa hanterare 

Nu skapa en hanterare och registrera den med eventprocessor värd. När värden startas tillämpas detta och andra angivna hanterare på inkommande meddelanden:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Skriv kod för att ta emot meddelanden

Med allt konfigurerat kan du starta `Start(context)` händelsebehandlarens värd för `StartNonBlocking(context)` att hålla den igång permanent eller med att köras endast så länge meddelanden är tillgängliga.

Den här självstudien startar och körs på följande sätt; se GitHub-exemplet för `StartNonBlocking`ett exempel med :

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[gratis konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
