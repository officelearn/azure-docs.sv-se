---
title: Ta emot händelser från Azure Event Hubs med hjälp av Go | Microsoft Docs
description: Börja ta emot händelser från Event Hubs med hjälp av Go
services: event-hubs
author: joshgav
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: joshgav
ms.openlocfilehash: 14044938692e9a549faa3be127864bf1e399f858
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39717026"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Ta emot händelser från Event Hubs med hjälp av Go

Azure Event Hubs är en mycket skalbar Händelsehanteringssystem som kan hantera flera miljoner händelser per sekund programmen kan bearbeta och analysera stora datamängder som produceras av anslutna enheter och andra system. När samlats in i en händelsehubb, du kan ta emot och hantera händelser med hjälp av pågående hanterare eller vidarebefordran till andra system för analys av.

Mer information om Händelsehubbar finns i [översikt av Händelsehubbar][Event Hubs overview].

Den här självstudien beskrivs hur du tar emot händelser från en händelsehubb i ett Go-program. Lär dig hur du skickar händelser finns i [motsvarande Skicka artikel](event-hubs-go-get-started-send.md).

Koden i den här självstudiekursen kommer från [dessa GitHub-exempel](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), som du kan kontrollera för att se fullständiga fungerar program inklusive importera instruktioner och variabeldeklarationer.

Andra exempel är tillgängliga [i Hubs paketera lagringsplatsen](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Förutsättningar

Den här kursen behöver du följande krav:

* Gå installerad lokalt. Följ [instruktionerna](https://golang.org/doc/install) om det behövs.
* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.
* För att ta emot meddelanden måste det finnas meddelanden i mål event hub. Lär dig hur du skickar meddelanden i den [skicka självstudien](event-hubs-go-get-started-send.md).
* En befintlig händelsehubb (se nedan).
* En befintlig lagringskontot och behållaren (se avsnittet efter nästa avsnitt).

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Den här självstudien börjar med en befintlig Event Hubs-namnområde och event hub. Du kan skapa dessa enheter genom att följa instruktionerna i [i den här artikeln](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare

Tillstånd, till exempel lån på partitioner och kontrollpunkter i stream delas mellan mottagare med hjälp av en Azure Storage-behållare. Du kan skapa ett lagringskonto och en behållare med Go SDK, men du kan också skapa en genom att följa instruktionerna i [om Azure storage-konton](../storage/common/storage-create-storage-account.md).

Exempel för att skapa Storage artefakter med Go SDK finns i den [Go-exempel lagringsplatsen](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) och i exemplet som motsvarar den här självstudien.

## <a name="receive-messages"></a>Ta emot meddelanden

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

## <a name="import-packages-in-your-code-file"></a>Importera paket i kodfilen

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

## <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett nytt huvudnamn för tjänsten genom att följa instruktionerna i [skapa Azure-tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Spara de angivna autentiseringsuppgifterna i din miljö med följande namn. Både Azure SDK för Go och Event Hubs-paketet är förkonfigurerade för att leta efter dessa variabelnamn.

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

## <a name="get-metadata-struct"></a>Hämta metadata struct

Få en struct med metadata om Azure-miljön med Go-SDK för Azure. Senare åtgärder använder den här struct för att hitta rätt slutpunkterna.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Skapa autentiseringsuppgifter hjälp 

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

## <a name="create-checkpointer-and-leaser"></a>Skapa Checkpointer och Leaser 

Skapa en **Leaser**, de ansvarar för att leasa en partition till en viss mottagare och en **Checkpointer**, de ansvarar för att skriva kontrollpunkter för meddelandeströmmen så att andra mottagare kan börja läser från rätt förskjutningen.

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

## <a name="construct-event-processor-host"></a>Skapa värden för händelsebearbetning

Nu har du de delar som behövs för att konstruera ett EventProcessorHost på följande sätt. Samma **StorageLeaserCheckpointer** används som både en Leaser och Checkpointer, enligt beskrivningen tidigare:

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

## <a name="create-handler"></a>Skapa hanterare 

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

## <a name="receive-messages"></a>Ta emot meddelanden

Med allt som ställer in, kan du starta värden för händelsebearbetning med `Start(context)` att hålla den permanent direkt eller med `StartNonBlocking(context)` att endast köra så länge meddelanden är tillgängliga.

Den här självstudien startar och kör följande; Se GitHub-exempel för ett exempel med hjälp av `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Anteckningar

Den här guiden använder en enda instans av **EventProcessorHost**. För att öka dataflödet och tillförlitlighet, bör du köra flera instanser av **EventProcessorHost** på olika system. Leaser säkerställer att endast en mottagare är kopplad till och tar emot meddelanden från en angiven partition vid en viss tidpunkt.

## <a name="next-steps"></a>Nästa steg

Gå till dessa sidor om du vill veta mer om Event Hubs:

* [Skicka händelser med Go](event-hubs-go-get-started-send.md)
* [Event Hubs-översikt](event-hubs-about.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
