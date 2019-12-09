---
title: Konfigurera en Sink-omvandling i mappnings data flödet
description: Lär dig hur du konfigurerar en Sink-omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/03/2019
ms.openlocfilehash: 828487aba651d10e5c906050dab544c097b49762
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930277"
---
# <a name="sink-transformation-for-a-data-flow"></a>Sink-transformering för ett data flöde

När du har transformerat ditt data flöde kan du sinka data till en mål data uppsättning. I omvandling för mottagare väljer du en data uppsättnings definition för målets utgående data. Du kan ha så många handfat som dina data flöden kräver.

För att kunna beakta schema avvikelser och ändringar i inkommande data, ska du skicka ut utdata till en mapp utan ett definierat schema i data uppsättningen för utdata. Du kan också konto för kolumn ändringar i dina källor genom att välja **Tillåt schema avvikelse** i källan. Sedan kan du Automap alla fält i mottagaren.

![Alternativen på fliken mottagare, inklusive alternativet automatisk mappning](media/data-flow/sink1.png "mottagare 1")

Om du vill ta Sink alla inkommande fält aktiverar du **Auto Map**. Om du vill välja vilka fält som ska översättas till målet, eller om du vill ändra namnen på fälten vid målet, så Stäng av **automatisk mappning**. Öppna sedan fliken **mappning** för att mappa utmatnings fält.

![Alternativ på fliken mappning](media/data-flow/sink2.png "mottagare 2")

## <a name="output"></a>Resultat 
För Azure Blob Storage-eller Data Lake Storage Sink-typer skriver du ut transformerade data till en mapp. Spark genererar partitionerade utdatafiler baserat på det partitionerings schema som används av Sink-omvandlingen. 

Du kan ställa in partitionerings schema från fliken **Optimize (optimera** ). Om du vill att Data Factory sammanfoga dina utdata till en enda fil väljer du **enskild partition**. Om du vill underhålla eller skapa partitionerade mappar använder du **nyckel partitionering** och anger de nycklar som du vill använda för partitionerade mappstrukturer.

![Alternativ på fliken optimera](media/data-flow/opt001.png "mottagar alternativ")

## <a name="field-mapping"></a>Fältmappning
På fliken **mappning** i din Sink-omvandling kan du mappa de inkommande kolumnerna till vänster till målen till höger. När du tar Sink-data flöden till filer skriver Data Factory alltid nya filer till en mapp. När du mappar till en databas data uppsättning väljer du åtgärds alternativ för databas tabell för att infoga, uppdatera, upsert eller ta bort.

![Fliken mappning](media/data-flow/sink2.png "Mottagare")

I mappnings tabellen kan du välja att länka flera kolumner, ta bort flera kolumner eller mappa flera rader till samma kolumn namn.

Om du alltid vill mappa inkommande uppsättning fält till ett mål som de är och för att fullständigt godkänna flexibla schema definitioner väljer du **Tillåt schema avvikelse**.

![Fliken mappning, som visar fält som har mappats till kolumner i data uppsättningen](media/data-flow/multi1.png "flera alternativ")

Om du vill återställa kolumn mappningarna väljer du **åter mappning**.

![Fliken mottagare](media/data-flow/sink1.png "Mottagar en")

Välj **Verifiera schema** om du vill stoppa sinken om schemat ändras.

Välj **Rensa mappen** för att trunkera innehållet i mappen Sink innan du skriver målfiler i den målmappen.

## <a name="fixed-mapping-vs-rule-based-mapping"></a>Fast mappning jämfört med regel-baserad mappning
När du inaktiverar automatisk mappning har du möjlighet att lägga till en kolumn-baserad mappning (fast mappning) eller regelbaserade mappning. Med hjälp av regelbaserade mappningar kan du skriva uttryck med mönster matchning medan fast mappning mappar logiska och fysiska kolumn namn.

![Regel baserad mappning](media/data-flow/rules4.png "Regel baserad mappning")

När du väljer regelbaserade mappning instruerar du ADF att utvärdera matchnings uttrycket så att det matchar inkommande mönster regler och definierar de utgående fält namnen. Du kan lägga till valfri kombination av både fält-och regelbaserade mappningar. Fält namn genereras sedan vid körning med ADF baserat på inkommande metadata från källan. Du kan visa namnen på de genererade fälten under fel sökning och i fönstret data förhands granskning.

Information om mönster matchning finns i [kolumn mönster dokumentation](concepts-data-flow-column-pattern.md).

Du kan också ange mönster för reguljära uttryck när du använder regel baserad matchning genom att expandera raden och ange ett reguljärt uttryck bredvid "namn matchningar:".

![Regex-mappning](media/data-flow/scdt1g4.png "Regex-mappning")

