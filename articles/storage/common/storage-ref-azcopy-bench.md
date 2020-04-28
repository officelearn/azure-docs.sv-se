---
title: AzCopy bänk | Microsoft Docs
description: Den här artikeln innehåller referensinformation för AzCopy bänk kommando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "72518309"
---
# <a name="azcopy-bench"></a>azcopy bench

Kör ett prestanda mått genom att överföra test data till ett angivet mål. Test data genereras automatiskt.

Benchmark-kommandot kör samma uppladdnings process som "Copy", förutom att:

  - Det finns ingen käll parameter.  Kommandot kräver bara en mål-URL. I den aktuella versionen måste denna mål-URL referera till en BLOB-behållare.
  
  - Nytto lasten beskrivs av kommando rads parametrar, som styr hur många filer som genereras automatiskt och hur stor de är. Generations processen sker helt i minnet. Disken används inte.
  
  - Endast några av de valfria parametrarna som är tillgängliga för kopierings kommandot stöds.
  
  - Ytterligare diagnostik mäts och rapporteras.
  
  - Som standard tas överförda data bort i slutet av test körningen.

Benchmark-läget justerar sig automatiskt till antalet parallella TCP-anslutningar som ger maximalt data flöde. Den kommer att visas i slutet av den här siffran. För att förhindra automatisk justering ställer du in AZCOPY_CONCURRENCY_VALUE-miljövariabeln på ett angivet antal anslutningar.

Alla typer av vanliga autentiseringar stöds. Men den bekvämaste metoden för benchmarking är vanligt vis att skapa en tom behållare med en SAS-token och använda SAS-autentisering.

## <a name="examples"></a>Exempel

```azcopy
azcopy bench [destination] [flags]
```

Kör ett benchmark-test med standard parametrar (lämpligt för benchmarking-nätverk upp till 1 Gbit/s):

- AzCopy bänk "https://[Account]. blob. Core. Windows. net/[container]? <SAS>"

Kör ett benchmark-test som överför 100-filer, varje 2-GiB i storlek: (lämpligt för benchmarking i ett snabbt nätverk, t. ex. 10 Gbit/s): '

- AzCopy bänk "https://[Account]. blob. Core. Windows. net/[container]? <SAS>" --fil-Count 100--storlek per fil – 2G

Samma som ovan, men Använd 50 000-filer, varje 8 MiB i storlek och beräkna deras MD5-hash-värden (på samma sätt som flaggan------en-MD5 gör detta i kopierings kommandot). Syftet med----MD5 vid benchmarking är att testa om MD5-beräkningen påverkar data flödet för det valda fil antalet och storleken:

- AzCopy bänk "https://[Account]. blob. Core. Windows. net/[container]? <SAS>" --antal filer 50000--storleks per fil 8 M------a-MD5

## <a name="options"></a>Alternativ

**--BLOB-Type** -strängen definierar BLOB-typen vid målet. Används för att tillåta benchmarking av olika BLOB-typer. Identiskt med samma namngivna parameter i kopierings kommandot (standard identifiering).

**--block-size-MB** float Använd den här block storleken (anges i MIB). Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal tillåts – t. ex. 0,25. Identiskt med samma-namngivna parameter i kopierings kommandot.

**--Delete-test-data**  Om värdet är True tas benchmark-data bort i slutet av benchmark-körningen.  Ange värdet FALSE om du vill behålla data vid målet – t. ex. för att använda den för manuella tester utanför benchmark-läge (standard sant).

**--fil-räkna** uint antalet automatiskt genererade datafiler som ska användas (standard 100).

**-h,--hjälp**  Hjälp för bänk

**--sträng på loggnivå** definierar loggens utförlighet för logg filen, tillgängliga nivåer: info (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information)

**--Skicka-MD5**  Skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen/-filen. (Som standard skapas inte hashen.) Identiskt med samma-namngivna parameter i kopierings kommandot.

**--storleks per fil** sträng storlek för varje automatiskt genererad data fil. Måste vara ett tal omedelbart följt av K, M eller G. t. ex. 12K eller 200G (standard "250M").

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s UInt32**  CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text).

## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
