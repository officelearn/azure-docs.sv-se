---
title: "Ge programdata hög tillgänglighet i Azure | Microsoft Docs"
description: "Använd geo-redundant lagring med läsbehörighet så att dina programdata hög tillgänglighet"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 286013aaa5335689206514027bef80b250643be1
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Ge dina programdata hög tillgänglighet med Azure storage

Den här kursen ingår i en serie. Den här kursen visar hur du gör dina programdata hög tillgänglighet i Azure. När du är klar har du en .NET core-konsolprogram som överför och hämtar en blobb till en [geo-redundant läsbehörighet](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) storage-konto. RA-GRS fungerar genom att replikera transaktioner från den primära servern till den sekundära regionen. Den här replikeringen garanterar att data i den sekundära regionen är överensstämmelse. Programmet använder den [strömbrytare](/azure/architecture/patterns/circuit-breaker.md) mönstret för att avgöra vilken slutpunkt för att ansluta till. Programmet växlar till sekundär slutpunkt när ett fel simuleras.

I delen en av serierna kan du lära dig hur du:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Hämta exemplet
> * Ange anslutningssträngen
> * Köra konsolprogrammet

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
  - **Azure Development**

  ![Azure-utveckling (under webb & moln)](media/storage-create-geo-redundant-storage/workloads.png)

* Hämta och installera [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett lagringskonto ger ett unikt namnområde för att lagra och komma åt dina Azure storage-dataobjekt.

Följ dessa steg om du vill skapa ett konto med geo-redundant lagring med läsbehörighet:

1. Välj den **ny** knapp hittades i det övre vänstra hörnet i Azure-portalen.

2. Välj **lagring** från den **ny** och väljer **lagringskonto - blob, fil, tabell, kö** under **aktuell**.
3. Fyll i formuläret storage-konto med följande information som visas i följande bild och välj **skapa**:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Namn** | mittlagringskonto | Ett unikt värde för ditt lagringskonto |
   | **Distributionsmodell** | Resource Manager  | Resource Manager innehåller de senaste funktionerna.|
   | **Typ av konto** | Generellt syfte | Mer information om vilka typer av konton finns [typer av lagringskonton](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Prestanda** | Standard | Standard är tillräcklig för exempelscenariot. |
   | **Replikering**| Geo-redundant lagring med läsbehörighet (RA-GRS) | Detta är nödvändigt att fungera. |
   |**Säker överföring krävs** | Disabled| Säker överföring krävs inte för det här scenariot. |
   |**Prenumeration** | Din prenumeration |Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Plats** | Östra USA | Välj en plats. |

![Skapa lagringskonto](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>Hämta exemplet

[Hämta exempelprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip).

Extrahera (packa) storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip filen.
Exempelprojektet innehåller ett konsolprogram.

## <a name="set-the-connection-string"></a>Ange anslutningssträngen

Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Det rekommenderas att lagra den här anslutningssträngen inom en miljövariabel på den lokala datorn som kör programmet. Gör något av exemplen nedan beroende på operativsystemet för att skapa miljövariabeln.

Navigera till ditt lagringskonto i Azure-portalen. Välj **åtkomstnycklar** under **inställningar** i ditt lagringskonto. Kopiera den **anslutningssträngen** från de primära och sekundära nycklarna. Ersätt \<yourconnectionstring\> med faktiska anslutningen sträng genom att köra något av följande kommandon baserat på ditt operativsystem. Detta kommando sparar en miljövariabel till den lokala datorn. I Windows, miljövariabeln är inte tillgänglig förrän du uppdatera den **kommandotolk** eller gränssnitt som du använder. Ersätt  **\<storageConnectionString\>**  i följande exempel:

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

![App-konfigurationsfil](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>Köra konsolprogrammet

I Visual Studio trycker du på **F5** eller välj **starta** att starta felsökningen av programmet. Visual studio automatiskt återställer saknas NuGet-paket om konfigurerad, gå till [installera och installera om paket med paket återställning](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) vill veta mer.

Ett konsolfönster öppnas och börjar programmet körs. Programmet överför den **HelloWorld.png** bilden från lösningen till lagringskontot. Programmet kontrollerar att avbildningen har replikerats till den sekundära RA-GRS-slutpunkten. Sedan börjar nedladdningen av avbildningen upp till 999 gånger. Varje Läs representeras av en **P** eller en **S**. Där **P** representerar primära slutpunkten och **S** representerar sekundära slutpunkten.

![Kör-konsolapp](media/storage-create-geo-redundant-storage/figure3.png)

I koden, den `RunCircuitBreakerAsync` uppgift i den `Program.cs` används för att hämta en avbildning från storage-konto med den [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet) metod. Före hämtningen en [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) har definierats. Åtgärden kontexten definierar händelsehanterare som utlöses när hämtningen är klar eller om en hämtning misslyckas och försöker igen.

### <a name="retry-event-handler"></a>Försök händelsehanterare

Den `OperationContextRetrying` händelsehanteraren anropas när hämtningen av avbildningen misslyckas och ställts in till rety. Om det maximala antalet försök som har definierats i programmet har uppnått den [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) för begäran har ändrats till `SecondaryOnly`. Den här inställningen tvingar programmet att försöka ladda ned avbildningen från den sekundära slutpunkten. Den här konfigurationen minskar den tid det tar att begära bilden som primär slutpunkten inte försöks under obestämd tid.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Begäran slutförd händelsehanterare

Den `OperationContextRequestCompleted` händelsehanteraren anropas när hämtningen av avbildningen lyckas. Om programmet använder sekundära slutpunkten, fortsätter programmet att använda den här slutpunkten upp till 20 gånger. Efter 20 gånger programmet anger den [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) tillbaka till `PrimaryThenSecondary` och försöker den primära slutpunkten. Om en begäran lyckas fortsätter programmet att läsa från den primära slutpunkten.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

I delen en av serierna du lärt dig om hur du skapar hög tillgänglighet med RA-GRS storage-konton, till exempel att:

> [!div class="checklist"]
> * skapar ett lagringskonto
> * Hämta exemplet
> * Ange anslutningssträngen
> * Köra konsolprogrammet

Gå vidare till avsnitt två serier till Lär dig att simulera ett fel och tvinga programmet så att den sekundära RA-GRS-slutpunkten.

> [!div class="nextstepaction"]
> [Simulera ett fel i anslutningen till din primära lagringsplatsen slutpunkt](storage-simulate-failure-ragrs-account-app.md)