Ett vanligt vanligt exempel för en regel baserad mappning jämfört med fast mappning är det fall där du vill mappa alla inkommande fält till samma namn i målet. Om det finns fasta mappningar, visar du varje enskild kolumn i tabellen. För regelbaserade mappningar skulle du ha en enda regel som mappar alla fält med ```true()``` till samma namn på inkommande fält som representeras av ```$$```.

### <a name="sink-association-with-dataset"></a>Sink-Association med data uppsättning

Den data uppsättning som du väljer för din mottagare kan ha ett schema som definierats i data uppsättnings definitionen. Om det inte finns något definierat schema måste du tillåta schema drift. När du definierade en fast mappning behålls mappningen mellan logiska och fysiska namn i Sink-omvandlingen. Om du ändrar schema definitionen för data uppsättningen kan du eventuellt dela upp Sink-mappningen. Undvik detta genom att använda regel-baserad mappning. Regelbaserade mappningar är generaliserade, vilket innebär att schema ändringar i din data uppsättning inte bryter mappningen.

## <a name="file-name-options"></a>Alternativ för fil namn

Konfigurera fil namn: 

   * **Standard**: Tillåt Spark att namnge filer baserat på del standarder.
   * **Mönster**: Ange ett mönster för utdatafilerna. Till exempel kommer **lån [n]** att skapa loans1. csv, loans2. csv och så vidare.
   * **Per partition**: Ange ett fil namn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn.
   * **Utdata till en enda fil**: med det här alternativet kombinerar ADF de partitionerade utdatafilerna till en enda namngiven fil. Om du vill använda det här alternativet bör data uppsättningen matcha till ett mappnamn. Tänk också på att den här sammanslagnings åtgärden kan sluta fungera baserat på Node-storlek.

> [!NOTE]
> Fil åtgärder startar bara när du kör aktiviteten kör data flöde. De startar inte i fel söknings läge för data flöde.

## <a name="database-options"></a>Databas alternativ

Välj databas inställningar:

![Fliken Inställningar, som visar alternativ för SQL-mottagare](media/data-flow/alter-row2.png "SQL-alternativ")

* **Uppdaterings metod**: standard är att tillåta infogningar. Rensa **Tillåt infogning** om du vill sluta infoga nya rader från källan. Om du vill uppdatera, upsert eller ta bort rader måste du först lägga till en Alter-Row-omvandling för att tagga rader för dessa åtgärder. 
* **Återskapa tabell**: släpp eller skapa mål tabellen innan data flödet har slutförts.
* **Trunkera tabell**: ta bort alla rader från mål tabellen innan data flödet har slutförts.
* **Batchstorlek**: Ange ett tal för Bucket skrivningar i segment. Använd det här alternativet för stora data inläsningar. 
* **Aktivera mellanlagring**: Använd PolyBase när du läser in Azure Data Warehouse som din Sink-datauppsättning.
* **För-och post-SQL-skript**: Ange SQL-skript med flera rader som ska köras före (för bearbetning) och efter (efter bearbetning)-data skrivs till din mottagar databas

![skript för SQL-bearbetning före och efter bearbetning](media/data-flow/prepost1.png "Skript för SQL-bearbetning")

> [!NOTE]
> I data flöde kan du direkt Data Factory skapa en ny tabell definition i mål databasen. Skapa tabell definitionen genom att ange en data uppsättning i Sink-omvandlingen som har ett nytt tabell namn. I SQL-datauppsättningen, under tabell namnet, väljer du **Redigera** och anger ett nytt tabell namn. I omvandlingen för mottagare aktiverar du sedan **Tillåt schema avvikelse**. Ange **import schema** till **ingen**.

![Inställningar för SQL-datauppsättning, som visar var du redigerar tabell namnet](media/data-flow/dataset2.png "SQL-schema")

> [!NOTE]
> När du uppdaterar eller tar bort rader i din databas mottagare måste du ange nyckel kolumnen. Med den här inställningen kan Alter-Row-omvandlingen bestämma den unika raden i data flyttnings biblioteket (DML).

### <a name="cosmosdb-specific-settings"></a>CosmosDB-inställningar

Vid landnings data i CosmosDB måste du överväga följande ytterligare alternativ:

* Partitionsnyckel: det här fältet är obligatoriskt. Ange en sträng som representerar partitionens partitionsnyckel för din samling. Exempel: ```/movies/title```
* Data flöde: Ange ett valfritt värde för det antal ru: er som du vill använda för din CosmosDB-samling för varje körning av det här data flödet. Minimum är 400.

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ditt data flöde lägger du till en [data flödes aktivitet i din pipeline](concepts-data-flow-overview.md).
