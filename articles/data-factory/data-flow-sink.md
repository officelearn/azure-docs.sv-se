---
title: Azure Data Factory mappning Dataomvandling Flow mottagare
description: Azure Data Factory mappning Dataomvandling Flow mottagare
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 3829fb3c045b149552d3f022e31f30f9cfae8182
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852448"
---
# <a name="mapping-data-flow-sink-transformation"></a>Mappningen Dataomvandling Flow mottagare

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Alternativ för mottagare](media/data-flow/windows1.png "mottagare 1")

När ditt flöde transformering av data, kan du mottagare omvandlade data till en mål-datauppsättning. Du kan välja definitionen för datauppsättningen som du vill använda för mål-utdata för omvandling mottagare. Du kan ha så många mottagare omvandling som kräver ditt dataflöde.

En vanlig metod att kompensera för att ändra inkommande data och att kompensera för drift av schemat är att mottagare utdata till en mapp utan ett definierat schema i datauppsättningen för utdata. Du kan också ta hänsyn till alla ändringar i kolumnen i dina källor genom att välja ”Tillåt schemat Drift” på källan och sedan automap alla fält i mottagaren.

Du kan välja att skriva över, lägga till eller misslyckas dataflödet när sänks till en datauppsättning.

Du kan också välja ”automap” till alla inkommande fält för mottagare. Om du vill välja vilka fält som du vill mottagare till målet, eller om du vill ändra namnen på fälten vid målet, Välj ”Off” för ”automap” och klicka sedan på fliken mappning för att mappa fält för tabellutdata:

![Alternativ för mottagare](media/data-flow/sink2.png "mottagare 2")

## <a name="output-to-one-file"></a>Utdata till en fil
För Azure Storage Blob eller Data Lake typer av mottagare kommer du sparar omvandlade data till en mapp. Spark genererar partitionerade data utdatafilerna baserat på schemat som används i mottagare transformeringen. Du kan ange partitioneringsschemat genom att klicka på fliken ”Optimize”. Om du vill ha ADF om du vill slå samman dina utdata till en enda fil, klicka på knappen ”enskild Partition”.

![Alternativ för mottagare](media/data-flow/opt001.png "alternativ för mottagare")

## <a name="field-mapping"></a>Fältmappning

På fliken mappning för den mottagare omvandlingen kan du mappa kolumnerna inkommande (vänster sida) till målet (höger sida). När du mottagare dataflöden för filer skrivs ADF alltid nya filer till en mapp. När du ansluter till en databas-datauppsättning, du kan välja att antingen skapa en ny tabell med det här schemat (värdet spara principen ”Skriv över”) eller infoga nya rader i en befintlig tabell och mappa fälten till det befintliga schemat.

Du kan använda flerval i mappningstabellen länka flera kolumner med ett enda klick, ta bort länk från flera kolumner eller mappa flera rader till samma kolumn.

När du vill ta den inkommande uppsättningen fält och mappa den till ett mål som alltid-är, ställa in inställningen ”Tillåt schemat Drift”.

![Fältmappning](media/data-flow/multi1.png "flera alternativ")

Om du vill återställa dina kolumnmappningar genom att trycka på knappen ”mappa om” att återställa mappningarna.

![Alternativ för mottagare](media/data-flow/sink1.png "mottagare en")

![Alternativ för mottagare](media/data-flow/sink2.png "egenskaperna")

* Tillåt schemat Drift-och verifiera schemat är nu tillgängliga i mottagare. Detta kan du få ADF till fullständigt acceptera flexibla schemadefinitioner (Schema Drift) eller misslyckas Sink om schemat ändras (verifiera Schema).

* Ta bort mappen. ADF trunkerar mappinnehåll mottagare innan du skriver målfilerna i den målmappen.

## <a name="file-name-options"></a>Alternativ för namn

   * Standard: Gör att Spark kan namnge filer baserat på standardinställningarna för en del
   * Mönster: Ange ett namn för din utdatafiler
   * Per partition: Ange ett filnamn per partition
   * Som data i kolumnen: Ange utdatafilen till värdet för en kolumn

> [!NOTE]
> Filåtgärder körs bara när du kör aktiviteten kör dataflöde inte i Data flöda felsökningsläge

## <a name="database-options"></a>Databasalternativ

* Tillåt insert-, update-, delete-, upsertar. Standardvärdet är att tillåta infogningar. Om du vill uppdatera, upsert eller infoga rader måste du först lägga till en alter rad transformering taggen rader för dessa specifika åtgärder.
* Trunkera tabellen (tar bort alla rader från din måltabellen innan du slutför dataflödet)
* Skapa tabellen (utför släpp/skapa din måltabellen innan du slutför dataflödet)
* Batchstorlek för stora mängder data läses in. Ange ett tal till bucketen skrivningar i segment
* Aktivera mellanlagring: Detta instruerar ADF om du vill använda Polybase vid inläsning av Azure Data Warehouse som din datauppsättning för mottagare

![Alternativ för SQL-mottagare](media/data-flow/alter-row2.png "SQL-alternativ")

> [!NOTE]
> När du uppdaterar eller tar bort rader i databasen-mottagaren, måste du ange nyckelkolumn. På så sätt kan Alter rad kan fastställa unik rad i DML.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ditt dataflöde, lägga till en [köra Data flödesaktivitet till din pipeline](concepts-data-flow-overview.md).
