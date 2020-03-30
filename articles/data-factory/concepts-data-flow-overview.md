---
title: Mappa dataflöden
description: En översikt över mappning av dataflöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 210c1814325e689dd70af9caa7fad08deed933e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243801"
---
# <a name="what-are-mapping-data-flows"></a>Vad är Mappa dataflöden?

Mappning av dataflöden är visuellt utformade dataomvandlingar i Azure Data Factory. Dataflöden gör det möjligt för datatekniker att utveckla grafisk dataomvandlingslogik utan att skriva kod. De resulterande dataflödena körs som aktiviteter i Azure Data Factory-pipelines som använder utskalade Spark-kluster. Dataflödesaktiviteter kan användas via befintliga funktioner för schemaläggning, kontroll, flöde och övervakning av datafabriken.

Mappning av dataflöden ger en helt visuell upplevelse utan kodning krävs. Dina dataflöden körs på ditt eget körningskluster för utskalning av databearbetning. Azure Data Factory hanterar alla kodöversättning, sökvägsoptimering och körning av dina dataflödesjobb.

## <a name="getting-started"></a>Komma igång

Om du vill skapa ett dataflöde markerar du plustecknet under **Fabriksresurser**och väljer sedan **Dataflöde**. 

![Nytt dataflöde](media/data-flow/newdataflow2.png "nytt dataflöde")

Detta tar dig till dataflödesarbetsytan där du kan skapa din omvandlingslogik. Välj **Lägg till källa** om du vill börja konfigurera källomvandlingen. Mer information finns i [Källomvandling](data-flow-source.md).

## <a name="data-flow-canvas"></a>Arbetsyta för dataflöde

Dataflödesarbetsytan är uppdelad i tre delar: det övre fältet, diagrammet och konfigurationspanelen. 

![Arbetsyta](media/data-flow/canvas1.png "Arbetsyta")

### <a name="graph"></a>Graph

Diagrammet visar omformningsströmmen. Den visar källdatas härstamning när den flödar in i en eller flera sänkor. Om du vill lägga till en ny källa väljer du **Lägg till källa**. Om du vill lägga till en ny omformning väljer du plustecknet längst ned till höger i en befintlig omvandling.

![Arbetsyta](media/data-flow/canvas2.png "Arbetsyta")

### <a name="azure-integration-runtime-data-flow-properties"></a>Azure-integreringskörningsdataflödesegenskaper

![Knappen Felsökning](media/data-flow/debugbutton.png "Knappen Felsökning")

När du börjar arbeta med dataflöden i ADF vill du aktivera växeln "Felsökning" för dataflöden högst upp i webbläsargränssnittet. Detta kommer att snurra upp ett Azure Databricks-kluster som ska användas för interaktiv felsökning, dataförhandsgranskningar och pipeline-felsökningskörningar. Du kan ange storleken på det kluster som används genom att välja en anpassad [Azure Integration Runtime](concepts-integration-runtime.md). Felsökningssessionen kommer att överleva i upp till 60 minuter efter din senaste dataförhandsgranskning eller senaste felsökningspipelinekörning.

När du använder dina pipelines med dataflödesaktiviteter använder ADF Azure Integration Runtime som är associerad med [aktiviteten](control-flow-execute-data-flow-activity.md) i egenskapen "Kör på".

Standardkörningen för Azure Integration Runtime är ett litet 4-kärnigt nodkluster för en enda arbetare som är avsett att tillåta dig att förhandsgranska data och snabbt köra felsökningspipelpipelsar till minimala kostnader. Ange en större Azure IR-konfiguration om du utför åtgärder mot stora datauppsättningar.

Du kan instruera ADF att underhålla en pool med klusterresurser (VMs) genom att ange en TTL i Azure IR-dataflödesegenskaperna. Detta resulterar i snabbare jobbkörning på efterföljande aktiviteter.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure-integreringskörning och strategier för dataflöde

