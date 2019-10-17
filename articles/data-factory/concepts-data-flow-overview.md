---
title: Mappa data flöden i Azure Data Factory | Microsoft Docs
description: En översikt över att mappa data flöden i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 37fec388acda78f3d13c8e85ddddf780ad099d69
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388004"
---
# <a name="what-are-mapping-data-flows"></a>Vad är det för att mappa data flöden?

Mappning av data flöden är visuellt utformad med data transformationer i Azure Data Factory. Data flöden gör det möjligt för data tekniker att utveckla grafisk data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i Azure Data Factory pipelines med utskalade Spark-kluster. Data flödes aktiviteter kan användas via befintliga Data Factory schemaläggning, styrning, flödes-och övervaknings funktioner.

Genom att mappa data flöden får du en helt visuell upplevelse utan att behöva koda. Dina data flöden körs i ditt eget körnings kluster för att skala ut data bearbetningen. Azure Data Factory hanterar all kod översättning, Sök vägs optimering och körning av dina data flödes jobb.

## <a name="getting-started"></a>Komma igång

Klicka på plus tecknet under fabriks resurser för att skapa ett data flöde. 

![nytt data flöde](media/data-flow/newdataflow2.png "nytt data flöde")

Detta tar dig till data flödes arbets ytan där du kan skapa din omvandlings logik. Klicka på rutan Lägg till källa för att börja konfigurera din käll omvandling. Mer information finns i [käll omvandling](data-flow-source.md).

## <a name="data-flow-canvas"></a>Data flödes arbets yta

Data flödes arbets ytan är uppdelad i tre delar: det översta fältet, grafen och konfigurations panelen. 

![Rityta](media/data-flow/canvas1.png "Rityta")

### <a name="graph"></a>Graph

I diagrammet visas omvandlings strömmen. Den visar härkomst för källdata när den flödar till en eller flera handfat. Om du vill lägga till en ny källa klickar du på rutan Lägg till källa. Lägg till en ny omvandling genom att klicka på plus tecknet längst ned till höger i en befintlig omvandling.

![Rityta](media/data-flow/canvas2.png "Rityta")

### <a name="configuration-panel"></a>Konfigurations panel

Konfigurations panelen visar de inställningar som är kopplade till den valda omvandlingen eller, om ingen omvandling har valts, data flödet. I den övergripande data flödes konfigurationen kan du redigera namnet och beskrivningen under fliken **Allmänt** eller lägga till parametrar via fliken **parametrar** . Mer information finns i [mappa data flödes parametrar](parameters-data-flow.md).

Varje omvandling har minst fyra konfigurations flikar:

#### <a name="transformation-settings"></a>Omvandlings inställningar

Den första fliken i varje omvandlings konfigurations fönster innehåller inställningar som är speciella för omvandlingen. Mer information finns på den omvandlingens dokumentations sida.

![Fliken käll inställningar](media/data-flow/source1.png "Fliken käll inställningar")

#### <a name="optimize"></a>Optimera

Fliken _Optimize_ innehåller inställningar för att konfigurera partitionerings scheman.

![Optimize](media/data-flow/optimize1.png "Optimera") (Optimera)

Standardvärdet är "Använd aktuell partitionering", som instruerar Azure Data Factory att använda partitionerings schema som är inbyggt i data flöden som körs på Spark. I de flesta fall är den här inställningen den rekommenderade metoden.

Det finns instanser där du kanske vill justera partitionering. Om du t. ex. vill mata ut dina omvandlingar till en enda fil i sjön väljer du "enskild partition" i en Sink-omvandling.

Ett annat fall där du kanske vill kontrol lera partitionerings scheman är optimering av prestanda. Genom att justera partitionering får du kontroll över distributionen av dina data över Compute-noder och optimeringar av data lokaler som kan ha både positiva och negativa effekter på dina övergripande data flödes prestanda. Mer information finns i [prestanda guiden för data flöde](concepts-data-flow-performance.md).

