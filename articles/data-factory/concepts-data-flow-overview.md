---
title: Mappa dataflöden
description: En översikt över att mappa data flöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: 850879675d4554329f24c86f2ac28660b303084c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475574"
---
# <a name="what-are-mapping-data-flows"></a>Vad är Mappa dataflöden?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mappning av data flöden är visuellt utformad med data transformationer i Azure Data Factory. Data flöden gör det möjligt för data tekniker att utveckla grafisk data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i Azure Data Factory pipelines som använder utskalade Apache Spark kluster. Data flödes aktiviteter kan användas via befintliga Data Factory schemaläggning, kontroll, flöde och övervakning.

Genom att mappa data flöden får du en helt visuell upplevelse utan kodning som krävs. Dina data flöden körs i ditt körnings kluster för att skala ut data bearbetningen. Azure Data Factory hanterar all kod översättning, Sök vägs optimering och körning av dina data flödes jobb.

![Arkitektur](media/data-flow/adf-data-flows.png "Arkitektur")

## <a name="getting-started"></a>Komma igång

Om du vill skapa ett data flöde väljer du plus tecknet under **fabriks resurser**och väljer sedan **data flöde**. 

![Nytt data flöde](media/data-flow/newdataflow2.png "nytt data flöde")

Den här åtgärden tar dig till data flödets arbets yta där du kan skapa din omvandlings logik. Välj **Lägg till källa** för att börja konfigurera din käll omvandling. Mer information finns i [käll omvandling](data-flow-source.md).

## <a name="data-flow-canvas"></a>Data flödes arbets yta

Data flödes arbets ytan är uppdelad i tre delar: det översta fältet, grafen och konfigurations panelen. 

![Arbetsyta](media/data-flow/canvas1.png "Arbetsyta")

### <a name="graph"></a>Graph

I diagrammet visas omvandlings strömmen. Den visar härkomst för källdata när den flödar till en eller flera handfat. Om du vill lägga till en ny källa väljer du **Lägg till källa**. Om du vill lägga till en ny omvandling väljer du plus tecknet längst ned till höger i en befintlig omvandling.

![Arbetsyta](media/data-flow/canvas2.png "Arbetsyta")

### <a name="azure-integration-runtime-data-flow-properties"></a>Data flödes egenskaper för Azure integration runtime

![Knappen Felsök](media/data-flow/debugbutton.png "Knappen Felsök")

När du börjar arbeta med data flöden i ADF vill du aktivera "Felsök"-växeln för data flöden överst i webb läsar gränssnittet. Detta gör att ett Spark-kluster kan användas för interaktiva fel sökning, för hands versionerna av data och pipeline-felsökning. Du kan ange storleken på det kluster som används genom att välja en anpassad [Azure integration runtime](concepts-integration-runtime.md). Felsöknings sessionen är aktiv i upp till 60 minuter efter din senaste data förhands granskning eller senaste fel söknings pipeline-körning.

När du operationalisera dina pipelines med data flödes aktiviteter använder ADF den Azure Integration Runtime som är kopplad till [aktiviteten](control-flow-execute-data-flow-activity.md) i egenskapen kör på.

Standard Azure Integration Runtime är ett litet 4-core-kluster med en arbets nod som gör att du kan förhandsgranska data och snabbt köra fel söknings pipeliner vid minimala kostnader. Ange en större Azure IR konfiguration om du utför åtgärder mot stora data mängder.

Du kan instruera ADF att underhålla en pool med kluster resurser (VM) genom att ange ett TTL-värde i egenskaperna för Azure IR data flödet. Den här åtgärden resulterar i snabbare jobb körningar på efterföljande aktiviteter.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure integration Runtime och data flödes strategier

##### <a name="execute-data-flows-in-parallel"></a>Kör data flöden parallellt

Om du kör data flöden i en pipeline parallellt, ökar ADF upp separata Spark-kluster för varje aktivitets körning baserat på inställningarna i Azure Integration Runtime som är kopplade till varje aktivitet. Om du vill utforma parallella körningar i ADF-pipeline lägger du till data flödes aktiviteter utan prioritets begränsningar i användar gränssnittet.

