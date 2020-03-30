---
title: Prestandajustering för Azure Data Lake Storage Gen1 – PowerShell
description: Tips om hur du kan förbättra prestanda när du använder Azure PowerShell med Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904567"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Prestandajusteringsvägledning för användning av PowerShell med Azure Data Lake Storage Gen1

I den här artikeln beskrivs de egenskaper som du kan ställa in för att få bättre prestanda när du använder PowerShell för att arbeta med Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Prestandarelaterade egenskaper

| Egenskap            | Default | Beskrivning |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Med den här parametern kan du välja antalet parallella trådar för att ladda upp eller ned varje fil. Detta nummer representerar max trådar som kan tilldelas per fil, men du kan få färre trådar beroende på ditt scenario (till exempel, om du laddar upp en 1-KB fil, får du en tråd även om du ber om 20 trådar).  |
| ConcurrentFileCount | 10      | Den här parametern är specifikt för att ladda upp och ned mappar. Den här parametern anger antalet samtidiga filer som kan laddas upp eller ned. Detta nummer representerar det maximala antalet samtidiga filer som kan laddas upp eller laddas ned på en gång, men du kan få mindre samtidighet beroende på ditt scenario (till exempel, om du laddar upp två filer, får du två samtidiga filer uppladdningar även om du frågar för 15). |

**Exempel:**

Det här kommandot hämtar filer från Data Lake Storage Gen1 till användarens lokala enhet med 20 trådar per fil och 100 samtidiga filer.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Så här bestämmer du egenskapsvärden

Nästa fråga du kan ha är hur du avgör vilket värde som ska tillhandahållas för prestandarelaterade egenskaper. Här är några riktlinjer som du kan använda.

* **Steg 1: Bestäm det totala antalet trådar** - Börja med att beräkna det totala gängantalet som ska användas. Som en allmän riktlinje bör du använda sex trådar för varje fysisk kärna.

    `Total thread count = total physical cores * 6`

    **Exempel:**

    Vi antar att du kör PowerShell-kommandon från en virtuell D14-dator med 16 kärnor

    `Total thread count = 16 cores * 6 = 96 threads`

* **Steg 2: Beräkna PerFileThreadCount** - Vi beräknar vår PerFileThreadCount baserat på storleken på filerna. För filer som är mindre än 2,5 GB behöver du inte ändra den här parametern eftersom standardvärdet 10 är tillräckligt. För filer som är större än 2,5 GB bör du använda 10 trådar som bas för de första 2,5 GB och lägga till 1 tråd för varje ytterligare 256 MB ökning av filstorleken. Om du kopierar en mapp med många olika filstorlekar bör du överväga att gruppera dem enligt liknande filstorlekar. Olika stora filstorlekar kan orsaka bristfälliga prestanda. Om du inte kan gruppera liknande filstorlekar ska du ställa in PerFileThreadCount baserat på den största filstorleken.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Exempel:**

    Förutsatt att du har 100 filer från 1 GB till 10 GB, använder vi 10 GB som den största filstorleken för ekvation, som skulle läsa som följande.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Steg 3: Beräkna concurrentFilecount** - Använd det totala antalet trådar och PerFileThreadCount för att beräkna ConcurrentFileCount baserat på följande ekvation:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Exempel:**

    Utifrån de exempelvärden vi har använt

    `96 = 40 * ConcurrentFileCount`

    Så, **ConcurrentFileCount** är **2,4**, vilket vi kan runda av till **2**.

## <a name="further-tuning"></a>Ytterligare justering

Du kan behöva justera ytterligare eftersom det finns en mängd filstorlekar att arbeta med. Den föregående beräkningen fungerar bra om alla eller de flesta av filerna är större och närmare intervallet 10 GB. Om det finns i stället många olika filstorlekar med många filer som är mindre kan du minska PerFileThreadCount. Du kan öka ConcurrentFileCount genom att minska PerFileThreadCount. Så, om vi antar att de flesta av våra filer är mindre i 5 GB-serien, kan vi göra om vår beräkning:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Så blir **ConcurrentFileCount** 96/20, vilket är 4,8, avrundat till **4**.

Du kan fortsätta att finjustera inställningarna genom att ändra **PerFileThreadCount** upp och ned beroende på distributionen av din filstorlek.

### <a name="limitation"></a>Begränsning

* **Antalet filer är mindre än ConcurrentFileCount**: Om antalet filer som du laddar upp är mindre än **ConcurrentFileCount** du har beräknat ska du minska **ConcurrentFileCount** så att det motsvarar antalet filer. Du kan använda eventuella återstående trådar för att öka **PerFileThreadCount**.

* **För många trådar**: Om du ökar antalet trådar för mycket utan att öka klusterstorleken riskerar du att prestanda försämras. Det kan uppstå konkurrensproblem när du växlar innehåll på processorn.

* **Otillräcklig samtidighet**: Om samtidigheten inte är tillräcklig kan klustret vara för litet. Du kan öka antalet noder i klustret, vilket ger dig mer samtidighet.

* **Begränsningsfel**: Om konkurrensen är för hög kan det leda till begränsningsfel. Om du råkar ut för begränsningsfel ska du antingen minska samtidigheten eller kontakta oss.

## <a name="next-steps"></a>Nästa steg

* [Använda Azure Data Lake Storage Gen1 för stordatakrav](data-lake-store-data-scenarios.md) 
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

