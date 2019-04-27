---
title: Skicka och ta emot händelser med hjälp av Go - Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en genomgång för att skapa ett Go-program som skickar händelser från Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821692"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Skicka händelser till eller ta emot händelser från Event Hubs med hjälp av Go
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

Den här självstudien beskrivs hur du skriver Go-program att skicka händelser till eller ta emot händelser från en event hub. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Gå installerad lokalt. Följ [instruktionerna](https://golang.org/doc/install) om det behövs.
- Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.
- **Skapa ett Event Hubs-namnområde och en event hub**. Använd den [Azure-portalen](https://portal.azure.com) att skapa ett namnområde av typen Event Hubs och hämta autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md).

## <a name="send-events"></a>Skicka händelser
Det här avsnittet visar hur du skapar ett Go-program för att skicka händelser till en händelsehubb. 

### <a name="install-go-package"></a>Installera Go-paketet

Hämta Go-paketet för Event Hubs med `go get` eller `dep`. Exempel:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importera paket i kodfilen

Använd följande kodexempel för att importera Go-paket:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

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

### <a name="create-event-hubs-client"></a>Skapa en händelsehubbklient

Följande kod skapar en händelsehubbklient:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Skriv kod för att skicka meddelanden

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Tillägg

Hämta ID: N för partitionerna i din event hub:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Kör programmet för att skicka händelser till event hub. 

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="receive-events"></a>Ta emot händelser

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare

Tillstånd, till exempel lån på partitioner och kontrollpunkter i stream delas mellan mottagare med hjälp av en Azure Storage-behållare. Du kan skapa ett lagringskonto och en behållare med Go SDK, men du kan också skapa en genom att följa instruktionerna i [om Azure storage-konton](../storage/common/storage-create-storage-account.md).

Exempel för att skapa Storage artefakter med Go SDK finns i den [Go-exempel lagringsplatsen](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) och i exemplet som motsvarar den här självstudien.

### <a name="go-packages"></a>Gå paket

För att ta emot meddelanden, hämta Go-paket för Event Hubs med `go get` eller `dep`:

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

Använd följande kodexempel för att importera Go-paket:

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

Skapa ett nytt huvudnamn för tjänsten genom att följa instruktionerna i [skapa Azure-tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Spara de angivna autentiseringsuppgifterna i din miljö med följande namn: Både Azure SDK för Go och Event Hubs-paketet är förkonfigurerade för att leta efter dessa variabelnamn.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Skapa sedan en auktoriseringsprovider för ditt Event Hubs-klient som använder dessa autentiseringsuppgifter:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Hämta metadata struct

Få en struct med metadata om Azure-miljön med Go-SDK för Azure. Senare åtgärder använder den här struct för att hitta rätt slutpunkterna.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Skapa autentiseringsuppgifter hjälp 

Skapa en autentiseringsuppgift hjälp där tidigare Azure Active Directory (AAD)-autentiseringsuppgifter används för att skapa en signatur för delad åtkomst (SAS)-autentiseringsuppgift för lagring. Den sista parametern talar om den här konstruktorn ska använda samma miljövariabler som använts tidigare:

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Skapa en pekare för kontroll och en leaser 

Skapa en **leaser**, de ansvarar för att leasa en partition till en viss mottagare och en **Kontrollera pekaren**, de ansvarar för att skriva kontrollpunkter för meddelandeströmmen så att andra mottagare kan börja läser från rätt förskjutningen.

För närvarande en enda **StorageLeaserCheckpointer** är tillgängliga som använder samma behållaren för att hantera både lån och kontrollpunkter. Förutom att storage-konto och en behållare namn, den **StorageLeaserCheckpointer** måste de autentiseringsuppgifter som skapades i föregående steg och Azure-miljön struct korrekt åtkomst till behållaren.

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

### <a name="construct-event-processor-host"></a>Skapa värden för händelsebearbetning

Nu har du de delar som behövs för att konstruera ett EventProcessorHost på följande sätt. Samma **StorageLeaserCheckpointer** används som både en leaser och kontrollera pekare, enligt beskrivningen tidigare:

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

Skapa en hanterare nu och registrera den med värden för händelsebearbetning. När värden startas, gäller detta och andra angivna hanterare för inkommande meddelanden:

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

Med allt som ställer in, kan du starta värden för händelsebearbetning med `Start(context)` att hålla den permanent direkt eller med `StartNonBlocking(context)` att endast köra så länge meddelanden är tillgängliga.

Den här självstudien startar och kör följande; Se GitHub-exempel för ett exempel med hjälp av `StartNonBlocking`:

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
- [Funktionerna och terminologin i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
