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
ms.openlocfilehash: 40ff6c6c76e255945681e678ef296ffcf9978f61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485178"
---
# <a name="azcopy-benchmark"></a>AzCopy-prestandatest

Kör ett prestanda mått genom att överföra test data till ett angivet mål. Test data genereras automatiskt.

Benchmark-kommandot kör samma uppladdnings process som "Copy", förutom att:

  - Det finns ingen käll parameter.  Kommandot kräver bara en mål-URL. 
  
  - Nytto lasten beskrivs av kommando rads parametrar som styr hur många filer som genereras automatiskt och deras storlek. Generations processen sker helt i minnet. Disken används inte.
  
  - Endast några av de valfria parametrarna som är tillgängliga för kopierings kommandot stöds.
  
  - Ytterligare diagnostik mäts och rapporteras.
  
  - Som standard tas överförda data bort i slutet av test körningen.

Benchmark-läget justerar sig automatiskt till antalet parallella TCP-anslutningar som ger maximalt data flöde. Den kommer att visas i slutet av den här siffran. För att förhindra autojustering ställer du in AZCOPY_CONCURRENCY_VALUE-miljövariabeln på ett angivet antal anslutningar.

Alla typer av vanliga autentiseringar stöds. Men den bekvämaste metoden för benchmarking är vanligt vis att skapa en tom behållare med en SAS-token och använda SAS-autentisering.

## <a name="examples"></a>Exempel

```azcopy
azcopy benchmark [destination] [flags]
```

Kör ett benchmark-test med standard parametrar (lämpligt för benchmarking-nätverk upp till 1 Gbit/s):

- AzCopy bänk "https://[Account]. blob. Core. Windows. net/[container]? <SAS> "

Kör ett benchmark-test som överför 100-filer, varje 2-GiB i storlek: (lämpligt för benchmarking i ett snabbt nätverk, till exempel 10 Gbit/s): '

- AzCopy bänk "https://[Account]. blob. Core. Windows. net/[container]? <SAS> " --fil-Count 100--storlek per fil – 2G

Kör ett benchmark-test men Använd 50 000-filer, varje 8 MiB i storlek och beräkna deras MD5-hashar (på samma sätt som `--put-md5` flaggan gör detta i kopierings kommandot). Syftet med `--put-md5` när benchmarking är att testa om MD5-beräkningen påverkar data flödet för det valda fil antalet och storleken:

- AzCopy bänk "https://[Account]. blob. Core. Windows. net/[container]? <SAS> " --antal filer 50000--storleks per fil 8 M------a-MD5

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

**--sträng för betrodd-Microsoft-suffix** anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
