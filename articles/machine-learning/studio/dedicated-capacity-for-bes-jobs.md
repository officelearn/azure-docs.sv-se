---
title: "Dedikerade kapacitet för Machine Learning Batch Execution Service jobb | Microsoft Docs"
description: "Översikt över Azure Batch-tjänster för Machine Learning-jobb."
services: machine-learning
documentationcenter: 
author: serinakaye
ms.author: serinak
manager: mwinkle
editor: 
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 51ae9d72906799e0e4f2f7d8a8a228cd0df0d91a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>Azure Batch-tjänsten för Machine Learning-jobb

Machine Learning Batch-Pool bearbetning innehåller kundhanterad skala för Azure Machine Learning Batch Execution Service. Klassiska gruppbearbetning för maskininlärning äger rum i en miljö med flera innehavare, vilket begränsar antalet samtidiga jobb som du kan skicka och jobb ställs i kö på grundval av first i first out. Den här osäkerhet innebär att du inte kan förutsäga när jobbet ska köras.

Poolen batchbearbetning kan du skapa pooler som du kan skicka batchjobb. Du styr storleken på poolen och vilka poolen jobbet har skickats. BES-jobb körs i sin egen bearbetning utrymme som ger förutsägbar bearbetning och möjligheten att skapa resurspooler som motsvarar belastningen som du skickar.

## <a name="how-to-use-batch-pool-processing"></a>Hur du använder behandling av Batch-Pool

Konfigurationen av gruppbearbetning poolen är inte tillgänglig via Azure-portalen. Om du vill använda Batch-Pool bearbetning, måste du:

-   Anropa CSS om du vill skapa en Pool med Batch-kontot och få en Pool tjänst-URL och auktoriseringsnyckel
-   Skapa en ny Resource Manager-baserat webbtjänsten och faktureringsavtal

Ring Microsofts kundservice och Support (CSS) för att skapa ditt konto, och ange ditt prenumerations-ID. CSS fungerar med dig för att avgöra lämpliga kapacitet för ditt scenario. CSS konfigurerar ditt konto med det maximala antalet pooler som du kan skapa och högsta antalet virtuella datorer (VM) som du kan placera i varje pool. När du har konfigurerat ditt konto finns din Pool URL: en och auktoriseringsnyckel.

När ditt konto har skapats kan använda du nyckeln poolen URL: en och auktorisering för att utföra poolen hanteringsåtgärder på Batch-Pool.

![Arkitektur för batch-pool.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Du kan skapa pooler genom att anropa åtgärden Skapa poolen på poolen tjänstens Webbadress CSS som du har fått. När du skapar en pool, kan du ange hur många virtuella datorer och URL-Adressen för swagger.json av en ny Resource Manager baserat Machine Learning-webbtjänst. Den här webbtjänsten har angetts för att upprätta fakturering association. Swagger.json använder tjänsten Batch-Pool för att associera poolen med ett faktureringsavtal. Du kan köra alla BES webbtjänst, både nya Resource Manager-baserat och classic du väljer på poolen.

Du kan använda alla nya Resource Manager-baserat webbtjänst, men tänk på att faktureringen för jobben debiteras mot den faktureringsplan som är associerade med den tjänsten. Du kanske vill skapa en webbtjänst och nya faktureringsavtal specifikt för Batch-Pool jobb som körs.

Mer information om hur du skapar webbtjänster finns [distribuera en Azure Machine Learning-webbtjänst](publish-a-machine-learning-web-service.md).

När du har skapat en pool kan skicka du BES jobbet med Batch begäranden URL för webbtjänsten. Du kan välja att skicka den till en pool eller klassiska batch-bearbetning. Om du vill skicka ett jobb för att bearbeta Batch-Pool du lägga till följande parameter till jobbet skicka frågans brödtext:

"AzureBatchPoolId":"&lt;pool ID&gt;"

Om du inte lägga till parametern körs jobbet i klassiskt batch processmiljö. Om poolen har tillgängliga resurser, startar jobbet körs omedelbart. Om poolen saknar lediga resurser i ditt jobb kö tills en resurs som är tillgänglig.

Om du upptäcker att du regelbundet når kapaciteten för din pooler, och du behöver bättre kapacitet kan du anropa CSS och arbeta med en representant för att öka din kvoter.

Exempelbegäran:

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

## <a name="considerations-when-using-batch-pool-processing"></a>Information om bearbetningen av Batch-Pool

Gruppbearbetning poolen är en alltid i fakturerbar tjänst och som kräver att du associerar den med en Resource Manager-baserat faktureringsavtal. Endast debiteras du för antal beräkningstimmar poolen körs på. oavsett vilket antal jobb som körs under tiden poolen. Om du skapar en pool, debiteras du för beräkningstimmar för varje virtuell dator i poolen tills poolen tas bort även om inga batchjobb körs i poolen. Fakturering för virtuella datorer startar när etableringen är klar och stoppas när de har tagits bort. Du kan använda någon av planer hittades på den [Machine Learning-priser sidan](https://azure.microsoft.com/pricing/details/machine-learning/).

Fakturering exempel:

Om du skapar en Batch-Pool med 2 virtuella datorer och ta bort den efter 24 timmar debiteras faktureringsavtalet 48 beräkningstimmar; oavsett hur många jobb kördes under denna period.

Om du skapar en Batch-Pool med 4 virtuella datorer och ta bort den efter 12 timmar, är också faktureringsavtalet Debiterat 48 beräkningstimmar.

Vi rekommenderar att du avsöka jobbstatus för att avgöra när jobben har slutförts. När alla jobb har körts anropa ändra storlek på poolen igen för att ange antalet virtuella datorer i poolen med noll. Om du har ont om resurser och du behöver skapa en ny pool, till exempel för att debiterar mot en annan faktureringsplan, du kan ta bort poolen i stället när dina jobb har körts.


| **Använd bearbetning när Batch-Pool**    | **Klassiska batchbearbetning när**  |
|---|---|
|Du måste köra ett stort antal jobb<br>Eller<br/>Du behöver veta att dina jobb ska köras omedelbart<br/>Eller<br/>Du måste garanterad genomflöde. Du behöver exempelvis kör ett antal jobb i en angiven tidsperiod och vill skala upp dina beräkningsresurser som uppfyller dina behov.    | Du kör några jobb<br/>Och<br/> Du behöver inte jobb ska köras omedelbart |
