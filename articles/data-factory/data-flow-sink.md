---
title: Konfigurera en mottagare omvandling i funktionen för mappning av dataflöde i Azure Data Factory
description: Lär dig hur du ställer in en mottagare omvandling i Data mappning flöda.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 4341cbb0e24330d535f5211c088f0068eab33af7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596259"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformering för ett dataflöde för mottagare

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

När du omvandla ditt dataflöde mottagare du data till en mål-datauppsättning. Välj en definition för datauppsättningen för utdata mål omvandling mottagare. Du kan ha många mottagare transformationer som kräver ditt dataflöde.

Konto för schemat drift och förändringar i den inkommande data mottagare utdata till en mapp utan ett definierat schema i datauppsättningen för utdata. Du kan också ta hänsyn till kolumnen ändringar i dina källor genom att välja **Tillåt schemat drift** i källan. Sedan fält automap alla i mottagaren.

![Alternativen på fliken mottagare, inklusive alternativet Automatisk karta](media/data-flow/sink1.png "mottagare 1")

Om du vill mottagare alla inkommande fält, aktivera **Automatisk karta**. Välj de fält till mottagare till målet eller ändra namnen på fälten vid målet genom att stänga av **Automatisk karta**. Öppna sedan den **mappning** fliken mappning av utdata.

![Alternativen på fliken mappning](media/data-flow/sink2.png "mottagare 2")

## <a name="output"></a>Utdata 
Utdata transformerade data till en mapp för Azure Blob storage eller Data Lake Storage mottagare typer. Spark genererar partitionerade data utdatafilerna baserat på schemat som mottagare transformeringen använder. 

Du kan ange partitioneringsschemat från den **optimera** fliken. Om du vill att Data Factory för att slå samman dina utdata till en enda fil, Välj **enkel partition**.

![Alternativen på fliken Optimize](media/data-flow/opt001.png "alternativ för mottagare")

## <a name="field-mapping"></a>Fältmappning

På den **mappning** fliken av din mottagare omvandling, kan du mappa kolumnerna inkommande till vänster till mål till höger. När du mottagare dataflöden för filer skrivs alltid nya filer till en mapp i Data Factory. När du ansluter till en databas-datauppsättning, du kan skapa en ny tabell som använder det här schemat genom att ange **spara princip** till **överskrivning**. Eller infoga nya rader i en befintlig tabell och mappa fält i det befintliga schemat. 

![Fliken mappning](media/data-flow/sink2.png "egenskaperna")

I mappningstabellen kan du markera flera för att länka flera kolumner, ta bort länk från flera kolumner eller mappa flera rader till samma kolumn.

Mappa alltid den inkommande uppsättningen fält till ett mål som de är och fullständigt accepterar flexibelt schemadefinitioner, Välj **Tillåt schemat drift**.

![Fliken mappning Visa fält som är mappade till kolumnerna i datauppsättningen](media/data-flow/multi1.png "flera alternativ")

Om du vill återställa din kolumnmappningarna, Välj **mappa**.

![Fliken mottagare](media/data-flow/sink1.png "mottagare en")

Välj **verifiera schemat** misslyckas sink om schemat ändras.

Välj **Rensa mappen** trunkera innehållet i mappen mottagare innan du skriver målfilerna i den målmappen.

## <a name="file-name-options"></a>Alternativ för namn

Ställ in filnamngivning: 

   * **Standard**: Att Spark kan namnge filer baserat på standardinställningarna för en del.
   * **Mönstret**: Ange ett mönster för din utdatafilerna. Till exempel **lån [n]** skapar loans1.csv, loans2.csv och så vidare.
   * **Per partition**: Ange ett filnamn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn.
   * **Utdata till en enskild fil**: Med det här alternativet kombineras ADF partitionerade utdatafilerna till en enda med namnet på filen. Om du vill använda det här alternativet om ska din datauppsättning matcha ett mappnamn. Dessutom var medveten om att den här sammanslagningsåtgärden kan eventuellt inte utifrån nodstorlek.

> [!NOTE]
> Filen operations start endast när du kör aktiviteten kör dataflöde. De startar inte i Data flöda felsökningsläge.

## <a name="database-options"></a>Databasalternativ

Välj databasinställningar:

* **Uppdatera metoden**: Standardvärdet är att tillåta infogningar. Rensa **Tillåt insert** om du vill stoppa att lägga till rader från källan. Om du vill uppdatera, upsert, eller ta bort rader, först lägga till en alter-raden transformering taggen rader för dessa åtgärder. 
* **Återskapa tabellen**: Ta bort eller skapa din måltabellen innan dataflöde har slutförts.
* **Trunkering tabell**: Ta bort alla rader från din måltabellen innan dataflöde har slutförts.
* **Batchstorlek**: Ange ett tal till bucketen skrivningar i segment. Använd det här alternativet för stora databelastningar. 
* **Aktivera mellanlagring**: Använd PolyBase när du läser in Azure Data Warehouse som din datauppsättning för mottagare.

![Fliken Inställningar som visar alternativ för SQL-mottagare](media/data-flow/alter-row2.png "SQL-alternativ")

> [!NOTE]
> I dataflöde, kan du dirigera Data Factory för att skapa en ny tabelldefinition i måldatabasen. Ange en datauppsättning för att skapa tabelldefinitionen, omvandling av den mottagare som har ett nytt tabellnamn. I SQL-datauppsättning nedan tabellnamnet, väljer **redigera** och ange ett nytt tabellnamn. Omvandling mottagare aktivera sedan **Tillåt schemat drift**. Ange **Importschema** till **ingen**.

![Inställningarna för datauppsättningen för SQL, som visar var du redigera tabellnamnet](media/data-flow/dataset2.png "SQL-schemat")

> [!NOTE]
> När du uppdatera eller ta bort rader i databasen-mottagaren, måste du ange nyckelkolumn. Den här inställningen transformeringen alter rad att fastställa unik rad i data movement library (DML).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ditt dataflöde, lägga till en [Data flödesaktivitet till din pipeline](concepts-data-flow-overview.md).