Av dessa tre alternativ körs det här alternativet förmodligen på kortast möjliga tid. Alla parallella data flöden körs dock samtidigt på separata kluster, så sortering av händelser är icke-deterministisk.

Om du kör dina data flödes aktiviteter parallellt i dina pipelines bör du inte använda TTL. Den här åtgärden beror på att parallella körningar av ditt data flöde samtidigt använder samma Azure Integration Runtime resulterar i flera instansen av en varm pool för din data fabrik.

##### <a name="overload-single-data-flow"></a>Överlagring av ett enskilt data flöde

Om du har lagt till all din logik i ett enda data flöde kör ADF samma jobb körnings kontext på en enda Spark kluster instans.

Det här alternativet kan vara mer utmanande att följa och felsöka eftersom affärs regler och affärs logik kan jumbled tillsammans. Det här alternativet ger inte heller mycket åter användning.

##### <a name="execute-data-flows-sequentially"></a>Köra data flöden sekventiellt

Om du kör dina data flödes aktiviteter i följd i pipelinen och du har angett ett TTL-värde för Azure IR konfigurationen återanvänds beräknings resurserna (VM), vilket resulterar i snabbare efterföljande körnings tider. Du får fortfarande en ny Spark-kontext för varje körning.

Av de här tre alternativen tar den här åtgärden förmodligen den längsta tiden att köra slut punkt till slut punkt. Men det ger en ren uppdelning av logiska åtgärder i varje data flödes steg.

### <a name="configuration-panel"></a>Konfigurations panel

Konfigurations panelen visar inställningarna för den aktuella valda omvandlingen. Om ingen omvandling väljs visas data flödet. I den övergripande data flödes konfigurationen kan du redigera namnet och beskrivningen under fliken **Allmänt** eller lägga till parametrar via fliken **parametrar** . Mer information finns i [mappa data flödes parametrar](parameters-data-flow.md).

Varje omvandling innehåller minst fyra konfigurations flikar.

#### <a name="transformation-settings"></a>Omvandlings inställningar

Den första fliken i varje omvandlings konfigurations fönster innehåller inställningar som är speciella för omvandlingen. Mer information finns på dokument sidan för omvandling.

![Fliken käll inställningar](media/data-flow/source1.png "Fliken käll inställningar")

#### <a name="optimize"></a>Optimera

Fliken **Optimize** innehåller inställningar för att konfigurera partitionerings scheman. Mer information om hur du optimerar dina data flöden finns i [prestanda guiden för att mappa data flödet](concepts-data-flow-performance.md).

![Optimera](media/data-flow/optimize.png "Optimera")

#### <a name="inspect"></a>Allmänt

Fliken **Granska** visar metadata för den data ström som du transformerar. Du kan se kolumn antal, kolumnerna har ändrats, kolumnerna tillagda, data typer, kolumn ordning och kolumn referenser. **Granska** är en skrivskyddad vy av dina metadata. Du behöver inte ha aktiverat fel söknings läge för att se metadata i rutan **Granska** .

![Allmänt](media/data-flow/inspect1.png "Allmänt")

När du ändrar formen på dina data med omvandlingar visas flödet ändringar i metadata i rutan **Granska** . Om det inte finns ett definierat schema i din käll omvandling visas inte metadata i rutan **Granska** . Brist på metadata är vanligt i schema avvikelse scenarier.

#### <a name="data-preview"></a>Förhandsgranskning

Om fel söknings läget är på visar fliken **data förhands granskning** en interaktiv ögonblicks bild av data vid varje transformering. Mer information finns [i förhands granskning av data i fel söknings läge](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Översta fältet

Det översta fältet innehåller åtgärder som påverkar hela data flödet, t. ex. Spara och verifiera. Du kan också växla mellan diagram-och konfigurations lägen med knapparna **Visa graf** och **Dölj diagram** .

![Dölj diagram](media/data-flow/hideg.png "Dölj diagram")

Om du döljer grafen kan du bläddra igenom dina transformationsfiler senare via knapparna **föregående** och **Nästa** .

![Knapparna föregående och nästa](media/data-flow/showhide.png "knapparna föregående och nästa")

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [käll omvandling](data-flow-source.md).
* Lär dig hur du skapar dina data flöden i [fel söknings läge](concepts-data-flow-debug-mode.md).
