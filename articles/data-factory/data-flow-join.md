---
title: Azure Data Factory Data Flow Join-transformering
description: Azure Data Factory Data Flow Join-transformering
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: f95edc881e26576df216c92ff0b94c5c19bf7fa8
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727935"
---
# <a name="azure-data-factory-data-flow-join-transformation"></a>Azure Data Factory Data Flow Join-transformering

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd koppling för att kombinera data från två tabeller i ditt dataflöde. Klicka på transformeringen som ska vänstra relationen och lägga till en Join-omvandling från verktygslådan. I Join-transformering väljer du en annan dataströmmen från ditt dataflöde är rätt relation.

![Ansluta till omvandling](media/data-flow/join.png "delta")

## <a name="join-types"></a>Kopplingstyper

Välja anslutningstyp krävs för omvandlingen Join

### <a name="inner-join"></a>Inre koppling

Inre koppling släpps igenom endast rader som matchar villkor som kolumnen från båda tabellerna

### <a name="left-outer"></a>Vänster yttre

Alla rader från den vänstra stream uppfyller inte kopplingsvillkoret släpps igenom och utdatakolumner än den andra tabellen ställs in på NULL utöver alla rader som returneras av den inre kopplingen.

### <a name="right-outer"></a>Höger yttre

Alla rader från rätt dataströmmen uppfyller inte kopplingsvillkoret släpps igenom och utdatakolumner som motsvarar den andra tabellen är satt till NULL, förutom att alla rader som returneras av den inre kopplingen.

### <a name="full-outer"></a>Fullständig yttre

Fullständig ytter ger alla kolumner och rader från båda sidorna med NULL-värden för kolumner som finns inte i den andra tabellen

### <a name="cross-join"></a>Korskoppling

Specifika kryssprodukten av två strömmar med ett uttryck

## <a name="specify-join-conditions"></a>Ange kopplingsvillkor

Left Join-villkoret är från dataströmmen är anslutna till vänster om kopplingen. Right Join-villkoret är andra dataströmmen är ansluten till din anslutning till längst ned, som antingen är en direkt anslutning till en annan stream eller en referens till en annan stream.

Du måste ange minst 1 (1..n) kopplingsvillkor. De kan vara fält som antingen refereras direkt, valt från den nedrullningsbara menyn eller uttryck.

## <a name="join-performance-optimizations"></a>Ansluta till prestandaoptimering

Till skillnad från Merge koppling i verktyg som SSIS är koppling i ADF dataflöde inte en obligatorisk merge join-åtgärd. Därför behöver inte join-nycklar som ska sorteras först. Join-åtgärd utförs i Spark med Databricks baserat på optimal join-åtgärd i Spark: Koppling för sändning / kartan sida:

![Ansluta till omvandling optimera](media/data-flow/joinoptimize.png "delta optimering")

Om din datauppsättning passar in i Databricks worker nod minne, kan vi optimera Join-prestanda. Du kan också ange partitionering av dina data på Join-åtgärd för att skapa uppsättningar av data som kan passa bättre minne per worker.

## <a name="self-join"></a>Självkoppling

Du kan uppnå självkoppling villkor i ADF dataflöde med hjälp av väljer omvandlingen till alias ett befintligt stream. Först skapa en ”ny gren” från en dataström med och sedan lägga till en väljer alias hela ursprungliga dataströmmen.

![Självkoppling](media/data-flow/selfjoin.png "självkoppling")

I diagrammet ovan är väljer vi högst upp. Alla avbrytas är alias ursprungliga direkt för att ”OrigSourceBatting”. Du kan se att vi använder dataströmmen väljer alias som kopplingen till höger så att vi kan referera till samma nyckel i både vänster och höger sida av den inre koppling används i den markerade Join-transformeringen under den.
