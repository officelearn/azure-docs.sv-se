---
title: azoskopibänk | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för kommandot askakopibänk.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518309"
---
# <a name="azcopy-bench"></a>azcopy bench

Kör ett prestandariktmärke genom att överföra testdata till ett angivet mål. Testdata genereras automatiskt.

Kommandot Benchmark kör samma uppladdningsprocess som "kopiera", förutom att:

  - Det finns ingen källparameter.  Kommandot kräver bara en mål-URL. I den aktuella versionen måste den här mål-URL:en referera till en blob-behållare.
  
  - Nyttolasten beskrivs av kommandoradsparametrar, som styr hur många filer som genereras automatiskt och hur stora de är. Generationsprocessen sker helt i minnet. Disk används inte.
  
  - Endast ett fåtal av de valfria parametrar som är tillgängliga för kopieringskommandot stöds.
  
  - Ytterligare diagnostik mäts och rapporteras.
  
  - Som standard tas de överförda data bort i slutet av testkörningen.

Benchmark-läget justerar sig automatiskt till antalet parallella TCP-anslutningar som ger maximalt dataflöde. Det kommer att visa det numret i slutet. Om du vill förhindra automatisk justering ställer du in AZCOPY_CONCURRENCY_VALUE miljövariabeln på ett visst antal anslutningar.

Alla vanliga autentiseringstyper stöds. Den mest praktiska metoden för benchmarking är dock vanligtvis att skapa en tom behållare med en SAS-token och använda SAS-autentisering.

## <a name="examples"></a>Exempel

```azcopy
azcopy bench [destination] [flags]
```

Kör ett benchmark-test med standardparametrar (lämplig för benchmarking av nätverk upp till 1 Gbit/s):"

- azcopy bänk "https://[account].blob.core.windows.net/[container]? <SAS>"

Kör ett benchmark-test som laddar upp 100 filer, var och en 2 GiB i storlek: (lämplig för benchmarking på ett snabbt nätverk, t.ex. 10 Gbit/s):"

- azcopy bänk "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 100 --size-per-file 2G

Samma som ovan, men använd 50.000 filer, varje 8 MiB i storlek och beräkna sina MD5 hashar (på samma sätt som --put-md5 flaggan gör detta i kopian kommandot). Syftet med --put-md5 vid benchmarking är att testa om MD5-beräkning påverkar dataflödet för det valda filantalet och storleken:

- azcopy bänk "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 50000 --size-per-file 8M --put-md5 --file-count 50000 --size-per-file 8M --put-md5 --file-count-count 5000 --size-per-file 8M --put-md5 -

## <a name="options"></a>Alternativ

**--blob-typ** sträng Definierar typen av blob vid målet. Används för att tillåta benchmarking av olika blobtyper. Identisk med samma namngivna parameter i kopieringskommandot (standard "Identifiera").

**--block-size-mb** float Använd den här blockstorleken (angiven i MiB). Standard beräknas automatiskt baserat på filstorlek. Decimalfraktioner är tillåtna - t.ex. Identisk med parametern med samma namn i kopieringskommandot.

**--delete-test-data**  Om värdet är true kommer referensdata att raderas i slutet av referensvärdet.  Ställ in den på false om du vill behålla data på destinationen - t.ex.

**--file-count** uint Antalet automatiskt genererade datafiler som ska användas (standard 100).

**-h, --hjälp**  Hjälp för bänk

**--log-level** string Definiera loggvergialiteten för loggfilen, tillgängliga nivåer: INFO(alla begäranden/svar), VARNING(långsamma svar), FEL (endast misslyckade begäranden) och NONE(inga utdataloggar). (standard "INFO")

**--put-md5**  Skapa en MD5-hash för varje fil och spara hash-värdet som egenskapen Content-MD5 för målblobben/-filen. (Som standard skapas INTE hash-värdet.) Identisk med parametern med samma namn i kopieringskommandot.

**--size-per-file** sträng Storlek för varje automatiskt genererad datafil. Måste vara ett tal omedelbart följt av K, M eller G. T.ex. 12k eller 200G (standard "250M").

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

**--cap-mbps uint32 --cap-mbps**  Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.

**--utdata-typ** sträng Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text". (standard "text").

## <a name="see-also"></a>Se även

- [azcopy (azcopy)](storage-ref-azcopy.md)
