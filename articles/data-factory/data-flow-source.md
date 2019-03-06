---
title: Azure Data Factory mappning Dataomvandling Flow källa
description: Azure Data Factory mappning Dataomvandling Flow källa
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: e33606e0b5be0db5306cfd42f87baf59671eb2a8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451737"
---
# <a name="mapping-data-flow-source-transformation"></a>Mappningen Dataomvandling Flow källa

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Käll-transformeringen konfigurerar en datakälla som du vill använda för att hämta data till ditt dataflöde. Du kan ha fler än en källa transformeringen i en enda dataflöde. Alltid börja utforma dina Data flödar med källan.

> [!NOTE]
> Varje dataflöde kräver minst en transformering av källa. Lägg till så många ytterligare datakällor som du behöver för att slutföra din Datatransformationer. Du kan ansluta till dessa källor tillsammans med ett Join- eller Union omvandling.

![Käll-omvandling alternativ](media/data-flow/source.png "källa")

Varje transformering av Data flöda källa måste vara kopplad till exakt en Data Factory-datauppsättningen som definierar formen och var dina data att skriva till eller läsa från. Du kan använda jokertecken och filen listor i din källan för att arbeta med fler än en fil i taget.

## <a name="data-flow-staging-areas"></a>Dataflöde Mellanlagringsområden

Dataflödet fungerar med ”mellanlagring” datauppsättningar som finns i Azure. Dessa datauppsättningar för flödet av data används för mellanlagring för att utföra din dataomvandlingar data. Data Factory har åtkomst till nästan 80 olika inbyggda anslutningar. För att inkludera data från de andra källorna i dina dataflöde, först mellanlagra data i en av dessa Data flöda datauppsättning mellanlagringsområden med hjälp av Kopieringsaktiviteten.

## <a name="options"></a>Alternativ

### <a name="allow-schema-drift"></a>Tillåt schemat drift
Välj Tillåt schemat Drift om källkolumner ändras ofta. Den här inställningen gör att alla inkommande fält från källan kan passera transformeringar som mottagaren.

### <a name="validate-schema"></a>Validera schemat

![Offentlig datakälla](media/data-flow/source1.png "offentliga källa 1")

Körningen av dataflödet misslyckas om den inkommande versionen för datakällan inte matchar definierat schema.

### <a name="sampling"></a>Samling
Använda Sampling för att begränsa antalet rader från källan.  Detta är användbart när du behöver bara ett exempel på dina källdata för testning och felsökning.

## <a name="define-schema"></a>Definiera Schema

![Käll-omvandling](media/data-flow/source2.png "datakällan 2")

Du bör definiera datatyperna för varje fält här i käll-transformering för käll-filtyper som inte är starkt typifierad (d.v.s. flata filer istället för Parquet-filer). Du kan senare ändra kolumnnamnen i en Select transformation och datatyper i en härledd kolumn omvandling. 

![Käll-omvandling](media/data-flow/source003.png "datatyper")

Du kan ändra datatyper i en efterföljande Select transformation för starkt typifierade källor. 

### <a name="optimize"></a>Optimera

![Käll-partitioner](media/data-flow/sourcepart.png "partitionering")

På fliken Optimize för käll-omvandling visas en ytterligare partitionering typ med namnet ”källa”. Detta kommer endast Ljus upp när du har valt Azure SQL DB som källan. Det beror på att ADF kommer vill parallellisera anslutningar för att köra stora frågor mot Azure SQL DB-källa.

Partitionera data på din SQL DB-källan är valfritt, men det är användbart för stora frågor. Du kan välja mellan två alternativ:

### <a name="column"></a>Kolumn

Markera en kolumn till partition på från din källtabellen. Du måste också ange det maximala antalet anslutningar.

### <a name="query-condition"></a>Frågevillkoret

Du kan välja att partitionera anslutningar baserat på en fråga. För det här alternativet kan du enkelt uttryckt i innehållet i en WHERE-predikat. T.ex. år > 1980

## <a name="source-file-management"></a>Hantering av källa
![Nya inställningar för datakälla](media/data-flow/source2.png "nya inställningar")

* Wilcard sökväg för att välja ett antal filer från din källmapp som matchar ett mönster. Detta åsidosätter alla filer som du har angett i definitionen av datauppsättningen.
* Lista över filer. Samma som en uppsättning. Peka en textfil som du skapar en lista med relativa sökvägen filer som ska bearbetas.
* Kolumnen för att lagra filnamn lagrar namnet på filen från källan i en kolumn i dina data. Ange ett nytt namn för att lagra filen namnsträngen.
* När du har slutfört (kan du göra något med källfilen när dataflödet körs tar du bort källfiler eller flytta källfilerna. Sökvägar för flytt är relativa sökvägar.

### <a name="sql-datasets"></a>SQL-datauppsättningar

När du använder Azure SQL DB eller Azure SQL DW som källan, har du ytterligare alternativ.

* Fråga: Ange en SQL-fråga för källan. Ställa en fråga åsidosätter en tabell som du har valt i datauppsättningen. Observera att Order By-satser inte stöds här.

* Batchstorlek: Ange en batchstorlek för att dela upp stora mängder data till batch medelstora läsningar.

> [!NOTE]
> Filinställningar för åtgärden körs bara när Data flöda körs från en pipeline som körs (pipeline debug eller körningen kör) med aktiviteten kör dataflöde i en pipeline. Filåtgärder körs inte i felsökningsläge dataflöde.

### <a name="projection"></a>Projektion

![Projektion](media/data-flow/source3.png "projektion")

Liknar scheman i datauppsättningar, projektion i källan definierar datakolumner, datatyper och dataformat från datakällan. Klicka på ”identifiera datatyp” om du vill ställa ADF att försöka hämta exempel och härleder datatyperna som om du har en textfil med inget definierat schema. Du kan ange standarddata format för automatisk identifiering med hjälp av knappen ”Definiera standardformatet”. Du kan ändra datatyperna för kolumnen vid omvandling av en efterföljande härledd kolumn. Kolumnnamnen kan ändras med hjälp av väljer transformeringen.

![Standardformat](media/data-flow/source2.png "standardformat")

## <a name="next-steps"></a>Nästa steg

Börja skapa din Dataomvandling med [härledd kolumn](data-flow-derived-column.md) och [Välj](data-flow-select.md).
