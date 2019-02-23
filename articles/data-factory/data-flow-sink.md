---
title: Azure Data Factory mappning Dataomvandling Flow mottagare
description: Azure Data Factory mappning Dataomvandling Flow mottagare
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dba043721c2d81b7fe2c254f62328e54bb959cdc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729380"
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

### <a name="output-settings"></a>Inställningar för utdata

Skriv över trunkera tabellen om den finns, sedan återskapa den och läsa in data. Lägg till att infoga nya rader. Om tabellen från tabellnamnet datauppsättningen inte finns alls i mål-ADW, ska dataflöde skapa tabellen och sedan läsa in data.

Om du avmarkerar ”Automatisk karta” kan mappa du fälten till din måltabell manuellt.

![Mottagare ADW alternativ](media/data-flow/adw2.png "adw mottagare")

#### <a name="field-mapping"></a>Fältmappning

På fliken mappning för den mottagare omvandlingen kan du mappa kolumnerna inkommande (vänster sida) till målet (höger sida). När du mottagare dataflöden för filer skrivs ADF alltid nya filer till en mapp. När du ansluter till en databas-datauppsättning, du kan välja att antingen skapa en ny tabell med det här schemat (värdet spara principen ”Skriv över”) eller infoga nya rader i en befintlig tabell och mappa fälten till det befintliga schemat.

Du kan använda flerval i mappningstabellen länka flera kolumner med ett enda klick, ta bort länk från flera kolumner eller mappa flera rader till samma kolumn.

![Fältmappning](media/data-flow/multi1.png "flera alternativ")

Om du vill återställa dina kolumnmappningar genom att trycka på knappen ”mappa om” att återställa mappningarna.

![Anslutningar](media/data-flow/maxcon.png "Anslutningar")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>Uppdateringar till mottagare omvandlingen av ADF V2 GA-versionen

![Alternativ för mottagare](media/data-flow/sink1.png "mottagare en")

![Alternativ för mottagare](media/data-flow/sink2.png "egenskaperna")

* Tillåt schemat Drift-och verifiera schemat är nu tillgängliga i mottagare. Detta kan du få ADF till fullständigt acceptera flexibla schemadefinitioner (Schema Drift) eller misslyckas Sink om schemat ändras (verifiera Schema).

* Ta bort mappen. ADF trunkerar mappinnehåll mottagare innan du skriver målfilerna i den målmappen.

* Alternativ för namn

   * Standard: Gör att Spark kan namnge filer baserat på standardinställningarna för en del
   * Mönster: Ange ett namn för din utdatafiler
   * Per partition: Ange ett filnamn per partition
   * Som data i kolumnen: Ange utdatafilen till värdet för en kolumn

> [!NOTE]
> Filåtgärder körs bara när du kör aktiviteten kör dataflöde inte i Data flöda felsökningsläge

Du kan ange med SQL-typer för mottagare:

* Trunkera tabellen
* Skapa tabellen (utför släpp/skapa)
* Batchstorlek för stora mängder data läses in. Ange ett tal till bucketen skrivningar i segment.

![Fältmappning](media/data-flow/sql001.png "SQL-alternativ")

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ditt dataflöde, lägga till en [köra Data flödesaktivitet till din pipeline](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
