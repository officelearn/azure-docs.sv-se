---
title: Azure Batch tjänst jobb
titleSuffix: ML Studio (classic) - Azure
description: Översikt över Azure Batch-tjänster för Machine Learning Studio (klassiska) jobb. Med batchbearbetning kan du skapa pooler där du kan skicka batch-jobb.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: ad3ae99b04d5386172c9598f35cbe7d64fddcd36
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837698"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Azure Batch tjänst för Azure Machine Learning Studio (klassiska) jobb

Machine Learning bearbetning av batch-pool ger kundhanterad skalning för Azure Machine Learning batch-körning. Den klassiska batchbearbetningen för maskin inlärning sker i en miljö för flera klienter, vilket begränsar antalet samtidiga jobb som du kan skicka och jobb placeras i kö enligt en första-in-First-out. Denna osäkerhet innebär att du inte kan förutsäga när jobbet ska köras.

Med batchbearbetning kan du skapa pooler där du kan skicka batch-jobb. Du styr storleken på poolen och till vilken pool jobbet skickas. Ditt BES-jobb körs i sitt eget bearbetnings utrymme och ger förutsägbar bearbetnings prestanda och möjlighet att skapa resurspooler som motsvarar den bearbetnings belastning som du skickar in.

> [!NOTE]
> Du måste ha en ny Resource Manager-baserad Machine Learning-webbtjänst för att skapa en pool. När du har skapat kan du köra alla BES-webbtjänster, både nya Resource Manager-baserade och klassiska, på poolen.

## <a name="how-to-use-batch-pool-processing"></a>Använda bearbetning av batch-pool

Konfiguration av batchbearbetning av pooler är för närvarande inte tillgängligt via Azure Portal. Om du vill använda bearbetning av batch-pool måste du:

-   Anropa CSS för att skapa ett batch-gruppkonto och hämta en URL för en adresspool och en nyckel för autentisering
-   Skapa en ny Resource Manager-baserad webb tjänst och fakturerings plan

Om du vill skapa ditt konto ska du kontakta Microsofts kund service och support (CSS) och ange ditt prenumerations-ID. CSS fungerar med dig för att fastställa lämplig kapacitet för ditt scenario. CSS konfigurerar sedan ditt konto med det maximala antalet pooler som du kan skapa och det maximala antalet virtuella datorer (VM) som du kan placera i varje pool. När ditt konto har kon figurer ATS får du en URL för din adresspool och en nyckel för autentisering.

När ditt konto har skapats använder du URL: en för pool tjänsten och auktoriseringsregeln för att utföra hanterings åtgärder för pooler i batch-poolen.

![Arkitektur för batch-adresspool.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Du skapar pooler genom att anropa åtgärden för att skapa pooler på den URL för pool tjänst som du har fått från den CSS. När du skapar en pool anger du antalet virtuella datorer och URL: en för Swagger. JSON för en ny Resource Manager-baserad Machine Learning-webbtjänst. Den här webb tjänsten tillhandahålls för att upprätta fakturerings associeringen. Batch-adresspoolen använder Swagger. JSON för att koppla poolen till en fakturerings plan. Du kan köra alla BES-webbtjänster, både nya Resource Manager-baserade och klassiska, på poolen.

Du kan använda alla nya Resource Manager-baserade webb tjänster, men tänk på att faktureringen för jobben debiteras mot den fakturerings plan som är associerad med tjänsten. Du kanske vill skapa en webb tjänst och en ny fakturerings plan som är specifik för att köra batch-poolens jobb.

Mer information om hur du skapar webb tjänster finns i [distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md).

När du har skapat en pool skickar du BES-jobbet med batch-begärandena URL: en för webb tjänsten. Du kan välja att skicka den till en pool eller till klassisk batchbearbetning. Om du vill skicka ett jobb till batchbearbetningen lägger du till följande parameter i begäran om jobb sändning:

"AzureBatchPoolId": "&lt;pool-ID&gt;"

Om du inte lägger till parametern körs jobbet i den klassiska batchens process miljö. Om poolen har tillgängliga resurser börjar jobbet att köras omedelbart. Om poolen inte har några lediga resurser placeras jobbet i kö tills en resurs är tillgänglig.

Om du upptäcker att du regelbundet når kapaciteten för dina pooler och du behöver ökad kapacitet kan du anropa CSS och arbeta med en representant för att öka dina kvoter.

Exempel förfrågan:

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

## <a name="considerations-when-using-batch-pool-processing"></a>Att tänka på när du använder batchbearbetning av pooler

Bearbetning av batch-pool är en fakturerbar tjänst som kräver att du kopplar den till en Resource Manager-baserad fakturerings plan. Du debiteras bara för antalet beräknings timmar som poolen körs. oavsett hur många jobb som körs under den tids poolen. Om du skapar en pool debiteras du för beräknings timmarna för varje virtuell dator i poolen tills poolen tas bort, även om inga batch-jobb körs i poolen. Faktureringen för de virtuella datorerna startar när den har slutfört etableringen och stoppas när de har tagits bort. Du kan använda någon av de planer som finns på [sidan Machine Learning prissättning](https://azure.microsoft.com/pricing/details/machine-learning/).

Fakturerings exempel:

Om du skapar en batch-pool med två virtuella datorer och tar bort den efter 24 timmar debiteras din fakturerings plan 48 beräknings timmar. oavsett hur många jobb som kördes under den perioden.

Om du skapar en batch-pool med 4 virtuella datorer och tar bort den efter 12 timmar debiteras även din fakturerings plan 48 beräknings timmar.

Vi rekommenderar att du avsöker jobbets status för att fastställa när jobben har slutförts. När alla jobb har slutförts, anropar du åtgärden ändra storlek för poolen för att ange antalet virtuella datorer i poolen till noll. Om du är kort med poolen resurser och du måste skapa en ny pool, till exempel för att fakturera mot en annan fakturerings plan, kan du ta bort poolen i stället för alla jobb som har körts.


| **Använda bearbetning av batch-pool när**    | **Använd klassisk batchbearbetning när**  |
|---|---|
|Du måste köra ett stort antal jobb<br>Eller<br/>Du måste veta att dina jobb kommer att köras omedelbart<br/>Eller<br/>Du behöver garanterat data flöde. Du måste till exempel köra ett antal jobb inom en tids period och vill skala upp dina beräknings resurser för att uppfylla dina behov.    | Du kör bara några få jobb<br/>Och<br/> Du behöver inte köra jobben direkt |
