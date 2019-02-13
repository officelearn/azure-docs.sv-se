---
title: Azure Data Factory mappning dataflöde visuell övervakning
description: Visuellt övervaka Azure Data Factory Data flödar
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 1497e6b85d3f577064b7a90fb1bcf5cbeb4a1f40
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213604"
---
# <a name="monitor-data-flows"></a>Övervaka dataflöden

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

När du har slutfört utvecklar och felsöker ditt dataflöde, kommer du att schemalägga dataflödet ska köras enligt ett schema inom ramen för en pipeline. Du kan schemalägga pipelinen från Azure Data Factory med hjälp av utlösare. Du kan också använda alternativet Utlös nu från Azure Data Factory-Pipeline Builder för att köra körning av en enskild och kör om du vill testa ditt dataflöde i pipeline-kontexten.

När du kör din pipeline, kommer du att kunna övervaka pipelinen och alla aktiviteter i pipelinen, inklusive aktiviteten dataflöde. Klicka på ikonen i den vänstra panelen för Azure Data Factory-Användargränssnittet. Visas en skärm som liknar den nedan. Markerade ikoner kan du öka detaljnivån i aktiviteterna i pipelinen, inklusive aktiviteten dataflöde.

<img src="media/data-flow/mon001.png" width="800">

Du kommer att se statistik på den här nivån som väl inculding körtider och status. Kör-ID på aktivitetsnivå är olika körningen ID: T på pipelinenivå. Kör-ID på föregående nivå är för pipelinen. Klicka på glasögon ger dig djupgående information på din körning för flödet av data.

<img src="media/data-flow/mon002.png" width="800">

När du använder grafiska noden övervakningsvyn kan visas en förenklad skrivskyddad version av din graf för flödet av data.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Visa Data Flow körningsplaner

När ditt dataflöde körs i Databricks, anger Azure Data Factory optimala kodsökvägar baserat på entirity av ditt dataflöde. Dessutom kan uppstå sökvägar för körning på olika noder i skalbara och datapartitioner. Därför representerar övervakning diagrammet utformningen av din flöde, med hänsyn till körningssökvägen för dina transformeringar. När du klickar på enskilda noder visas ”grupperingar” som representerar kod som har gjorts tillsammans på klustret. Representerar grupperna till skillnad från de enskilda stegen i din design tidsinställningar och antal som visas.

<img src="media/data-flow/mon004.png" width="800"> 

* När du klickar på det öppna utrymmet i fönstret övervakning statistiken längst ned i fönstret visar tidpunkten och radantalet för varje mottagare och transformeringar som ledde till mottagardata för omvandling härkomst.

* När du väljer enskilda transformationer, får du ytterligare feedback på den högra panelen som visar statistik för partition, kolumnen antal, snedställningar (hur är data jämnt över partitioner), och datamängds (hur spikey är data).

* När du klickar på mottagare i vyn noden visas kolumnen härkomst. Det finns tre olika metoder att kolumner ackumuleras i hela ditt dataflöde hamnar i mottagaren. De är:

  * Beräknad: Du använder kolumnen för villkorsstyrd bearbetning eller i ett uttryck i ditt dataflöde, men hamnar inte den i mottagaren
  * Härledda: Kolumnen är en ny kolumn som du genererade i ditt flöde, dvs. det fanns inte i källan
  * Mappa: Kolumnen har sitt ursprung på käll- och din kan mappas till ett fält för mottagare
  
## <a name="monitor-icons"></a>Övervaka ikoner

Den här ikonen anger att Omvandlingsdata redan har cachelagrats på klustret, så tidsinställningar och exekveringsväg har hänsyn som:

<img src="media/data-flow/mon005.png" width="800"> 

Du kan även se grön cirkel ikonerna i transformeringen. De representerar det antal mottagare som data flödar till.