##### <a name="execute-data-flows-in-parallel"></a>Köra dataflöden parallellt

Om du kör dataflöden i en pipeline parallellt kommer ADF att snurra upp separata Azure Databricks-kluster för varje aktivitetskörning baserat på inställningarna i din Azure Integration Runtime som är kopplad till varje aktivitet. Om du vill utforma parallella körningar i ADF-pipelines lägger du till dataflödesaktiviteter utan prioritetsbegränsningar i användargränssnittet.

Av dessa tre alternativ kommer det här alternativet sannolikt att köras på kortast möjliga tid. Varje parallellt dataflöde körs dock samtidigt på separata kluster, så ordningsföljden av händelser är icke-deterministisk.

Om du kör dina dataflödesaktiviteter parallellt inuti dina pipelines rekommenderar vi att du inte använder TTL. Detta beror på att parallella körningar av dataflöden samtidigt med samma Azure Integration Runtime resulterar i flera varma poolinstanser för din datafabrik.

##### <a name="overload-single-data-flow"></a>Överbelasta enstaka dataflöde

Om du lägger all din logik i ett enda dataflöde körs alla i samma jobbkörningskontext på en enda Spark-klusterinstans.

Det här alternativet kan möjligen vara svårare att följa och felsöka eftersom dina affärsregler och affärslogik kommer att virrvarr tillsammans. Det här alternativet ger inte heller mycket återanvändbarhet.

##### <a name="execute-data-flows-serially"></a>Köra dataflöden seriellt

Om du kör dina dataflödesaktiviteter i seriella i pipelinen och du har angett en TTL på Azure IR-konfigurationen, kommer ADF att återanvända beräkningsresurserna (VMs) vilket resulterar i snabbare efterföljande körningstider. Du får fortfarande en ny Spark-kontext för varje körning.

Av dessa tre alternativ kommer detta sannolikt att ta längst tid att köra end-to-end. Men det ger en ren separation av logiska åtgärder i varje dataflödessteg.

### <a name="configuration-panel"></a>Panelen Konfiguration

Konfigurationspanelen visar de inställningar som är specifika för den valda omvandlingen. Om ingen omvandling är markerad visas dataflödet. I den övergripande konfigurationen för dataflöde kan du redigera namnet och beskrivningen under fliken **Allmänt** eller lägga till parametrar via fliken **Parametrar.** Mer information finns i [Mappa dataflödesparametrar](parameters-data-flow.md).

Varje omvandling har minst fyra konfigurationsflikar.

#### <a name="transformation-settings"></a>Inställningar för omvandling

Den första fliken i varje omvandlings konfigurationsfönster innehåller de inställningar som är specifika för omvandlingen. Mer information finns på den omvandlingens dokumentationssida.

![Fliken Källinställningar](media/data-flow/source1.png "Fliken Källinställningar")

#### <a name="optimize"></a>Optimera

Fliken **Optimera** innehåller inställningar för att konfigurera partitioneringsscheman.

![Optimera](media/data-flow/optimize1.png "Optimera")

Standardinställningen är **Använd aktuell partitionering**, som instruerar Azure Data Factory att använda partitioneringsschemat som är inbyggt i dataflöden som körs på Spark. I de flesta fall rekommenderar vi den här inställningen.

Det finns fall där du kanske vill justera partitioneringen. Om du till exempel vill skriva ut dina omvandlingar till en enda fil i sjön väljer du **En partition** i en sink-omformning.

Ett annat fall där du kanske vill styra partitioneringsscheman är att optimera prestanda. Om du justerar partitioneringen får du kontroll över fördelningen av dina data mellan beräkningsnoder och datalokalitetsoptimeringar som kan ha både positiva och negativa effekter på dina övergripande dataflödesprestanda. Mer information finns i [prestandaguiden för dataflödet](concepts-data-flow-performance.md).