Om du vill ändra partitionering för en omvandling klickar du på fliken optimera och väljer alternativ knappen Ange partitionering. Sedan visas en serie alternativ för partitionering. Den bästa metoden för partitionering varierar beroende på dina data volymer, kandidat nycklar, null-värden och kardinalitet. Vi rekommenderar att du börjar med standardpartitionering och sedan provar olika partitionerings alternativ. Du kan testa att använda fel söknings körningar för pipelinen och Visa körnings tid och partition användning i varje omvandlings grupp från vyn övervakning. Mer information finns i [övervaka data flöden](concepts-data-flow-monitoring.md).

Nedan visas tillgängliga partitionerings alternativ.

##### <a name="round-robin"></a>Resursallokering 

Resursallokering är en enkel partition som automatiskt distribuerar data jämnt mellan partitioner. Använd Round Robin när du inte har goda viktiga kandidater för att implementera en solid, smart partitionerings strategi. Du kan ange antalet fysiska partitioner.

##### <a name="hash"></a>Hash

Azure Data Factory kommer att skapa en hash av kolumner för att skapa enhetliga partitioner, så att rader med liknande värden hamnar i samma partition. När du använder hash-alternativet kan du testa om det finns en sned partition. Du kan ange antalet fysiska partitioner.

##### <a name="dynamic-range"></a>Dynamiskt intervall

I dynamiskt intervall används Spark-dynamiska intervall baserat på de kolumner eller uttryck som du anger. Du kan ange antalet fysiska partitioner. 

##### <a name="fixed-range"></a>Fast intervall

Bygg ett uttryck som ger ett fast intervall för värden i dina partitionerade data kolumner. Du bör ha en god förståelse för dina data innan du använder det här alternativet för att undvika att skeva partitionen. Värdena som du anger för uttrycket kommer att användas som en del av en partitions funktion. Du kan ange antalet fysiska partitioner.

##### <a name="key"></a>Nyckel

Om du har en god förståelse av kardinalitet för dina data kan nyckel partitionering vara en strategi för lämplig partitionering. Med nyckel partitionering skapas partitioner för varje unikt värde i kolumnen. Du kan inte ange antalet partitioner eftersom antalet baseras på unika värden i data.

#### <a name="inspect"></a>Allmänt

Fliken _Granska_ visar metadata för den data ström som du transformerar. Du kan se kolumn antal, kolumner ändrade, tillagda kolumner, data typer, kolumn ordning och kolumn referenser. Granska är en skrivskyddad vy av dina metadata. Du behöver inte ha aktiverat fel söknings läge för att se metadata i rutan granska.

![Allmänt](media/data-flow/inspect1.png "Allmänt")

När du ändrar formen på dina data med hjälp av omvandlingar ser du att metadata-ändringarna flödar genom fönstret Granska. Om det inte finns ett definierat schema i din käll omvandling visas inte metadata i rutan granska. Brist på metadata är vanligt i schema avvikelse scenarier.

#### <a name="data-preview"></a>Data för hands version

Om fel söknings läget är på visar fliken _data förhands granskning_ en interaktiv ögonblicks bild av data vid varje transformering. Mer information finns [i förhands granskning av data i fel söknings läge](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Översta fältet

Det översta fältet innehåller åtgärder som påverkar hela data flödet, till exempel spara och verifiera. Du kan också växla mellan diagram-och konfigurations lägen med knapparna **Visa graf** och **Dölj diagram** .

![Dölj diagram](media/data-flow/hideg.png "Dölj diagram")

Om du döljer grafen kan du gå igenom dina transformationsfiler senare via knapparna **föregående** och **Nästa** .

![Sökning](media/data-flow/showhide.png "Sökning")

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en [käll omvandling](data-flow-source.md)
* Lär dig hur du skapar dina data flöden i [fel söknings läge](concepts-data-flow-debug-mode.md)
