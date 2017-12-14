---
title: "Kontrollera genomflöde och svarstid mått för ett lagringskonto i Azure portal | Microsoft Docs"
description: "Lär dig hur du verifierar genomflöde och svarstid mått för ett lagringskonto i portalen."
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
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: b3102bd4e40e10fe88c12295794da37e359c56f1
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Kontrollera genomflöde och svarstid mått för ett lagringskonto

Den här kursen är en del fyra och den sista delen av en serie. I tidigare självstudiekurser du lärt dig hur du ladda upp och hämta larges mängder slumpmässiga data till ett Azure storage-konto. Den här kursen visar hur du kan använda mått för att visa genomflöde och svarstid i Azure-portalen.

I del fyra av serien får du lära dig hur du:

> [!div class="checklist"]
> * Konfigurera diagram i Azure-portalen
> * Verifiera mått för dataflöde och svarstid

[Azure storage-mätvärden](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) använder Azure Övervakaren för att ge en enhetlig överblick över prestanda och tillgänglighet för ditt lagringskonto.

## <a name="configure-metrics"></a>Konfigurera mått

Gå till **mått (förhandsgranskning)** under **inställningar** i ditt lagringskonto.

Välj Blob från den **SUB SERVICE** listrutan.

Under **mått**, väljer du något av de mätvärden som finns i följande tabell:

Följande mått ger dig en uppfattning om den latens och genomflöde. Mått som du konfigurerar i portalen finns i genomsnitt 1 minut. Om en transaktion klar mitt i en minut är att minut data halfed för medelvärdet. Åtgärderna överföring och hämtning har tid och under förutsättning att du utdata för den faktiska mängden tid det tog att ladda upp och hämta filer i programmet. Den här informationen kan användas tillsammans med portalen mått till fullo vara införstådd genomflöde.

|Mått|Definition|
|---|---|
|**Lyckade E2E-svarstid**|Genomsnittlig slutpunkt till slutpunkt svarstid för slutförda förfrågningar som gjorts till en lagringstjänst för eller den angivna API-åtgärden. Det här värdet inkluderar bearbetningstiden som krävs i Azure Storage läsa begäran, skicka svaret och ta emot bekräftelse i svaret.|
|**Lyckade Server svarstid**|Genomsnittlig tid som används för att bearbeta en begäran om lyckade av Azure Storage. Det här värdet inkluderar inte Nätverksfördröjningen som anges i SuccessE2ELatency. |
|**Transaktioner**|Antalet begäranden som görs till en lagringstjänst för eller den angivna API-åtgärden. Antalet inkluderar lyckade och misslyckade begäranden, samt begäranden som producerade fel. I det här exemplet blockstorleken har ställts in till 100 MB. I det här fallet varje block med 100 MB betraktas som en transaktion.|
|**Ingång**|Mängden ingång data. Antalet inkluderar ingång från en extern klient i Azure Storage, liksom ingång i Azure. |
|**Utgång**|Mängden utgående data. Antalet inkluderar utgående trafik från en extern klient i Azure Storage, liksom utgång i Azure. Det här numret avspeglar därför inte fakturerbar utgång. |

Välj **senaste 24 timmarna (automatisk)** bredvid **tid**. Välj **senaste timmen** och **minut** för **tidskornighet**, klicka på **tillämpa**.

![Storage-konto mått](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Diagram kan ha fler än ett mått som har tilldelats, men tilldela fler än ett mått inaktiverar möjligheten att gruppen dimensioner.

## <a name="dimensions"></a>Mått

[Dimensioner](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) används för att titta djupare diagrammen och få mer information. Olika mått har olika dimensioner. En dimension som är tillgänglig är det **API-namnet** dimension. Den här dimensionen bryts till schemat i varje separat API-anrop. Den första bilden nedan visas ett exempeldiagram av totala transaktioner för ett lagringskonto. Den andra bilden visar samma diagram men namnet dimension som har markerats med API. Som du ser visas varje transaktion ger mer information i hur många anrop gjordes av API-namnet.

![Storage-mätvärden för konto - transaktioner utan en dimension](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Storage-mätvärden för konto - transaktioner](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Gör du genom att välja en resursgrupp för den virtuella datorn och klicka på Ta bort.

## <a name="next-steps"></a>Nästa steg

I steg fyra i serien du lärt dig om hur du visar mätvärden för exempellösning, till exempel att:

> [!div class="checklist"]
> * Konfigurera diagram i Azure-portalen
> * Verifiera mått för dataflöde och svarstid

Den här länken om du vill se förskapad lagring prover.

> [!div class="nextstepaction"]
> [Azure storage skriptexempel](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md