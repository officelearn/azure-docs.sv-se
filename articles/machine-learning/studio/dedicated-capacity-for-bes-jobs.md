---
title: Azure Batch-webbtjänstjobb
titleSuffix: Azure Machine Learning Studio
description: Översikt över Azure Batch-tjänster för Machine Learning Studio-jobb. Batchbearbetning för IP-Pool kan du skapa pooler där du kan skicka batch-jobb.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 24efa3caba3918a38c09b1c921c600b117dedbc1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871383"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-jobs"></a>Azure Batch-tjänsten för Azure Machine Learning Studio-jobb

Machine Learning Batch-Pool bearbetning ger kundhanterad scale för Azure Machine Learning Batch Execution Service. Klassiska batch behandling i samband med maskininlärning äger rum i en miljö med flera innehavare, vilket begränsar antalet samtidiga jobb som du kan skicka och jobb ställs i kö för först in först ut. Den här osäkerheten innebär att det går inte att korrekt förutse när jobbet ska köras.

Batchbearbetning för IP-Pool kan du skapa pooler där du kan skicka batch-jobb. Du kontrollerar storleken på poolen och jobbet skickas till vilken pool. BES jobbet körs i sin egen bearbetning utrymme att tillhandahålla förutsägbar bearbetning och möjligheten att skapa resurspooler som motsvarar den belastning som du skickar.

> [!NOTE]
> Du måste ha en ny Resource Manager baserat Machine Learning-webbtjänsten för att skapa en pool. När du skapat kan du köra alla BES webbtjänst, både nya Resource Manager baserade och klassiska, på poolen.

## <a name="how-to-use-batch-pool-processing"></a>Hur du använder Batch-Pool bearbetning

Konfigurationen för bearbetning av Batch-Pool är inte tillgänglig via Azure portal. För att använda Batch-Pool bearbetning, måste du:

-   Anropa CSS om du vill skapa ett Batch-Pool-konto och hämta en Pool tjänstens URL och en auktoriseringsnyckel
-   Skapa en ny Resource Manager-baserade webbtjänst och faktureringsplan

Anropa Microsofts kundservice och Support (CSS) för att skapa ditt konto, och ange ditt prenumerations-ID. CSS samarbetar med dig för att fastställa rätt kapacitet för ditt scenario. CSS konfigurerar sedan ditt konto med det maximala antalet pooler som du kan skapa och det maximala antalet virtuella datorer (VM) som du kan placera i varje pool. När ditt konto har konfigurerats kan tillhandahålls du Pool tjänstens URL och en auktoriseringsnyckel.

När ditt konto har skapats kan använda du nyckeln Pool tjänstens URL och auktorisering för att utföra hanteringsåtgärder för poolen i Batch-poolen.

![Batch-pool service-arkitektur.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Du kan skapa pooler för genom att anropa åtgärden Skapa poolen på poolen tjänstens URL CSS fått. När du skapar en pool kan du ange hur många virtuella datorer och URL: en för swagger.json av en ny Resource Manager baserat Machine Learning-webbtjänst. Den här webbtjänsten tillhandahålls för att upprätta fakturering kopplingen. Batch-Pool-tjänsten använder swagger.json för att associera poolen med en lämplig faktureringsplan. Du kan köra alla BES webbtjänst, både nya Resource Manager baserade och klassiska, på poolen.

Du kan använda någon ny Resource Manager-baserade webbtjänst, men tänk på att faktureringen för jobb debiteras mot den faktureringsplan som är associerade med den tjänsten. Du kanske vill skapa en webbtjänst och nya faktureringsplan specifikt för att köra jobb i Batch-Pool.

Mer information om hur du skapar webbtjänster finns i [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

När du har skapat en pool kan skicka du BES-jobb med hjälp av URL: en för Batch-begäranden för webbtjänsten. Du kan välja att skicka den till en pool eller klassiska batch-bearbetning. Om du vill skicka ett jobb till Batch-Pool som bearbetar du lägga till följande parameter till begärandetexten jobbet bidrag:

”AzureBatchPoolId” ”:&lt;lagringspoolen ID&gt;”

Om du inte lägga till parametern körs jobbet i den klassiska batch process-miljön. Om poolen har tillgängliga resurser kan jobbet börjar köras omedelbart. Om poolen inte har kostnadsfria resurser, är ditt jobb i kö tills en resurs är tillgänglig.

Om du upptäcker att du regelbundet når kapaciteten för dina pooler och du behöver ökad kapacitet, kan du anropa CSS och arbeta med en representant för att öka dina kvoter.

Exempelbegärande:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Att tänka på när du använder Batch-Pool bearbetning

Bearbetning av batch-Pool är en ständig fakturerbar tjänst och som kräver att du associerar det med en Resource Manager-baserade faktureringsplan. Du debiteras endast för antalet beräkningstimmar poolen körs på. oavsett hur många jobb som körs under tiden poolen. Om du skapar en pool, debiteras du för beräkningstimmar för varje virtuell dator i poolen tills poolen tas bort även om inga batchjobb körs i poolen. Fakturering för virtuella datorer startar när etableringen är klar och stoppas när de har tagits bort. Du kan använda någon av de planer som finns på den [Machine Learning-priser sidan](https://azure.microsoft.com/pricing/details/machine-learning/).

Fakturering exempel:

Om du skapar en Batch-Pool med 2 virtuella datorer och ta bort den efter 24 timmar debiteras en lämplig faktureringsplan 48 beräkningstimmar; oavsett hur många jobb kördes under den perioden.

Om du skapar en Batch-Pool med 4 virtuella datorer och ta bort den efter 12 timmar, är din faktureringsplan också Debiterat 48 beräkningstimmar.

Vi rekommenderar att du avsöka jobbstatus för att avgöra när du jobb är klar. När alla jobb har körts du anropa åtgärden Ändra storlek på poolen för att ange hur många virtuella datorer i poolen till noll. Om du är kort på poolresurser och du måste skapa en ny pool, till exempel för att debitera mot en annan faktureringsplan, du kan ta bort poolen i stället när alla jobb har körts.


| **Använda Batch-Pool som bearbetar när**    | **Använda klassiska satsvis bearbetning när**  |
|---|---|
|Du måste köra ett stort antal jobb<br>Eller<br/>Du behöver veta att dina jobb ska köras omedelbart<br/>Eller<br/>Behöver du garanterat dataflöde. Exempelvis måste köra ett antal jobb i en angiven tidsperiod och vill skala ut dina beräkningsresurser som uppfyller dina behov.    | Du kör några jobb<br/>Och<br/> Du behöver inte de jobb som ska köras omedelbart |
