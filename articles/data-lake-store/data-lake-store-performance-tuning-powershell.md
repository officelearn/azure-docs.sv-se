---
title: "Prestandajustering riktlinjer för att använda Powershell med Data Lake Store | Microsoft Docs"
description: "Tips om hur du förbättrar prestanda när du använder Azure PowerShell med Data Lake Store"
services: data-lake-store
documentationcenter: 
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 63e1114d49b7bcb8910e8cd8205f10d1e8587f61
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Prestandajustering riktlinjer för att använda PowerShell med Azure Data Lake Store

Den här artikeln innehåller de egenskaper som kan anpassas för att få en bättre prestanda när du använder PowerShell för att arbeta med Data Lake Store:

## <a name="performance-related-properties"></a>Prestanda-relaterade egenskaper

| Egenskap            | Standard | Beskrivning |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Med den här parametern kan du välja antalet parallella trådar för att ladda upp eller ned varje fil. Det här numret representerar de högsta antal trådar som kan tilldelas per fil, men du får färre trådar beroende på scenario (till exempel, om du överför en 1-KB-fil får du en tråd även om du fråga efter 20 trådar).  |
| ConcurrentFileCount | 10      | Den här parametern är specifikt för att ladda upp och ned mappar. Den här parametern anger antalet samtidiga filer som kan laddas upp eller ned. Det här numret representerar maximala antalet samtidiga filer som kan överföras eller hämtas åt gången, men du får mindre samtidighet beroende på scenario (till exempel, om du överför två filer, får du två samtidiga filer överföringar även om du be för 15). |

**Exempel**

Det här kommandot laddar ned filer från Azure Data Lake Store till användarens lokala enhet med 20 trådar per fil och 100 samtidiga filer.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Hur tar jag reda på värdet för egenskaperna?

Nästa fråga som du kanske är hur man avgör vilket värde som ska ange för prestanda-relaterade egenskaper. Här är några riktlinjer som du kan använda.

* **Steg 1: Fastställ det totala antalet trådar** – Du ska börja med att beräkna det totala antalet trådar som ska användas. Som en generell riktlinje bör du använda sex trådar för varje fysiska kärnor.

        Total thread count = total physical cores * 6

    **Exempel**

    Vi antar att du kör PowerShell-kommandon från en virtuell D14-dator med 16 kärnor

        Total thread count = 16 cores * 6 = 96 threads


* **Steg 2: Beräkna PerFileThreadCount** – Vi beräknar vår PerFileThreadCount baserat på filernas storlek. För filer som är mindre än 2,5 GB finns behöver du inte ändra den här parametern eftersom standardvärdet 10 är tillräckliga. För filer som är större än 2,5 GB bör du använda 10 trådar som bas för den första 2,5 GB och Lägg till 1 tråd för varje ytterligare 256 MB ökning av filstorlek. Om du kopierar en mapp med många olika filstorlekar bör du överväga att gruppera dem enligt liknande filstorlekar. Olika stora filstorlekar kan orsaka bristfälliga prestanda. Om du inte kan gruppera liknande filstorlekar ska du ställa in PerFileThreadCount baserat på den största filstorleken.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Exempel**

    Anta att du har 100 filer mellan 1 GB och 10 GB och använda vi 10 GB som den största filstorleken för formeln som skulle läsa som liknar följande.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Steg 3: Beräkna ConcurrentFilecount** – Använd totala antalet trådar och PerFileThreadCount att beräkna ConcurrentFileCount baserat på följande formel:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exempel**

    Utifrån de exempelvärden vi har använt

        96 = 40 * ConcurrentFileCount

    Så, **ConcurrentFileCount** är **2,4**, vilket vi kan runda av till **2**.

## <a name="further-tuning"></a>Ytterligare justering

Du kan behöva justera ytterligare eftersom det finns en mängd filstorlekar att arbeta med. Den föregående beräkningen fungerar bra om alla eller de flesta av filerna som är större och närmare intervallet 10 GB. Om det finns i stället många olika filstorlekar med många filer som är mindre kan du minska PerFileThreadCount. Du kan öka ConcurrentFileCount genom att minska PerFileThreadCount. Det innebär att om vi antar att de flesta av våra filer är mindre i intervallet 5 GB, kan vi göra om våra beräkning:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Därför **ConcurrentFileCount** blir 96/20, vilket är 4.8, avrundat till **4**.

Du kan fortsätta att finjustera inställningarna genom att ändra **PerFileThreadCount** upp och ned beroende på distributionen av din filstorlek.

### <a name="limitation"></a>Begränsning

* **Antalet filer är mindre än ConcurrentFileCount**: Om antalet filer som du laddar upp är mindre än **ConcurrentFileCount** du har beräknat ska du minska **ConcurrentFileCount** så att det motsvarar antalet filer. Du kan använda eventuella återstående trådar för att öka **PerFileThreadCount**.

* **För många trådar**: Om du ökar antalet trådar för mycket utan att öka klusterstorleken riskerar du att prestanda försämras. Det kan uppstå konkurrensproblem när du växlar innehåll på processorn.

* **Otillräcklig samtidighet**: Om samtidigheten inte är tillräcklig kan klustret vara för litet. Du kan öka antalet noder i klustret, som ger mer samtidighet.

* **Begränsningsfel**: Om konkurrensen är för hög kan det leda till begränsningsfel. Om du råkar ut för begränsningsfel ska du antingen minska samtidigheten eller kontakta oss.

## <a name="next-steps"></a>Nästa steg
* [Använd Azure Data Lake Store för stordatakrav](data-lake-store-data-scenarios.md) 
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

