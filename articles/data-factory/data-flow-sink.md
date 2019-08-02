---
title: Konfigurera en Sink-omvandling i funktionen för att mappa data flöde i Azure Data Factory
description: Lär dig hur du konfigurerar en Sink-omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b228dfd92fe389d196a65f7152ef22751842f4bb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640281"
---
# <a name="sink-transformation-for-a-data-flow"></a>Sink-transformering för ett data flöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

När du har transformerat ditt data flöde kan du sinka data till en mål data uppsättning. I omvandling för mottagare väljer du en data uppsättnings definition för målets utgående data. Du kan ha så många handfat som dina data flöden kräver.

För att kunna beakta schema avvikelser och ändringar i inkommande data, ska du skicka ut utdata till en mapp utan ett definierat schema i data uppsättningen för utdata. Du kan också konto för kolumn ändringar i dina källor genom att välja **Tillåt schema avvikelse** i källan. Sedan kan du Automap alla fält i mottagaren.

![Alternativen på fliken mottagare, inklusive alternativet automatisk mappning](media/data-flow/sink1.png "mottagare 1")

Om du vill ta Sink alla inkommande fält aktiverar du **Auto Map**. Om du vill välja vilka fält som ska översättas till målet, eller om du vill ändra namnen på fälten vid målet, så Stäng av **automatisk mappning**. Öppna sedan fliken **mappning** för att mappa utmatnings fält.

![Alternativ på fliken mappning](media/data-flow/sink2.png "mottagare 2")

## <a name="output"></a>Output 
För Azure Blob Storage-eller Data Lake Storage Sink-typer skriver du ut transformerade data till en mapp. Spark genererar partitionerade utdatafiler baserat på det partitionerings schema som används av Sink-omvandlingen. 

Du kan ställa in partitionerings schema från fliken Optimize ( **optimera** ). Om du vill att Data Factory sammanfoga dina utdata till en enda fil väljer du **enskild partition**.

![Alternativ på fliken optimera](media/data-flow/opt001.png "mottagar alternativ")

## <a name="field-mapping"></a>Fält mappning
På fliken **mappning** i din Sink-omvandling kan du mappa de inkommande kolumnerna till vänster till målen till höger. När du tar Sink-data flöden till filer skriver Data Factory alltid nya filer till en mapp. När du mappar till en databas data uppsättning väljer du åtgärds alternativ för databas tabell för att infoga, uppdatera, upsert eller ta bort.

![Fliken mappning](media/data-flow/sink2.png "Handfat")

I mappnings tabellen kan du välja att länka flera kolumner, ta bort flera kolumner eller mappa flera rader till samma kolumn namn.

Om du alltid vill mappa inkommande uppsättning fält till ett mål som de är och för att fullständigt godkänna flexibla schema definitioner väljer du **Tillåt schema avvikelse**.

![Fliken mappning, som visar fält som har mappats till kolumner i data uppsättningen](media/data-flow/multi1.png "flera alternativ")

Om du vill återställa kolumn mappningarna väljer du **åter mappning**.

![Fliken mottagare](media/data-flow/sink1.png "Mottagar en")

Välj **Verifiera schema** om du vill stoppa sinken om schemat ändras.

Välj **Rensa mappen** för att trunkera innehållet i mappen Sink innan du skriver målfiler i den målmappen.

## <a name="rule-based-mapping"></a>Regel baserad mappning
När du inaktiverar automatisk mappning kan du välja att lägga till en kolumnbaserade mappning (fast mappning) eller regelbaserade mappning. Med hjälp av regelbaserade mappningar kan du skriva uttryck med mönster matchning. 

![Regel baserad mappning](media/data-flow/rules4.png "Regel baserad mappning")

När du väljer regelbaserade mappning instruerar du ADF att utvärdera matchnings uttrycket så att det matchar inkommande mönster regler och definierar de utgående fält namnen. Du kan lägga till valfri kombination av både fält-och regelbaserade mappningar. Fält namn genereras sedan vid körning med ADF baserat på inkommande metadata från källan. Du kan visa namnen på de genererade fälten under fel sökning och i fönstret data förhands granskning.

Information om mönster matchning finns i [kolumn mönster dokumentation](concepts-data-flow-column-pattern.md).

## <a name="file-name-options"></a>Alternativ för fil namn

Konfigurera fil namn: 

   * **Standard**: Tillåt Spark att namnge filer baserat på del standarder.
   * **Mönster**: Ange ett mönster för utdatafilerna. Till exempel kommer **lån [n]** att skapa loans1. csv, loans2. csv och så vidare.
   * **Per partition**: Ange ett fil namn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn.
   * **Utdata till en enda fil**: Med det här alternativet kombinerar ADF de partitionerade utdatafilerna till en enda namngiven fil. Om du vill använda det här alternativet bör data uppsättningen matcha till ett mappnamn. Tänk också på att den här sammanslagnings åtgärden kan sluta fungera baserat på Node-storlek.

> [!NOTE]
> Fil åtgärder startar bara när du kör aktiviteten kör data flöde. De startar inte i fel söknings läge för data flöde.

## <a name="database-options"></a>Databas alternativ

Välj databas inställningar:

* **Uppdaterings metod**: Standardvärdet är att tillåta infogningar. Rensa **Tillåt infogning** om du vill sluta infoga nya rader från källan. Om du vill uppdatera, upsert eller ta bort rader måste du först lägga till en Alter-Row-omvandling för att tagga rader för dessa åtgärder. 
* **Återskapa tabell**: Släpp eller skapa mål tabellen innan data flödet har slutförts.
* **Trunkera tabell**: Ta bort alla rader från mål tabellen innan data flödet har slutförts.
* **Batchstorlek**: Ange ett tal för Bucket skrivningar i segment. Använd det här alternativet för stora data inläsningar. 
* **Aktivera mellanlagring**: Använd PolyBase när du läser in Azure Data Warehouse som din Sink-datauppsättning.

![Fliken Inställningar, som visar alternativ för SQL-mottagare](media/data-flow/alter-row2.png "SQL-alternativ")

> [!NOTE]
> I data flöde kan du direkt Data Factory skapa en ny tabell definition i mål databasen. Skapa tabell definitionen genom att ange en data uppsättning i Sink-omvandlingen som har ett nytt tabell namn. I SQL-datauppsättningen, under tabell namnet, väljer du **Redigera** och anger ett nytt tabell namn. I omvandlingen för mottagare aktiverar du sedan **Tillåt schema avvikelse**. Ange **import schema** till **ingen**.

![Inställningar för SQL-datauppsättning, som visar var du redigerar tabell namnet](media/data-flow/dataset2.png "SQL-schema")

> [!NOTE]
> När du uppdaterar eller tar bort rader i din databas mottagare måste du ange nyckel kolumnen. Med den här inställningen kan Alter-Row-omvandlingen bestämma den unika raden i data flyttnings biblioteket (DML).

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ditt data flöde lägger du till en [data flödes aktivitet i din pipeline](concepts-data-flow-overview.md).