Om du vill ändra partitioneringen på valfri omvandling markerar du fliken **Optimera** och väljer alternativknappen **Ange partitionering.** Du kommer då att presenteras med en rad alternativ för partitionering. Den bästa partitioneringen skiljer sig beroende på dina datavolymer, kandidatnycklar, null-värden och kardinalitet. 

En bra idé är att börja med standardpartitionering och sedan prova olika partitioneringsalternativ. Du kan testa med hjälp av pipeline-felsökningskörningar och visa körningstid och partitionsanvändning i varje omvandlingsgruppering från övervakningsvyn. Mer information finns i [Övervaka dataflöden](concepts-data-flow-monitoring.md).

Följande partitioneringsalternativ är tillgängliga.

##### <a name="round-robin"></a>Rödhake 

Round robin är en enkel partition som automatiskt distribuerar data lika mellan partitioner. Använd round robin när du inte har bra nyckelkandidater för att implementera en solid, smart partitioneringsstrategi. Du kan ange antalet fysiska partitioner.

##### <a name="hash"></a>Hash

Azure Data Factory kommer att producera en hash av kolumner för att producera enhetliga partitioner så att rader med liknande värden kommer att falla i samma partition. När du använder alternativet Hash testar du om det finns en eventuell partitionssnedställning. Du kan ange antalet fysiska partitioner.

##### <a name="dynamic-range"></a>Dynamiskt omfång

Dynamiskt omfång använder Spark dynamiska intervall baserat på de kolumner eller uttryck som du anger. Du kan ange antalet fysiska partitioner. 

##### <a name="fixed-range"></a>Fast räckvidd

Skapa ett uttryck som ger ett fast intervall för värden i dina partitionerade datakolumner. För att undvika partitionssnedställning bör du ha en god förståelse för dina data innan du använder det här alternativet. De värden som du anger för uttrycket används som en del av en partitionsfunktion. Du kan ange antalet fysiska partitioner.

##### <a name="key"></a>Nyckel

Om du har en god förståelse för kardinaliteten av dina data, kan nyckelpartitionering vara en bra strategi. Nyckelpartitionering skapar partitioner för varje unikt värde i kolumnen. Du kan inte ange antalet partitioner eftersom antalet ska baseras på unika värden i data.

#### <a name="inspect"></a>Inspektera

På fliken **Granska** visas metadata för dataströmmen som du omvandlar. Du kan se kolumnantal, ändrade kolumner, tillagda kolumner, datatyper, kolumnordning och kolumnreferenser. **Inspect** är en skrivskyddad vy av dina metadata. Du behöver inte ha felsökningsläge aktiverat för att se metadata i fönstret **Inspektera.**

![Inspektera](media/data-flow/inspect1.png "Inspektera")

När du ändrar formen på dina data genom omvandlingar visas flödet för metadataändringar i fönstret **Inspektera.** Om det inte finns ett definierat schema i källomvandlingen visas inte metadata i fönstret **Granska.** Brist på metadata är vanligt i schemadrift scenarier.

#### <a name="data-preview"></a>Förhandsgranskning

Om felsökningsläget är aktiverat ger fliken **Förhandsgranskning** av data dig en interaktiv ögonblicksbild av data vid varje transformering. Mer information finns [i Förhandsgranskning av data i felsökningsläge](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Översta fältet

Det övre fältet innehåller åtgärder som påverkar hela dataflödet, till exempel spara och validera. Du kan också växla mellan diagram- och konfigurationslägen med hjälp av knapparna **Visa diagram** och **Dölj diagram.**

![Dölj diagram](media/data-flow/hideg.png "Dölj diagram")

Om du döljer diagrammet kan du bläddra igenom dina omvandlingsnoder i sidled via knapparna **Föregående** och **Nästa.**

![Föregående och nästa knappar](media/data-flow/showhide.png "föregående och nästa knappar")

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [källomvandling](data-flow-source.md).
* Lär dig hur du skapar dataflöden i [felsökningsläge](concepts-data-flow-debug-mode.md).
